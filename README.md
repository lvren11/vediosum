# vediosum
# 1.深度强化学习用于无监督的视频摘要多样性代表性(DR)
#### 无监督视频摘要
#### 将视频摘要表述为一个顺序决策过程，并训练一个深层的摘要网络，以通过强化学习来产生各种具有代表性的视频摘要

## DSN具有编码器-解码器体系结构
- 编码器是对视频帧执行特征提取的卷积神经网络（CNN），
- 解码器是双向LSTM网络，它会根据采样的动作来选择帧，从而产生概率
- 数据集SumMe和TVSum->标准的5倍交叉验证（5FCV），即80％的视频用于培训，其余的用于测试。

本文贡献是：
- 到端+基于强化学习框架具有多样性代表（DR）奖励功能

   奖励函数由多样性奖励和代表性奖励组成。

   分集奖励衡量所选帧之间的相异程度
- 代表性奖励则计算帧与其最近的所选帧之间的距离-k值问题
- 将无监督的方法扩展到有监督的版本以利用标签。对数概率最大化
## 拟议方法
通过强化学习来训练深度汇总网络（DSN）。DSN收到视频V i并采取措施视频的一部分被选择为摘要S的A（即二进制变量序列）。反馈奖励R（S）基于摘要的质量（即多样性和代表性）来计算。
![Proposed Approach](imgs/pa1.png)
## 深度汇总网络
DSN中
编码器是卷积的提取视觉特征向量的神经网络（CNN）从输入视频帧。
解码器是双向递归神经网络（BiRNN）顶部带有完全连接（FC）层。
以S形函数结尾的FC层为每个帧预测概率pt，从中采样帧选择动作
## DR
Rdiv计算为所选帧之间成对差异的平均值

![DR](imgs/pa2.png)

Rrep视频帧与其最近的帧之间的平方误差最小均值

![DR](imgs/pa3.png)


![DR](imgs/pa4.png)
## 政策梯度(不了解)
## 正则化
对概率分布施加正则化项DSN产生的p1：T限制了为摘要选择的帧的百分比
## 优化
stochas-优化策略函数的参数θ基于tic梯度的方法。
## 扩展到监督学习
给定视频的关键帧索引，Y ∗ = {y ∗ i | i =1，...，| Y ∗ |}，使用最大似然估计（MLE）以最大化选择的对数概率由Y ∗指定的关键帧，log p（t;θ）其中t∈Y ∗
## 摘要的镜头
通过最大化总得分来选择镜头，而
确保摘要长度不超过限制，
通常是视频长度的15％。
步骤本质上是0/1背包问题，即
被称为NP-hard问题（百度）
## 实验比较

- 设定基准模型和那些只接受Rdiv和Rrep训练的人一样分别由D-DSN和R-DSN表示。我们代表
该模型将两种奖励一起训练为DR-DSN。
扩展到监督版本的模型表示为
由DR-DSNsup。验证了
拟议的技术（称之为“技术”
on）在计算Rdiv时忽略了遥远的相似性。
将未经技术培训的D-DSN表示为
D-DSNw / o

# 2.对抗性LSTM网络的无监督视频汇总

## 用作无监督视频摘要的基准

关键思想是学习一个深入的摘要器网络以最大程度地减少训练视频与发布之间的距离以无人监督的方式总结它们的摘要
## 新的生成对抗框架-摘要器和鉴别器
- 摘要器是自动编码器长短期记忆网（LSTM），选择视频帧，然后解码获得的用于重建输入视频的摘要
- 鉴别器是另一个LSTM针对原始视频，并从汇总程序的重建之间的区分。
## 文章运用到的模型
- 变体自动编码器（ VAE）[[16] D. P. Kingma and M. Welling. Auto-encoding variational
bayes. ICLR, 2014.]是一个有向图模型，在给定一个未观察到的
潜在变量的情况下，它定义了观测数据的后验分布。
- 生成对抗网络（ GAN）[ [8] I. Goodfellow, J. Pouget-Abadie, M. Mirza, B. Xu,
D.Warde-Farley, S. Ozair, A. Courville, and Y. Bengio. Generative
adversarial nets. In NIPS, pages 2672–2680, 2014]是由两个相互竞争的子网组成的神经网络：i）
一个“生成器”网络（G），它生成模仿未知分布的数据； ii）一个“鉴别器”网络
（D），用于区分生成的样本和来自以下样本的样本真实的观察。目的是要找
到一个生成器，该生成器在分配鉴别器犯错误的可能性最大化的同时，确定真
实的数据分布。
## 步骤
- 任务：给定一个视频，在没有关键帧的标注下，选取关键帧。

- 先验：关键帧的分布应该与原序列的分布一直（去除冗余信息）

- 正规化：关键帧的个数应该尽可能的少。

  关键帧的信息尽可能离散。
