---
title: David Silver 强化学习教程（1）：强化学习简介
date: 2020-5-7 11:44:26
index_img: /img/post_index_img/david silver.jpg
math: true
tags: 
- Reinforcement Learning
- AI
categories: 技术
---

# 写在最前面

人工智能领域无疑是近些年各个行业争相追捧的热点，我一个原本搞通信的现在也在逐渐学习AI的知识，毕竟现在通信的理论基础已经发展遇到瓶颈了，除非再出现一个祖师爷香农级别的人物引领一波理论革命。上一阶段的人工智能，按照个人理解可以概括建立在大数据基础上，让机器从带有标签的大量数据中提取特征，学习模式来完成预测和分类任务。最常见应用就包括计算机视觉、自然语言处理，这两个领域机器已经能做到达到甚至超越人类的性能，而无论公司和从业者都多到离谱，无疑已是一片红海。

另一类人工智能的分支，以强化学习为代表（当然这里头也涉及到多种技术的融合）。监督式的学习毕竟以人类标记作为准测，机器的性能天花板不可能超过人类。而强化学习才是真正能赋予了机器认知和意识的技术，实现真正意义上的“强人工智能”。以谷歌DeepMind团队开发的AlphaGo系统在2016年和2017年先后击败世界围棋高手李世石和柯洁为标志事件，强化学习算法引起了各个领域的广泛关注，并且取得了不少的研究成果。

