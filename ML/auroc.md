# 受试者工作特征 ROC（Receiver Operating Characteristic）：曲线（ROC Curve）、面积（AUROC）及空间（ROC Space）

## ROC定义

> 接前文 TODO引用前文

受试者工作特征曲线 （Receiver Operating Characteristic curve，简称ROC曲线），又称为感受性曲线（Sensitivity curve）。
ROC曲线就是以假正类率FPR（False Positive Rate）为横轴，真正类率TPR（True Positive Rate）为纵轴所组成的坐标图，
利用模型采用不同的判断标准得出的不同结果画出的曲线。

根据每个测试样本属于正样本的概率值从大到小排序。下图是一个示例，图中共有20个测试样本，“Class”一栏表示每个测试样本真正的标签（p表示正样本，n表示负样本），“Score”表示每个测试样本属于正样本的概率。


| 序号 | 类别 | 得分 | TPR | FPR | | 序号 | 类别 | 得分 | TPR | FPR |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | p | 0.9 | 0.1 | 0 | |  11 | p | 0.4 | 0.7 | 0.4 |
| 2 | p | 0.8 | 0.2 | 0 | |  12 | n | 0.39 | 0.7 | 0.5 |
| 3 | n | 0.7 | 0.2 | 0.1 | |  13 | p | 0.38 | 0.8 | 0.5 |
| 4 | p | 0.6 | 0.3 | 0.1 | |  14 | n | 0.37 | 0.8 | 0.6 |
| 5 | p | 0.55 | 0.4 | 0.1 | |  15 | n | 0.36 | 0.8 | 0.7 |
| 6 | p | 0.54 | 0.5 | 0.1 | |  16 | n | 0.35 | 0.8 | 0.8 |
| 7 | n | 0.53 | 0.5 | 0.2 | |  17 | p | 0.34 | 0.9 | 0.8 |
| 8 | n | 0.52 | 0.5 | 0.3 | |  18 | n | 0.33 | 0.9 | 0.9 |
| 9 | p | 0.51 | 0.6 | 0.3 | |  19 | p | 0.30 | 1.0 | 0.9 |
| 10 | n | 0.505 | 0.6 | 0.4 | |  20 | n | 0.1 | 1.0 | 1.0 |

接下来，我们从高到低，依次将“Score”值作为阈值threshold，当测试样本属于正样本的概率大于或等于这个threshold时，我们认为它为正样本，否则为负样本。举例来说，对于图中的第4个样本，其“Score”值为0.6，那么样本1，2，3，4都被认为是正样本，因为它们的“Score”值都大于等于0.6，而其他样本则都认为是负样本。每次选取一个不同的threshold，我们就可以得到一组FPR和TPR，即ROC曲线上的一点。这样一来，我们一共得到了20组FPR和TPR的值，将它们画在ROC曲线的结果如下图：

![](roc_example.png)

当我们将threshold设置为1和0时，分别可以得到ROC曲线上的(0,0)和(1,1)两个点。将这些(FPR,TPR)对连接起来，就得到了ROC曲线。当threshold取值越多，ROC曲线越平滑。

其实，我们并不一定要得到每个测试样本是正样本的概率值，只要得到这个分类器对该测试样本的“评分值”即可（评分值并不一定在(0,1)区间）。评分越高，表示分类器越肯定地认为这个测试样本是正样本，而且同时使用各个评分值作为threshold。我认为将评分值转化为概率更易于理解一些。

## AUROC值的计算

AUROC (Area Under Receiver Operating Characteristic curve)，有时简称AUC (Area Under Curve) 
定义为ROC曲线下的面积，显然这个面积的数值不会大于1。又由于ROC曲线一般都处于y=x这条直线的上方，所以AUROC的取值范围一般在0.5和1之间。使用AUROC值作为评价标准是因为很多时候ROC曲线并不能清晰的说明哪个分类器的效果更好，而作为一个数值，对应AUROC更大的分类器效果更好。

> AUROC计算结果为一实数值，通常介于0.5~1之间。

AUROC的计算有两种方式，梯形法和ROC AUCH法，都是以逼近法求近似值

## AUROC的直观意义

那么AUROC值的含义是什么呢？根据(Fawcett, 2006)，AUROC的值的含义是：

> The AUROC value is equivalent to the probability that a randomly chosen positive example is ranked higher than a randomly chosen negative example.

解释：首AUROC值代表一个概率值——对于随机挑选一个正样本以及一个负样本，当前的分类算法根据计算得到的Score值将这个正样本排在负样本前面的概率即AUROC值。
当然，AUROC值越大，当前的分类算法越有可能将正样本排在负样本前面，即能够更好的分类。

> AUROC受测试集正反例分布影响较小，而AUPR受其影响更大

从AUROC判断分类器（预测模型）优劣的标准：

AUROC = 1，是完美分类器，采用这个预测模型时，存在至少一个阈值能得出完美预测。绝大多数预测的场合，不存在完美分类器。
0.5 < AUROC < 1，优于随机猜测。这个分类器（模型）妥善设定阈值的话，能有预测价值。
AUROC = 0.5，跟随机猜测一样（例：丢铜板），模型没有预测价值。
AUROC < 0.5，比随机猜测还差；但只要总是反预测而行，就优于随机猜测。

![](three_aoc.png)

简单说：AUROC值越大的分类器，正确率越高。

## ROC空间

ROC空间就是以假正类率FPR（False Positive Rate）为横轴，真正类率TPR（True Positive Rate）为纵轴所构成的平面空间。

模型输出的结果经过计算，会成为落在ROC空间中的点，对ROC空间中的点的位置的比较，可以直观地反映模型的性能好坏。

![](four_results.png)

以上是4个模型的输出结果，其中C和C'互为相反的模型，即C模型输出Positive时，C'输出Negative，反之亦然。

![](roc_space.png)

以上是4个模型的输出结果落在ROC空间中的点。

> 点越靠近空间的左上角，其预测性能越好

> 点与随机猜测线（虚线）的距离的大小，表示模型的预测能力的大小

> 如果点落在随机猜测线下，表名模型性能甚至不如随机选择，应对其结果取反再计算

结果表明，模型A在A、B、C模型中具有最好的预测性能。
模型B落在随机猜测线上（虚线），可以看到B的准确率是50%。
模型C表现最差，然而，其关于中心（0.5,0.5）的对称点C'，性能甚至超过A。
