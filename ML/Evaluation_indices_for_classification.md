# 分类问题结果统计指标的解释：真正类率TPR与假负类率FPR、准确率与召回、感受性与特异性

## 预测及结果概念定义：TP、FN、FP、TN

对于一个模型接受输入后的预测结果，可由下表表示。表中1代表正样本，0代表负样本。

例如，检测一张图片是否是含有猫的图片。真实值（ground truth）1代表图片中有猫，真实值0代表图片中没有猫，
预测值1代表模型（输出）认为图片中有猫，预测值0代表模型认为图片中没有猫。

|  | 预测值1 | 预测值0 | 合计（真实） |
| ------ | ------ | ------ | ------ |
| 真实值1 | TP （True Positive） | FN （False Negative） | TP+FN （Actual Positive） |
| 真实值0 | FP （False Positive） | TN （True Negative） | FP+TN （Actual Negative） |
| 合计（预测） | TF+FP （Predicted Positive） | FN+TN （Predicted Negative） |

TP （True Positive）：真正类，模型认为图中有猫实际也有猫的。模型预测正确。

FP （False Positive）：假正类，有些领域中称“假阳性”，模型认为图中有猫实际没有有猫的。模型预测错误。

FN （False Negative）：假负类，模型认为图中没有猫实际有猫的，即模型没检测出来。模型预测错误（两个F开头的都是模型预测错误）。

TN （True Negative）：真负类，模型认为图中没有猫实际也没有猫的。模型预测正确（两个T开头的都是模型预测正确）。

## TPR（Recall）真正类率 与 FPR 假负类率

### TPR（Recall）真正类率

TPR（True Positive Rate），即真正类率，又叫真阳性率、Recall（召回率）、Sensitivity（感受性）、Probability of Detection等。

TPR = TP/(TP+FN) = TP/(Actual Positive) ，表示模型预测的真正类个数占所有真实值为1个数的比例。

> TPR 预测对的/所有对的

### FPR 假正类率

FPR（False Positive Rate），即假正类率，又叫假阳性率，Fall-out、Probability of False Alarm等。

FPR = FP/(FP+TN) = FP/(Actual Negative) = 1 - Specificity，表示模型预测的假正类个数占所有真实值为0个数的比例。

## Sensitivity感受性 与 Specificity 特异性

Sensitivity感受性，定义同TPR，见前文。

### Specificity 特异性

Specificity = TN/(FP+TN) = TN/(Actual Negative) = 1 - FPR

Sensitivity（感受性）和Specificity（特异性）的直观解释：

感受性高：考虑极端情况，所有的正样本都被模型正确预测，TP↑，FN→0（准确地说不应该这样表示，应该理解为TP较大，FN相对较小）。
实例中，只要图中有猫一定能被模型识别出来，也就是，有猫的图像不会被误判为没有猫的（不会漏检）。

特异性高：考虑极端情况，所有的负样本都被模型正确预测，TN↑，FP→0（此表达不准确，解释类同上）。
实例中，没有猫的图像都被模型识别为没有猫，也就是，没有猫的图像不会被误判为有猫的，“假阳性”几乎没有。

> 医学检验中常采用特异性高的指标，例如，当感染HIV病毒时，血液中HIV1/2抗体会升高，这一指标之所以用于HIV检测就是因为它特异性非常高，
如果血液中HIV1/2抗体被检验为阳性，说明感染HIV病毒的可能性非常高（几乎不会是其他因素引起的，不会错检）。若用特异性不高的指标检测，则非常容易出现假阳性。“假阳性”这一概念源于医学术语。

因此，特异性高的模型，如果模型预测结果图中有猫，那么实际图中也有猫的概率非常大。

> FPR 预测错的/所有错的

## Precision 准确率 与 Recall召回率

Precision = TP/(TP+FP) = TP/(Predicted Positive)

> Precision即模型预测出来的有多少是对的

Recall召回率，定义同TPR，见前文。

> 准确率Precision与召回率Recall概念常一同使用，可简单理解为：召回率是检出了“多少”，准确率是检出的有“多少是对的”。
召回率代表检出的“数量”，准确率代表检出的“质量”。