对于各行业从业者，学会强化学习的知识并且用于自己的研究领域无疑是一个迫切的需求。而早在2015年作为领导AlphaGo项目的David Silver就在 UCL 开设了课程并把视频发布到了YouTube上，较为系统地介绍了强化学习的各种思想、实现算法。本系列文章是对于David 所授课程的学习笔记，力求尽量还原教学内容并穿插自己的理解。b站有配上中文字幕得教学视频。[b站链接](https://www.bilibili.com/video/BV1kb411i7KG?from=search&seid=4606460319322526728)

本笔记的撰写也参考了以下的文章：

* [David Silver 强化学习公开课中文讲解及实践](https://zhuanlan.zhihu.com/reinforce)
* [David Silver 增强学习——笔记合集](https://zhuanlan.zhihu.com/p/50478310)
* [强化学习知识整理](https://www.cnblogs.com/pinard/p/9385570.html)

关于参考书籍，David在本课程建议的参考书籍主要有两本：

* [《An Introduction to Reinforcement Learning》](https://web.stanford.edu/class/psych209/Readings/SuttonBartoIPRLBook2ndEd.pdf), （被誉为强化学习教父的Richard S. Sutton编写的经典书籍，也是David Silver的老师，这门课程基本按照这本书的逻辑来），这本书有人翻译成了中文：https://rl.qiwihui.com/zh_CN/latest/index.html
* [《Algorithms for Reinforcement Learning》](https://sites.ualberta.ca/~szepesva/papers/RLAlgsInMDPs.pdf)，（全书 200+页，较为精简，重视数学逻辑和严格推导，适合喜欢啃公式的同学）



# 强化学习简介

**强化学习框架**

下图简单阐述了强化学习的基本框架：智能体（Agent，即图中的大脑）需要通过和环境交互学习解决某项任务。首先智能体通过观察环境（observation）并采取一个动作（action），动作会对环境产生影响从而使环境发生改变，同时环境也会对行为进行反馈一个即时奖励（reward）。智能体重复该过程与环境不断地交互得到很多数据记录，并根据设定的学习算法使用数据改善自身的动作策略。在经过若干次学习后，智能体能最终学会完成相应任务的最优策略（policy）。

<div align="center">
<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course1.1.JPG" style="zoom:67%;" />
</div>

**强化学习与其他机器学习的联系**

如果你对常见的机器学习方法（machine learning）有所涉猎，根据上面对强化学习的描述，可以看出相比于其他的机器学习算法，强化学习具备以下的特点：

* 不存在监督者，而是通过和环境交互来获得奖励
* 反馈可能存在延迟，当前步骤采取动作带来的效果并不会立即见效（所以一般在实际操作中会设定一个强化学习过程的持续时间）
* 数据非独立同分布的：我们得到的决策过程，我们获取的每一次数据都不是独立同分布(i.i.d)的，他是一个有先后顺序的数据序列
* 动作对之后的结果能够产生影响：在决策过程中存在先后顺序，当前动作的不同导致下一次得到完全不同的数据

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course1.2.PNG" style="zoom:67%;" />

下面这张图说明了强化学习和其他机器学习方法之间的关系：

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course1.15.PNG" style="zoom:67%;" />

# 强化学习中的基本概念

强化学习入门困难的一点在于其术语非常多且容易混淆，有必要对这些基本概念做详细解释

## Reward

奖励是环境对智能体采取动作做出的反馈，是一个**标量**；它反映了每一步决策的好坏情况；智能体的目标就是最大化累积奖励。

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course1.3.JPG" style="zoom: 80%;" />

强化学习本质上是一个**序贯决策过程**，对于任何一个强化学习任务，需要进行以下的考量

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course1.4.PNG" style="zoom:75%;" />

## Agent and Environment
智能体和环境的交互过程如下：

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course1.5.PNG" style="zoom:67%;" />

* agent观测环境（observation）并获得环境对其以往动作的反馈（reward），以此为输入，根据内置的算法进而采取对应的行动（action）
* observation是外部环境产生的，可被agent观察到的情况，**不一定等同于environment的内部运行机制**，实际上很多情况下我们也不需要详细知道环境是怎么运作的
* 每一个action产生之后会对environment产生作用，影响到下一步骤观测的observation，并且反馈agent相应的reward

## History and State

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course1.6.PNG" style="zoom: 80%;" />

* 历史是截至时间t为止所能观察到的变量信息，是一个包含过去观测、动作、奖励的序列
* 状态是用来规划将来的已有信息，可以看作**是对历史的总结，因此可以被视作关于history的函数**。State相对于history而言最大的好处在于他的信息量很少，实际上，我们也不需要根据所有的历史信息来决策下一步该采取怎样的action（在之后的马尔科夫状态中会提到历史信息的无记忆性）

**3种不同的State**

1. **Environment State** $S_t^e$：是真正的环境所包含的信息，一般对agent并不完全可见。实际上，个体有时候也不需要知道环境状态的全部细节，一是因为agent是根据环境反馈的观测/奖励来修改自己的策略；二是即使环境状态对个体完全可见的，这些信息中也可能包含着一些无关成分；

2. **Agent State** $S_t^a$：包括个体可以使用的，用来决定未来动作的所有信息。我个人理解是Agent自己对Environment State的解读与翻译，它可能不完整，但我们的确是指望着这些信息来运行强化学习算法的决策

3. **Information State**：又称Markov状态，这个概念更多是强调状态的某种性质，与前面的两种State并不形成并列关系。如果现在的状态已经包含了预测未来所有的有用的信息，换句话讲可以丢弃掉历史信息中跟决策影响无关的成分。则它具有马尔科夫性，满足如下定义：

   <img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course1.7.PNG" style="zoom:75%;" />

## Observation and Environment

环境可以分为两种：

**1. Fully Observable Environments**

完全可观察环境下，agent能够直接观察到environment state，即满足图中的等式关系。这是一个很理想化的情况，现实中很多复杂问题是不具备这个条件的。同时根据定义，此时的环境是一个**MDP问题：Markov decision process**。也是强化学习最核心的问题。

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course1.8.PNG" style="zoom:75%;" />

**2. Partially Observable Environments**

部分可观察环境中，Observation state 不等于 environment state，我们只能看到部分信息，或者只能看到一些现象，这也是大多数强化学习问题的场景。这类问题被称为**POMDP问题：partially observable Markov decision process**。所以此时想要解决问题的话Agent必须自己对环境进行解读，自己去探索。对于这类问题David指出一般有以下的解决办法：

* 记住所有的历史状态
* 使用贝叶斯概率，推导出产生当前环境状态的概率，为此我们需要记录所有环境状态的概率值
* 使用递归神经网络，将最近的状态和观测进行组合来推演

![](https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course1.9.PNG)



## Agent的组成要素

Agent涉及到三个组成要素：策略（Policy），价值函数（Value Function）和模型（Model），但要注意这三要素不一定要同时具备。

![](https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course1.10.PNG)

**1. 策略 Policy**

策略是决定个体行为的机制。是**从状态到动作的一个映射**。有具体两种表现形式：

* 确定性的（Deterministic）：在某一特定状态确定对应着某一个行为

* 随机的（Stochastic）：在某一状态下，对应不同行动有不同的概率。随机性的行为更具鲁棒性，因为在一些对抗类的场景下（下围棋），如果agent采取确定性的策略，很容易被对手猜到并作出对付方案

![](https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course1.11.PNG)

**2. 价值函数 Value Function**

* agent在学习过程中需要对面临许多不同的状态，因此要预测某个状态下（或在该状态下状态采取某一动作）可能获得未来的reward的期望值。这个期望值是一个关于状态的函数State-Value function（或关于状态和动作的函数：Action-Value function，在之后笔记的第二章会更详细解释）
* 价值函数**是建立在采取某一个策略基础上的**，在不同的策略下即便处于同一状态的，所获得价值并不相同

![](https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course1.12.PNG)

**模型 Model**

个体对环境的一个建模，相当于agent“脑补”的一个环境。它体现了**个体是如何思考环境运行机制**的（how the agent think what the environment was.），个体希望模型能模拟环境与个体的交互机制。

模型至少要解决两个问题：一是**预测状态转移概率**：另一项工作是**预测环境的反馈（即时奖励）**：

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course1.13.PNG" style="zoom:75%;" />

**[注]**：*模型并不是构建agent所必需的，很多强化学习算法并不试图（依赖）构建一个模型。模型仅针对agent的组成而言，环境实际运行机制称为**环境动力学(dynamics of environment)**，它唯一明确agent下一个状态和所得的即时奖励。*

**Agent的分类**

1. 仅基于价值函数的 **Value Based**：在这样的个体中，有对状态的价值估计函数，但是没有直接的策略函数，策略函数由价值函数间接得到。
2. 仅直接基于策略的 **Policy Based**：这样的个体中行为直接由策略函数产生，个体并不维护一个对各状态价值的估计函数。
3. 演员-评判家形式 **Actor-Critic**：个体既有价值函数、也有策略函数。两者相互结合解决问题。

此外，根据个体在解决强化学习问题时是否建立一个对环境动力学的模型，将其分为两大类：

1. **Model free**: 这类个体并不视图了解环境如何工作，而仅聚焦于价值和/或策略函数。
2. **Model based**：个体尝试建立一个描述环境运作过程的模型，以此来指导价值或策略函数的更新。

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course1.14.PNG" style="zoom:75%;" />



# 强化学习算法中的几组理念

## Learning & Planning

- 学习：**环境初始时是未知的**，个体不知道环境的信息，只能通过和环境来互动得知我们的action会造成什么样的改变，逐渐改善其行为策略。
- 规划: **环境如何工作对于个体是已知或近似已知的**，个体并不与环境发生实际的交互，而是利用其构建的模型进行计算，在此基础上改善其行为策略。Planning 问题可以使用动态规划来解决，在本系列的第三部分我们会具体谈到这个方法。

一个常用的强化学习问题解决思路是，先学习环境如何工作，也就是了解环境工作的方式，即学习得到一个模型，然后利用这个模型进行规划。

## Exploration & Exploitation

强化学习是一个不断试错，然后减少错误率的过程。所以这里存在一个矛盾，当我们存在一个相对比较好的解决方案时，我们应该选择沿用我们的解决方案，还是选择尝试新的未知的路径，这个路径可能有很高的错误率，也可能有更好的解决方案。如何进行两者的平衡，就是Exploration和Exploitation的问题

- Exploration(探索)：倾向于探索环境中新的信息
- Exploitation(利用)：倾向于利用已知的信息来获取最大reward

## Prediction & Control

在强化学习里，我们经常需要先解决关于预测（prediction）的问题，而后在此基础上解决关于控制（control）的问题。实际上，这两者是递进的关系。

- prediction：**给定一个policy**，评价遵循该策略下agent能获得多大的奖励，可以看成是求解在给定策略下的价值函数（value function）的过程。

- control：**在没有policy的前提下**，直接找出一个好的策略来最大化未来的奖励。



