# 基于上下文环境断定目标缺失 Seeing What Is Not There: Learning Context to Determine Where Objects Are Missing

## 1 摘要及简介

计算机视觉研究大多关注的是图像中有什么，本文提出并训练了一个独立以目标为中心的周边环境模型来完成相反的工作，即检测图像中缺少什么。
我们的周边环境模型可以检测到应该出现的物体，即使它没有出现。
模型基于卷积神经网络结构。通过一个特殊的训练策略，模型学着忽略目标物体本身儿专注于目标周边的环境。

从计算的角度看，在满足以下情况时，一个目标物体被认为在图像中缺失：

1. 目标检测器检测到不存在
1. 目标物体典型环境预测器表明其有很大概率存在

我们需要把所有这样的区域高效地检测出来。目标物体与环境的关系见下表：

| 目标检测器评分 | 环境预测器评分 | 图像区域标记 |
| :------: | :------: | :------: |
| 高 | 高 | 典型物体 |
| 低 | 高 | 物体缺失（该有却没有） |
| 高 | 低 | 不符合环境预期而出现的物体（不该有却有） |

提出这个课题的最初契机是街景路缘坡道（路口马路牙子便于残疾人上下的斜坡）检测问题。
标记城市路口路缘坡道的有无以便于残疾人规划其出行路线。

![](.Seeing_what_is_not_there_images\f1.png)

> 图注：当路口路缘坡道缺失（如图中左侧橙色区域，右侧绿色区域为正常）时，行动障碍人士将无法穿越这个路口。
我们提出了一种通过学习周边环境来检测物体缺失的模型，用来与目标检测的结果结合。

解决这个问题的关键在于训练一个仅关注周边环境的模型，就像一个目标检测器一样：通过扫描每张图片，获得一个概率映射热力图，
图中每一个像素代表这一点目标存在的概率，即使目标物体不存在。
这种周边环境和目标分离的好处在于，我们不需要再训练集中标记不正常（缺失、不符合环境预期而出现的物体）的目标物体，
独立周边环境模型可以由典型目标物体训练得到，而后用于寻找不正常目标物体。
这大大减轻了训练的工作量，因为正常物体的样本非常丰富，收集起来比不正常样本容易得多。

本文主要内容

1. 提出一种通过学习遮罩物体来获得以目标为中心的周边环境表示的训练方法。
1. 提出一种通过强制神经网络忽略物体本身，学习非显式遮罩的训练策略。
1. 应用于检测街景图中缺失路缘坡道的较理想结果；应用于检测不符合环境预期出现的人脸图像的初步结果。

## 2 相关工作

### 目标识别中的周边环境

认知科学认为周边环境影响人对物体的识别与判断。因此，此概念在计算机视觉中也逐渐被接受。

### 找到缺失目标

General Hough Transform：在目标跟踪的诊帧中检测缺失目标（可能是视频），其核心是通过周边物体的连续运动评估目标的位置。

### 带有遮罩图像的计算机视觉

Pathak et al.提出一个基于卷积神经网络的encoder用来自动填补画图（image inpainting）。与本文同用了遮罩，但他们训练生成式模型来填补遮罩，
而本文训练判别（discriminative）模型来推断遮罩后是什么。

### Accessibility Task

很多计算机算法利用网上大量的数据资源，例如谷歌街景等来帮助残障人士。 CrossingGuard是一套帮助视觉障碍行人导航通过十字路口的系统。
Tohme是半自动的结合众包和计算机视觉来收集城市中路缘坡道位置的系统，也使用谷歌街景图像。它使用DPM(Deformable Parts Models)作为路缘坡道检测器。
本文利用的数据集，是他们提供的一套包含1086个城市交叉路口街景路缘坡道的数据集。

## 3 从显式目标遮罩中学习周边环境

这部分介绍前文提到的周边环境学习算法的基本版本。
如果周边环境被定义为目标物体周围除了目标物体本身的内容，那么可以认为这个模型字面上说是在学习周边环境：
因为所有训练集中的目标物体都被遮罩掉了。

在一个图像二分类训练集上训练模型。

1. 正样本：目标物体在每张图中心，物体本身及其视觉延伸被黑框遮罩（预处理后值为零）。整个图片宽约是物体边界框的4倍。
1. 负样本：每张图片中心区域采用类似的遮罩层随机裁剪。被覆盖的区域与ground truth标记物体重合不大于Jaccard系数0.2。



