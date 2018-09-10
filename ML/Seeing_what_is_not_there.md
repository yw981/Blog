# 基于上下文环境断定目标缺失 Seeing What Is Not There: Learning Context to Determine Where Objects Are Missing

## 摘要及简介

计算机视觉研究大多关注的是图像中有什么，本文提出并训练了一个独立目标中心化的上下文环境模型来完成相反的工作，即检测图像中缺少什么。
我们的上下文环境模型可以检测到应该出现的物体，即使它没有出现。
模型基于卷积神经网络结构。通过一个特殊的训练策略，模型学着忽略目标物体本身儿专注于目标周边的环境。

从计算的角度看，在满足以下情况时，一个目标物体被认为在图像中缺失：

1. 目标检测器检测到不存在
1. 目标物体典型环境预测器表明其有很大概率存在

我们需要把所有这样的区域高效地检测出来。目标物体与环境的关系见下表：

| 目标检测器评分 | 环境预测器评分 | 图像区域标记 |
| :------: | :------: | :------: |
| 高 | 高 | 典型物体 |
| 低 | 高 | 物体缺失 |
| 高 | 低 | 不符合环境预期而出现的物体 |

提出这个课题的最初契机是街景路缘坡道（路口马路牙子便于残疾人上下的斜坡）检测问题。
标记城市路口路缘坡道的有无以便于残疾人规划其出行路线。

![](.Seeing_what_is_not_there_images\f1.png)

> 图注：当路口路缘坡道缺失（如图中左侧橙色区域，右侧绿色区域为正常）时，行动障碍人士将无法穿越这个路口。
我们提出了一种通过学习周边环境来检测物体缺失的模型，用来与目标检测的结果结合。

## 相关工作

## 从显式目标遮罩中学习上下文环境

\begin{equation}
L_c = -Q_y(I_m) + log \sum_y e^{Q_y(I_m)}
\end{equation}

## A Fully Convolutional Model that Learns Implicit Masks

## Finding Missing Object Regions Pipeline

## 实验

### 6.1. Characteristics of the Trained Model

### 6.2. Finding Missing Curb Ramp Regions

### 6.3. Finding Out of Context Faces

## 结论

| 左对齐标题 | 右对齐标题 | 居中对齐标题 |
| :------| ------: | :------: |
| 短文本 | 中等文本 | 稍微长一点的文本 |
| 稍微长一点的文本 | 短文本 | 中等文本 |
