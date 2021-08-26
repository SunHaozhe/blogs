---
layout: post
category: Python     
title: PyTorch memo 
tagline: by SunHaozhe
tags: 
  - memo
  - PyTorch
  - Python
mathjax: true
comments: true
published: true
---

# Imports

```python
import torch 
import torch.nn as nn
import torch.nn.functional as F  
```

# Image format convention


In PyTorch, images are represented as [channels, height, width]. During the training you will get batches of images, so your shape in the forward method will get an additional batch dimension at `dim0`: [batch size, channels, height, width], also called `NCHW`.

```python
x = x.permute(0, 3, 1, 2) # from NHWC to NCHW 
```

# Creation & initialization 

In PyTorch `1.8.1`, `torch.Tensor` is a class (`<class 'torch.Tensor'>`), `torch.tensor` is a function:

According to [https://pytorch.org/docs/stable/tensors.html#torch.Tensor](https://pytorch.org/docs/stable/tensors.html#torch.Tensor):

* To create a tensor with pre-existing data, use `torch.tensor()`
* To create a tensor with specific size, use `torch.*` tensor creation ops (see [Creation Ops](https://pytorch.org/docs/stable/torch.html#tensor-creation-ops))
* To create a tensor with the same size (and similar types) as another tensor, use `torch.*_like` tensor creation ops (see [Creation Ops](https://pytorch.org/docs/stable/torch.html#tensor-creation-ops))
* To create a tensor with similar type but different size as another tensor, use `tensor.new_*` creation ops 

```python
torch.tensor([[0.1, 1.2], [2.2, 3.1], [4.9, 5.2]])
```

```
tensor([[ 0.1000,  1.2000],
        [ 2.2000,  3.1000],
        [ 4.9000,  5.2000]])
```

```python
# the input can be a variable number of 
# integers or a collection-like a list or tuple
torch.zeros(2, 3)
```

```
tensor([[ 0.,  0.,  0.],
        [ 0.,  0.,  0.]])
```

However, it is also possible to use the constructor of `torch.Tensor` even if this is not documented. The following is an example. The generated `torch.Tensor` instance contains some small random values, sometimes many 0s. It is not clear to me how it has been initialized, my guess is that the initial weights are just random values induced from uninitialized memory blocks. If this guess is correct, this way of creating `torch.Tensor` objects should be followed by a initialization phase such as Kaiming uniform, etc. 

```python
torch.__version__ # 1.8.1 

a = torch.Tensor(1, 2, 3, 4, 5)
a.shape  # torch.Size([1, 2, 3, 4, 5])
a.size() # torch.Size([1, 2, 3, 4, 5]) 
a.dtype  # torch.float32 
```



Returns a `torch.Tensor` object filled with random numbers from a normal distribution with mean 0 and variance 1 (also called the standard normal distribution)

```python
# By default, requires_grad == False 
torch.randn(2, 3)
# or equivalently
torch.randn((2, 3))
```

```
tensor([[ 1.5954,  2.8929, -1.0923],
        [ 1.1719, -0.4709, -0.1996]])
```

Please note the difference between `torch.Tensor` and `torch.nn.parameter.Parameter` (`nn.Parameter(torch.randn(2, 3))`, by default `requires_grad == True`).


### Converting numpy Array to torch Tensor 

The created tensor and numpy `ndarray` **share the same memory**. Modifications to the tensor will be reflected in the ndarray and vice versa. The returned tensor is not resizable. 

```python
a = numpy.array([1, 2, 3])
t = torch.from_numpy(a)
t
```

```
tensor([ 1,  2,  3])
```

### Converting torch Tensor to numpy Array

The tensor and the returned numpy `ndarray` **share the same underlying storage**. Changing one will change the other.

It has been firmly established that we should call `.detach()` before calling `.numpy()` (`xxx.detach().numpy()`). The logic is that `np.ndarray` does not store/represent the computational graph associated with the array, this graph should be explicitly removed using `detach()`. 


```python
a = torch.ones(5) # tensor([1., 1., 1., 1., 1.])
b = a.numpy()     # [1. 1. 1. 1. 1.] 
```

```python
# one example 
b = model(torch.from_numpy(a)) 
c = b.cpu().detach().numpy()
d = b.detach().cpu().numpy()
```

# Shape manipulation 

### transpose()

`torch.transpose(input, dim0, dim1) → Tensor` returns a tensor that is a transposed version of `input`. The given dimensions `dim0` and `dim1` are swapped. The resulting `out` tensor **shares its underlying storage** with the `input` tensor, so changing the content of one would change the content of the other.

```python
a = torch.randn(1, 2, 3, 4) # torch.Size([1, 2, 3, 4])

# Swaps 2nd and 3rd dimension
b = a.transpose(1, 2)       # torch.Size([1, 3, 2, 4])
```

`transpose_()` is the in-place version of `transpose()`.

`T` returns this Tensor with its dimensions reversed. If `n` is the number of dimensions in `x`, `x.T` is equivalent to `x.permute(n-1, n-2, ..., 0)`.


### contiguous()

[https://stackoverflow.com/a/52229694/7636942](https://stackoverflow.com/a/52229694/7636942)

There are few operations on Tensor in PyTorch that do not really change the content of the tensor, but only how to convert indices in to tensor to byte location. These operations include: `narrow()`, `view()`, `expand()` and `transpose()`. For example: when you call `transpose()`, PyTorch doesn't generate new tensor with new layout, it just modifies meta information in Tensor object so offset and stride are for new shape. The transposed tensor and original tensor are indeed sharing the memory!

The word "contiguous" is bit misleading because its not that the content of tensor is spread out around disconnected blocks of memory. Here bytes are still allocated in one block of memory but the order of the elements is not "contiguous". When you call `contiguous()`, it actually makes a copy of tensor so the order of elements would be same as if tensor of same shape created from scratch. Normally you don't need to worry about this. If PyTorch expects contiguous tensor but if its not then you will get `RuntimeError: input is not contiguous` and then you just add a call to `contiguous()`. If self tensor is already contiguous, this function returns the self tensor. 


### view()

`view(*shape) → Tensor` returns a new tensor with the same data as the `self` tensor but of a different `shape`. The returned tensor **shares the same data** and must have the same number of elements, but may have a different size. For a tensor to be viewed, the new view size must be compatible with its original size and stride, i.e., each new view dimension must either be a subspace of an original dimension, or only span across original dimensions that satisfy some contiguity-like condition (see [https://pytorch.org/docs/stable/tensors.html#torch.Tensor.view](https://pytorch.org/docs/stable/tensors.html#torch.Tensor.view)). Otherwise, it will not be possible to view `self` tensor as `shape` without copying it (e.g., via `contiguous()`). When it is unclear whether a `view()` can be performed, it is advisable to use `reshape()`, which returns a view if the shapes are compatible, and copies (equivalent to calling `contiguous()`) otherwise.

```python
x = torch.randn(4, 4) # torch.Size([4, 4])

y = x.view(16)        # torch.Size([16])

# the size -1 is inferred from other dimensions
z1 = x.view(-1, 8)     # torch.Size([2, 8]) 
z2 = x.view((-1, 8)     # torch.Size([2, 8]) 
```

### reshape()

When possible, the returned tensor will be a view of `input`. Otherwise, it will be a copy. Contiguous inputs and inputs with compatible strides can be reshaped without copying, but you should not depend on the copying vs. viewing behavior.

It means that `torch.reshape()` may return a copy or a view of the original tensor. You can not count on that to return a view or a copy. According to the developer, if you need a copy use `clone()` if you need the same storage use `view()`. The semantics of `reshape()` are that it may or may not share the storage and you don’t know beforehand. 

```python
a = torch.randn(2, 3, 4) # torch.Size([2, 3, 4])
b = a.reshape(3, -1)     # torch.Size([3, 8])
c = a.reshape((3, -1))   # torch.Size([3, 8])
```


### Difference between view(), reshape(), transpose(), permute().

[https://jdhao.github.io/2019/07/10/pytorch_view_reshape_transpose_permute/](https://jdhao.github.io/2019/07/10/pytorch_view_reshape_transpose_permute/)

Both `view()` and `transpose()` return a new tensor sharing the data with the original tensor. One difference between them is that `view()` can only operate on contiguous tensor and the returned tensor is still contiguous. `transpose()` can operate both on contiguous and non-contiguous tensor, but the returned tensor may be not contiguous any more.

But what does contiguous mean? Here is a [reference](https://stackoverflow.com/questions/26998223/what-is-the-difference-between-contiguous-and-non-contiguous-arrays/26999092#26999092) which also applies to PyTorch. A lot of tensor operations requires that the tensor should be contiguous, otherwise, an error will be thrown. To make a non-contiguous tensor become contiguous, just use `contiguous()`, which will create a new memory space for the new tensor and copy the value from the non-contiguous tensor to the new tensor. 

```python
x = torch.tensor([[1, 2, 3], [4, 5, 6]]) 
y = x.transpose(0, 1) 

# x and y share the same memory space
print(x.data_ptr())      # 140340426148352
print(y.data_ptr())      # 140340426148352 

print(x.is_contiguous()) # True 
print(y.is_contiguous()) # False
```

`permute()` and `tranpose()` are similar. `transpose()` can only swap two dimension, but `permute()` can swap all the dimensions. Note that, in `permute()`, you must provide the new order of all the dimensions. In `transpose()`, you can only provide two dimensions. `tranpose()` can be thought as a special case of `permute()` method.

```python
x = torch.rand(16, 32, 3) # torch.Size([16, 32, 3])
y = x.transpose(0, 2)     # torch.Size([3, 32, 16])
z = x.permute(2, 1, 0)    # torch.Size([3, 32, 16])
w = x.permute(1, 2, 0)    # torch.Size([32, 3, 16]) 
```

`torch.equal()` returns `True` if two tensors have the same size and elements, `False` otherwise`

```python
x = torch.tensor([1, 2]) # torch.Size([2])
y = torch.tensor([1, 2]) # torch.Size([2]) 

torch.equal(x, y) # True

x.data_ptr()      # 140340397035200
y.data_ptr()      # 140340370049664

#################################################

a = torch.randn(1, 2, 3, 4) # torch.Size([1, 2, 3, 4])
b = a.transpose(1, 2)       # torch.Size([1, 3, 2, 4])
c = a.view(1, 3, 2, 4)      # torch.Size([1, 3, 2, 4])

torch.equal(a, b)    # False
torch.equal(a, c)    # False
torch.equal(b, c)    # False 

a.data_ptr()      # 140340397022336
b.data_ptr()      # 140340397022336
c.data_ptr()      # 140340397022336
```

# detach(), detach_(), clone()

### clone()

`clone()` returns a copy. This function **is differentiable**, so gradients will flow back from the result of this operation to the original tensor object. To create a tensor without an autograd relationship, see `detach()`.

### clone() vs copy.deepcopy()

For Tensors in most cases, you should go for `clone()` since this is a PyTorch operation that will be recorded by autograd. When it comes to `Module`, there is no `clone()` method available so you can either use `copy.deepcopy` or create a new instance of the model and just copy the parameters.

In the following example, both are equivalent, but there might be a (small) speed difference. When you use `.data`, you get a new Tensor with `requires_grad=False`, so cloning it won’t involve autograd. 

```python
x = model.encoder[0].weight.data.clone() 
x = copy.deepcopy(model.encoder[0].weight.data) 
```

The `.data` field is an old field that is kept for backward compatibility but should not be used anymore as it’s usage is dangerous and can make computations wrong. You should use `.detach()` and/or `with torch.no_grad()` instead now.


### detach()

`detach()` returns a new Tensor, detached from the current graph. The result will never require gradient. Returned Tensor **shares the same storage** with the original one. In-place modifications on either of them will be seen, and may trigger errors in correctness checks. 

### detach_()

`detach_()` detaches the Tensor from the graph that created it, making it a leaf. Views cannot be detached in-place. 


# squeeze()/unsqueeze()

### squeeze()

`squeeze()` returns a tensor with all the dimensions of `input` of size `1` removed. The returned tensor **shares the storage** with the `input` tensor, so changing the contents of one will change the contents of the other. 

When `dim` is given, a squeeze operation is done only in the given dimension. If input is of shape: `(A×1×B)`, `squeeze(input, 0)` leaves the tensor unchanged, but `squeeze(input, 1)` will squeeze the tensor to the shape `(A×B)`.

```python
x = torch.zeros(2, 1, 2, 1, 2) # torch.Size([2, 1, 2, 1, 2])
x.squeeze()                    # torch.Size([2, 2, 2])
torch.squeeze(x)               # torch.Size([2, 2, 2]) 
```


### unsqueeze()

`unsqueeze()` returns a new tensor with a dimension of size one inserted at the specified position. The returned tensor **shares the same underlying data** with this tensor. A `dim` value within the range `[-input.dim() - 1, input.dim() + 1)` can be used. Negative `dim` will correspond to `unsqueeze()` applied at `dim = dim + input.dim() + 1`.

```python
x = torch.tensor([1, 2, 3, 4]) # torch.Size([4])

x.unsqueeze(0)        # torch.Size([1, 4])
torch.unsqueeze(x, 0) # torch.Size([1, 4])
```

```
tensor([[1, 2, 3, 4]])
```

```python
x.unsqueeze(1) # torch.Size([4, 1])
```

```
tensor([[1],
        [2],
        [3],
        [4]])
```

# CPU, GPU

```python
if torch.cuda.is_available():
    pass 
```

`cuda()` is used to move a tensor to GPU memory. `cpu()` moves it back to memory accessible to the CPU. `cpu()` or `cuda()` works differently for model and tensor. 

For tensors:

```python
tensor = tensor.cpu()
# or equivalently 
tensor = tensor.to("cpu")

tensor = tensor.cuda()
tensor = tensor.to(torch.device("cuda:0"))
```

Some operations on tensors cannot be performed on cuda tensors. In these cases, you need to move them to cpu first.

For models:

```python
model.cuda() # in-place operation for models 
```

Get current device name: 

```python
torch.cuda.get_device_name(torch.cuda.current_device())
```

The first GPU can be identified by the following string: 

```python
"cuda:0"
```

# Save and load

Using `state_dict` is the recommended way. Python dictionary can easily be pickled, unpickled, updated, and restored. Thus saving model using `state_dict` offers more flexibility. We can also save the optimizer state, hyperparameters, etc., as key-value pairs along with the model's state_dict. The drawback is that we will need the model definition to load the `state_dict`.

It is also possible to save and load the entire model. It is however not a recommended way of doing. This approach saves/loads models with the least amount of code, it is also more intuitive. The drawbacks are:

* Since Python's pickle module is used internally, the serialized data is bound to the specific classes and the exact directory structure is used when the model is saved. Pickle simply saves a path to the file containing the specific class. This is used during load time.
* The code might break after refactoring as the saved model might not link to the same path. Using such a model in another project is hard as well since the path structure needs to be maintained.


### Save 

```python
# recommended
torch.save(model.state_dict(), PATH)
```

```python
# not recommended 
torch.save(model, PATH)
```

### Load 

`PATH` is the path to a `.pth`/`.pt` file. `model.load_state_dict(PATH)` **will not** work. `torch.load(PATH)` returns a `collections.OrderedDict` containing the parameters. 

```python
model = ClassBlaBlaBla(*args, **kwargs)

# recommended
model.load_state_dict(torch.load(PATH))
```

If we save on GPU, load on CPU:

```python
device = torch.device("cpu")
model.load_state_dict(torch.load(PATH, map_location=device))
```

```python
# not recommended
model = torch.load(PATH)
```

# sum

When using `sum()`, if `dim` is given (analogy to `axis` in `numpy`), the sum is only done in the given dimension `dim`. If `dim` is a list of dimensions, reduce over all of them. 

If `keepdim` is `True`, the output tensor is of the same size as `input` except in the dimension(s) `dim` where it is of size `1`. Otherwise, `dim` is squeezed (see `torch.squeeze()`), resulting in the output tensor having 1 (or `len(dim)`) fewer dimension(s). By default, `keepdim=False`.

### einsum

* [https://pytorch.org/docs/stable/generated/torch.einsum.html](https://pytorch.org/docs/stable/generated/torch.einsum.html)
* [https://stackoverflow.com/a/55894780/7636942](https://stackoverflow.com/a/55894780/7636942)

`torch.einsum(equation, *operands) → Tensor` sums the product of the elements of the input operands along dimensions specified using a notation based on the Einstein summation convention. 

For example, matrix multiplication can be computed using einsum as `torch.einsum(“ij,jk->ik”, A, B)`. Here, `j` is the summation subscript and `i` and `k` the output subscripts.


# Tensor unfold()/fold()

[https://pytorch.org/docs/stable/generated/torch.nn.Unfold.html](https://pytorch.org/docs/stable/generated/torch.nn.Unfold.html)

`torch.nn.Unfold()` extracts sliding local blocks from a batched input tensor. Convolution is equivalent with Unfold + Matrix Multiplication + Fold (or view to output shape).



































































