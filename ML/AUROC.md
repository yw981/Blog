# 分类问题结果统计指标说明：准确率、召回、AUROC

## 概念定义

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

### TPR 真正类率

> TPR（True Positive Rate），即真正类率，又叫 Recall（召回率）、Sensitivity（敏感度）、Probability of Detection等。

TPR = TP/(TP+FN) = TP/(Actual Positive) ，表示模型预测的真正类个数占所有真实值为1个数的比例。

> TPR 预测对的/所有对的

### FPR 假负类率

> FPR（False Positive Rate），即假负类率，又叫Fall-out、Probability of False Alarm等。

FPR = FP/(FP+TN) = FP/(Actual Negative) ，表示模型预测的假负类个数占所有真实值为0个数的比例。

> FPR 预测错的/所有错的