若一幅图像中有多个目标物体，在每个正样本中仅遮罩一个。因为对于一个目标物体来说，其他目标物体可以视作有用的“周边环境”。例如，路缘坡道就经常成对出现。

训练模型Q，由带有池化和dropout的四层卷积层和两个全连接层构成。其结构见下表

| 层类型 | Shape | 参数个数 |
| :------: | :------: | :------: |
| Convolution2D | (3, 3, 32) | 896 | 
| Convolution2D | (3, 3, 32) | 9248 | 
| MaxPooling2D | (2, 2) | 0 |
| Dropout | - | 0 |
| Convolution2D | (3, 3, 64) | 18496 |
| Convolution2D | (3, 3, 64) | 36928 |
| MaxPooling2D | (2, 2) | 0 |
| Dropout | - | 0 |
| FullyConnected | (53*53*64, 256) | 46022912 |
| Dropout | - 0 |
| FullyConnected | (256, 2) | 514 | 

共46,088,994个参数

损失函数：使用交叉熵作为分类损失函数。

\begin{equation}
L_c = -Q_y(I_m) + log \sum_y e^{Q_y(I_m)}
\end{equation}

其中，$ y \in \{ 1,2 \} $是遮罩图片$ I_m $的groundtruth标签（1为正样本，2为负样本），
$ Q(I_m) $是2*1向量，表示Q的输出，$ Q_y(I_m) $表示第y个分量。

$$
Q(I_m) = \begin{bmatrix}
   Q_1(I_m) \\
   Q_2(I_m)
\end{bmatrix}
$$

## 4 学习隐式遮罩的全卷积模型 A Fully Convolutional Model that Learns Implicit Masks

\begin{equation}
L_d = \| Q_y(I_m) - Q(I) \|_{p}
\end{equation}

![](.Seeing_what_is_not_there_images\F2.png)

孪生训练全卷积周边环境神经网络 the Siamese trained Fully convolutional Context network (SFC)的训练方案图。
直觉上看，强制全卷积神经网络Q输出相似的结果，无论输入图片被遮罩与否。

> Siamese network 孪生神经网络，简单来说用于衡量两个输入的相似程度。孪生神经网络有两个输入（Input1 and Input2）,将两个输入feed进入两个神经网络（Network1 and Network2），这两个神经网络分别将输入映射到新的空间，形成输入在新的空间中的表示。通过Loss的计算，评价两个输入的相似度。


\begin{equation}
L = \lambda L_d + L_c 
\end{equation}

![](.Seeing_what_is_not_there_images\f3.png)

上：输入：街景全景图。

中：使用基本神经网络利用滑动窗口方法生成的热力图。由于较高的计算损失，空间分辨率低。

下：利用SFC生成的稠密热力图。

hard negative mining（难分样本挖掘）：把错误（尤其是顽固棘手的）送回训练集作为负样本，继续训练。

## 5 找到缺失物体区域的渠道 Finding Missing Object Regions Pipeline

利用独立的周边环境网络（基本网络或SFC网络），找出缺失物体区域的步骤如下：

1. 通过周边环境网络Q生成环境热力图。热力图表示目标物体应该出现的位置。
1. 利用目标检测器生成目标检测结果。将目标检测结果转换为二值图像（0-1图像），转换方式为，
目标物体边界框以内均转为0，其它区域为1。
1. 将热力图与二值图执行按像素与操作，得到结果图，是目标物体应该出现但目标检测器未检测到物体的区域。
1. 根据结果图，取其高分区域（超过某一阈值），将原图中对应部分裁剪下来，得到物体缺失区域。

## 6 实验

### 6.1. Characteristics of the Trained Model

### 6.2. Finding Missing Curb Ramp Regions

### 6.3. Finding Out of Context Faces

## 7 结论


#### Precision 与 Recall
 
Precision（准确率）和Recall（召回率）是两个评价模型效果的指标。

例如一个从图像中识别飞机的问题，定义：

True positives : 飞机的图片正确识别成为飞机。 
True negatives: 不是飞机的图片正确识别为不是飞机的图片。 
False positives: 不是飞机的图片被错误识别成为飞机。 
False negatives: 是飞机的图片被错误识别成不是飞机的图片（没有识别出来）。