- 做法：

  slstm：输出每一帧的得分，与原来帧加权后得到新的特征。
 
  elstm：对于lstm得到的特征编码，得到一个特征。
 
  dlstm：对elstm得到的特征解码，恢复出原来的特征。
 
  clstm：判断dlstm得到的特征是否还是原来的特征。

- 处理：根据每一帧的得分选出关键帧。

  将视频分成不重叠的几个clip。
  
  每个clip的得分是这个clip中所有帧的得分的平均，对clip排序。
  
  高得分的clip中的帧按照分数排序，选出最高的几帧。

##与原纸相比的变化
- 视频特征提取器
     - GoogleNet pool5（1024）=> ResNet-101 pool5（2048）
     - 随后线性投影至500-dim
- 稳定的GAN训练
     - 鉴别者的学习率：1e-5（其他人：1e-4）
     - 在每个时期的前15个步骤中修复鉴别器的参数。
## 模型特征
![Model figure 1](imgs/fig1.png)

- （a）概述：我们的目标是选择关键帧，以使所选关键帧的特征表示与
视频之间的距离最小。
- （b）由于很难指定深层特征之间的合适距离，因此我
们使用了生成对抗性框架来优化帧选择器。我们的方法包括一个可变自动编码
器和一个生成对抗网络。
![Model figure 2](imgs/fig2.png)

图2：主要组成部分：选择器LSTM（sLSTM）从输入序列中选择
帧的子集 X。 编码器LSTM（eLSTM）将选定的帧编码为固定长度的特征 e，
然后将其转发给解码器LSTM（dLSTM），以重建视频

![Model figure 3](imgs/fig3.png)


我们训练中使用的四个损失函数。 LGAN是增加的GAN损耗，Lreconst是循环编码器/解码器的重建损耗。 在训练中，我们使用
由先验分布（例如，均匀）控制的附加帧选择器sp，其产生编码表示
ep和重建的特征序列^ xp。 对cLSTM的对抗训练经过了正规化处理，因此在将^ xp识别为“摘要”时非常准确，但是却将^ x识别为“原始”。

## 算法
![Algorithm figure](imgs/fig4.png)

其中为了GAN模型训练相似，制定了一种对抗学习算法，该算法迭代地优化了以下三个目标：
- 学习{s，e}；最小化
（Lreconst + Lprior + Lsparsity）。
- 为了学习{d}，最小化（Lreconst + LGAN）。
- 为了学习{c}，最大化LGAN。

## 正则化（三种比较）
- 摘要长度正则化 SUM-GAN
- 基于DPP的正则化 SUMGANdpp
- 关键帧正则化 SUM-GANsup

## 数据集
SumMe ，TVSum，打开视频项目（OVP）和Youtube

将谐波平均F分数用作评估指标。将帧级别的分数
转换为关键帧和关键镜头摘要，反之亦然。为了为仅提供关键帧分数的数据集
生成关键镜头，使用KTS将视频最初在时间上分割为不相交的间隔。
根据间隔的重要性评分对所得间隔进行排序，其中间隔的重要性评分等于该间
隔中帧的平均评分。从排序的间隔中选择一个间隔子集作为关键帧，以使生成
的关键帧的总持续时间小于 15％ 原始视频的时长。


#3.通过注意力驱动的对抗学习进行无监督的视频汇总（2020）
基于SUM-GAN模型，提出架构SUM-GAN-sl（减少了参数数量，以增量方式更新模型的组件，并遵循基于标签的逐
步方法来训练对抗部分。

##贡献
- 在无监督的学习框架中引入注意机制，而以前所有基于注意的摘要方法均受到监督；
- 使用注意力来指导模型的生成对
抗训练，而不是使用它对视频片段进行排名。fa'fa

## 内容
以上一篇为起点做的，通过最小化原始视频的深层特征表示与其基于所选关键帧的重构版
本之间的距离来选择视频关键帧

- 在模型的变体自动编码器中直接引入关注层（SUM-GAN-VAAE）
- 用确定性关注自动编码
器（SUM-GAN-AAE）替换此组件。在SumMe和TVSum数据集上进行的实验记录了与原始
SUM-GAN模型相比所取得的改进，显示了变化注意力不能增强体系结构的训练能力，突出了注意力自动的贡献
-encoder可以更快，更稳定地训练模型，并展示提出的SUM-GAN-AAE体系结构与最新技术的竞争力。

![Model figure 1](imgs/su1.png)

其他的还在看，在第二篇的基础上加入注意力机制（还没看完）

# 想法
## 无监督方法
(按照论文中的看点，监督学习无法充分探索深度网络在视频摘要中的潜力，因为不存在视频的单个事实摘要。这是基于以下事实：人们对应该选择视频的哪些部分作为摘要有主观意见。)

## 可以在SUM-GAN模型上进行改进参数或者引进某个新的机制（第一篇是DR机制，二是不清楚，三是注意力机制）来计算精确度

## 暑假那个营里介绍GAN理论知识到时候可以学习
