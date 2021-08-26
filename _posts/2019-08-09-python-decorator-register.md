---
layout: post
category: Python
title: Python decorator register
tagline: by SunHaozhe
tags: 
  - Python
mathjax: true
comments: true
published: true
---


```python
mapping = {}

def register(name):
	def wrapper(function):
		mapping[name] = function
		return function
	return wrapper

def get_neural_network_architecture(architecture_name):
	if architecture_name in mapping:
		return mapping[architecture_name] 
	else:
		raise ValueError('Unknown architecture name: {}'.format(architecture_name))

@register("mlp") 
def mlp(hidden_layers):
	print('running mlp(hidden_layers) ' + str(hidden_layers))

@register("cnn") 
def cnn(hidden_layers, stride): 
	print('running cnn(hidden_layers, stride) ' + str(stride))


if __name__ == '__main__':
	#from ... import get_neural_network_architecture
	f = get_neural_network_architecture("cnn")
	print(f)
	f(2, 1) 
```


```python
Out:

<function cnn at 0x00000219BA78A950>
running cnn(hidden_layers, stride) 1 
```


