Precision其实就是在识别出来的图片中，True positives所占的比率： 
这里写图片描述 
其中的n代表的是(True positives + False positives)，也就是系统一共识别出来多少照片 。 
在这一例子中，True positives为3，False positives为1，所以Precision值是 3/（3+1）=0.75。 
意味着在识别出的结果中，飞机的图片占75%。

Recall 是被正确识别出来的飞机个数与测试集中所有飞机的个数的比值： 
这里写图片描述 
Recall的分母是(True positives + False negatives)，这两个值的和，可以理解为一共有多少张飞机的照片。 
在这一例子中，True positives为3，False negatives为2，那么Recall值是 3/（3+2）=0.6。 
意味着在所有的飞机图片中，60%的飞机被正确的识别成飞机.。

#### Jaccard Index IoU

> Jaccard Index （IoU）: 给定两个集合A,B的Jaccard系数定义为A与B交集的大小与并集大小的比值。

$$
J(A,B) = \frac{|A \cap B|}{|A \cup B|} = \frac{|A \cap B|}{|A|+|B|-|A \cap B|}
$$

> Jaccard值越大说明相似度越高。当A和B相等时，Jaccard(A,B)=1； 

#### ADADELTA: An Adaptive Learning Rate Method

[https://arxiv.org/abs/1212.5701](https://arxiv.org/abs/1212.5701)

Adagrad

Adagrad其实是对学习率进行了一个约束。即： 
nt=nt−1+g2tnt=nt−1+gt2n_t=n_{t-1}+g_t^2 
Δθt=−ηnt+ϵ−−−−−√∗gtΔθt=−ηnt+ϵ∗gt\Delta{\theta_t}=-\frac{\eta}{\sqrt{n_t+\epsilon}}*g_t 
此处，对gtgtg_t从111到ttt进行一个递推形成一个约束项regularizer，−1∑tr=1(gr)2+ϵ√−1∑r=1t(gr)2+ϵ-\frac{1}{\sqrt{\sum_{r=1}^t(g_r)^2+\epsilon}} ，ϵϵ\epsilon用来保证分母非0

特点：


前期gtgtg_t较小的时候， regularizer较大，能够放大梯度
后期gtgtg_t较大的时候，regularizer较小，能够约束梯度
适合处理稀疏梯度


缺点：


由公式可以看出，仍依赖于人工设置一个全局学习率
ηη\eta设置过大的话，会使regularizer过于敏感，对梯度的调节太大
中后期，分母上梯度平方的累加将会越来越大，使gradient→0gradient→0gradient\to0，使得训练提前结束




Adadelta

Adadelta是对Adagrad的扩展，最初方案依然是对学习率进行自适应约束，但是进行了计算上的简化。 
Adagrad会累加之前所有的梯度平方，而Adadelta只累加固定大小的项，并且也不直接存储这些项，仅仅是近似计算对应的平均值。即： 
nt=ν∗nt−1+(1−ν)∗g2tnt=ν∗nt−1+(1−ν)∗gt2n_t=\nu*n_{t-1}+(1-\nu)*g_t^2 
Δθt=−ηnt+ϵ−−−−−√∗gtΔθt=−ηnt+ϵ∗gt\Delta{\theta_t} = -\frac{\eta}{\sqrt{n_t+\epsilon}}*g_t

在此处Adadelta其实还是依赖于全局学习率的，但是作者做了一定处理，经过近似牛顿迭代法之后： 
E|g2|t=ρ∗E|g2|t−1+(1−ρ)∗g2tE|g2|t=ρ∗E|g2|t−1+(1−ρ)∗gt2E|g^2|_t=\rho*E|g^2|_{t-1}+(1-\rho)*g_t^2 
Δxt=−∑t−1r=1Δxr−−−−−−−−√E|g2|t+ϵ−−−−−−−−√Δxt=−∑r=1t−1ΔxrE|g2|t+ϵ\Delta{x_t}=-\frac{\sqrt{\sum_{r=1}^{t-1}\Delta{x_r}}}{\sqrt{E|g^2|_t+\epsilon}} 
其中，EEE代表求期望。 
此时，可以看出Adadelta已经不用依赖于全局学习率了。

特点：


训练初中期，加速效果不错，很快
训练后期，反复在局部最小值附近抖动


[https://blog.csdn.net/u012759136/article/details/52302426](https://blog.csdn.net/u012759136/article/details/52302426) 


Keras 已收录
