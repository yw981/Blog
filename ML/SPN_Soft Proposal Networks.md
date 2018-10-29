# Soft Proposal Networks for Weakly Supervised Object Localization

# 弱监督目标定位的SPN（Soft Proposal Networks）论文重点摘录及阅读笔记

## 摘要

仅使用图像标签而不用边界框的弱监督目标定位仍是一个有挑战性的课题。
在定位模型中，Object proposals是非常有效的组件，但它常常难于计算。
本文中，我们首次将弱监督的Object proposal集成到CNNs端到端的网络中。
设计并提出了一种网络组件SP（Soft Proposal），可嵌入标准CNN结构中，几乎无消耗地提供Object proposal。
SP强化的CNN，后称Soft Proposal Networks (SPNs)，基于deep feature maps迭代进化的Object proposal投射回原图像，
而后仅使用图像标签训练和调整优化网络参数。
经过统一的学习过程，SPN可学得更佳的目标中心filter，发现更多discriminative信息，抑制背景影响。
在多个评价标准下，同时大幅提升弱监督目标定位和分类的性能。

## 简介

## SPN（Soft Proposal Networks）

### Soft Proposal生成

### Soft Proposal Coupling

### Weakly Supervised Activation