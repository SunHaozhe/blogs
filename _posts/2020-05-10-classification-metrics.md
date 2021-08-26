---
layout: post
category: Mathematics
title: Classification metrics
tagline: by SunHaozhe
tags: 
  - Common knowledge
  - Mathematics
  - Machine learning
mathjax: true
comments: true
published: true
---


* true positive (TP) 
* true negative (TN)
* false positive (FP)
    * type I error: rejection of a true null hypothesis
* false negative (FN) 
    * type II error: non-rejection of a false null hypothesis 


Confusion matrix:

| | |
|-|-|
| TP                 | FP (type I error) |
| FN (type II error) | TN                |





$$\text{precision} = \frac{\text{TP}}{|\text{predicted as positive}|} = \frac{\text{TP}}{\text{TP} + \text{FP}}$$

$$\text{recall} = \text{sensitivity} = \text{true positive rate} = \text{probability of detection} = \frac{\text{TP}}{|\text{real positive}|} = \frac{\text{TP}}{\text{TP} + \text{FN}}$$

$$\text{specificity} = \text{true negative rate} = \frac{\text{TN}}{|\text{real negative}|} = \frac{\text{TN}}{\text{TN} + \text{FP}}$$

$$\text{accuracy} = \frac{|\text{correct prediction}|}{|\text{all samples}|} = \frac{\text{TP} + \text{TN}}{\text{TP} + \text{TN} + \text{FP} + \text{FN}}$$

$$\text{F1 score} = \frac{2 \times \text{precision} \times \text{recall}}{\text{precision} + \text{recall}}$$



**Accuracy** can be a misleading metric for imbalanced data sets because it can be biased by the most common target class. 

A high **sensitivity** test is reliable when its result is negative since it rarely misdiagnoses the cases which are indeed positive, it is not that reliable when its result is positive. 

A high **specificity** test is reliable when its result is positive since it rarely gives positive results in negative cases, it is not that reliable when its result is negative. A test with a higher specificity has a lower type I error rate.

The **ROC curve** is created by plotting the true positive rate (TPR) against the false positive rate (FPR) at various threshold settings. **True positive rate (TPR)** is also known as recall, sensitivity or probability of detection. **false positive rate (FPR)** can be calculated as $1 - \text{specificity}$. 

![roc_curve](/assets/images/blog/roc_curve.png)


The **precision-recall curve (PR curve)** shows the tradeoff between precision and recall for different threshold. 

According to [a post on Cross-Validated](https://stats.stackexchange.com/questions/7207/roc-vs-precision-and-recall-curves), the key difference is that ROC curves will be the same no matter what the baseline probability is, but PR curves may be more useful in practice for problems where the positive class is more interesting than the negative class (imbalanced problems). Recall (sensitivity) and specificity, which make up the ROC curve, are probabilities conditioned on the true class label. Precision is a probability conditioned on the estimate of the class label. If the question is: "How meaningful is a positive result from the classifier given the baseline probabilities of the problem?", use a PR curve. If the question is, "How well can this classifier be expected to perform in general, at a variety of different baseline probabilities?", go with a ROC curve.



![pr_curve_example](/assets/images/blog/pr_curve_example.png)

![pr_curve_example_2](/assets/images/blog/pr_curve_example_2.png)


The general definition for the Average Precision (AP) is finding the area under the precision-recall curve. Before calculating AP for the object detection, we often smooth out the zig-zag pattern first. Graphically, at each recall level, we replace each precision value with the maximum precision value to the right of that recall level. 

mAP (mean average precision) is the average of AP. In some context, we compute the AP for each class and average them to get mAP. But in some context, for example, under the COCO context, AP is averaged over all classes (even also over several IoU thresholds), which is traditionally called mAP. 

How is precision-recall curve drawn? 

* In the context of object detection, we first rank the predicted bounding boxes by their corresponding confidence score. By following this ranking, we cumulatively (only considering the current bounding box and all previous bounding boxes) calculate pairs of precision and recall. For each precision-recall pair, we draw a point in the precision-recall curve. By following this ranking, the recall of successive pairs will be increasing (not strictly increasing) by nature, on the other hand the precision can have a zig-zag pattern because of newly encountered false-positive predicted bounding boxes. This is why precision-recall curve is usually smoothed. 

![pr_curve_smoothing](/assets/images/blog/pr_curve_smoothing.jpeg)

Comparison of F1 score and AP:

* F1 score is sensitive to the trade-off between precision and recall. Therefore, careful tuning is often needed to get the best combination of precision and recall. On the other hand, AP is invariant to that trade-off. AP, on the other hand, calculates a measure by iterating over all points of precisions and recalls. Thus, it is invariant to the trade-off between precision and recall. 
* For each point of the precision-recall curve (each pair of precision+recall), one can obtain a F1 score. One can then choose to keep the maximum one among obtained F1 scores. 


| English   | Chinese        |
|-----------|----------------|
| precision | 精确率，查准率 |
| recall    | 召回率，查全率 |
| sensitivity    | 灵敏度 |
| specificity    | 特异度 |
| accuracy    | 准确率 |
| confusion matrix    | 混淆矩阵 |
| mAP    | 平均精度均值 |















