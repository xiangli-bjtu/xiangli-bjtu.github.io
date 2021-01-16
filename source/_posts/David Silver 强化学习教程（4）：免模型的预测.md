---
title: David Silver 强化学习教程（4）：免模型的预测
date: 2020-06-02 10:25:51
index_img: /img/post_index_img/david silver.jpg
math: true
tags: 
- Reinforcement Learning
- AI
categories: 技术
---

上一讲介绍了在掌握MDP细节（即时奖励和转移概率，或者说对环境建立了模型）下如何通过动态规划求解最优价值函数和最优策略。但是由于动态规划法需要在每一次回溯更新某一个状态的价值时，回溯到该状态的所有可能的后续状态。导致对于复杂问题计算量很大。同时很多时候，我们对于环境模型是无法知道的，这时动态规划法根本没法使用。这时候我们如何求解强化学习问题呢？这正是强化学习要解决的问题，通过直接从Agent与环境的交互来得到一个估计的最优价值函数和最优策略。

本讲的内容聚焦于策略评估也就是model-free prediction；下一讲将利用本讲的主要观念来进行model-free control进而找出最优策略，最大化Agent的奖励。



# Monte-Carlo Reinforcement Learning

## 蒙特卡罗算法

蒙特卡罗法并不是一种算法的名称，而是对一类随机算法的特性的概括。既然是随机算法，在采样不全时，通常不能保证找到最优解，只能说是尽量找。那么根据怎么做的“尽量”，我们可以把随机算法分成两类：

* 蒙特卡洛算法：随机采样的越多，得到的结果**越近似最优解**（概率越大）；
* 拉斯维加斯算法：另一类随机算法的思路是采样越多，越**有机会找到最优解**。

这两个词本身是两座著名赌城，因为赌博中体现了许多随机算法，所以借过来命名。这两类随机算法之间的选择，往往受到问题的局限。如果问题要求在有限采样内，必须给出一个解，但不要求是最优解，那就要用蒙特卡罗算法。反之，如果问题要求必须给出最优解，但对采样没有限制，那就要用拉斯维加斯算法。

关于蒙特卡罗的例子可以看看这个视频（给了5个案例）：[蒙特卡罗 Monte Carlo](https://www.youtube.com/watch?v=XRGquU0ZJok)

## 蒙特卡罗强化学习

蒙特卡洛强化学习指：指在不清楚MDP具体细节的情况下，直接基于某个策略从某个状态开始，让个体与环境交互经历完整的状态序列 (episode) ，某状态的价值等于在多个状态序列中以该状态算得到的所有return 的平均。这里所谓**完整的状态序列 (complete episode)**并不要求起始状态一定是某一个特定的状态，但是要求个体最终进入环境认可的某一个终止状态。从这里也能看出MC的局限性在于**要求MDP过程存在终止状态**

**Monte-Carlo Policy Evaluation 的流程**

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course4.1.JPG" style="zoom:67%;" />

可以看出，预测问题的求解思路还是很简单的。核心思想就是**用平均收获值代替价值（value = empirical mean return）**，理论上Episode越多，结果越准确。不过有几个点可以优化考虑：

* 第一个考虑的点在于：同样一个状态可能在一个完整的状态序列中重复出现，那么该状态的收获该如何计算？

一种办法是仅把状态序列中第一次出现该状态时的收获值纳入到收获平均值的计算中，这被称为 Fist-Visit Monte-Carlo Policy Evaluation

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course4.2.JPG" style="zoom:80%;" />

另一种是针对一个状态序列中每次出现的该状态，都计算对应的收获值并纳入到收获平均值的计算中，这被称为 Every-Visit Monte-Carlo Policy

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course4.3.JPG" style="zoom:80%;" />

第二种方法比第一种的计算量要大一些，但是在完整的经历样本序列少的场景下会比第一种方法适用。



- 另一个需要考虑的点在于：上面预测问题的求解公式里，我们有一个average的公式，意味着要保存所有该状态的收获值之和最后取平均。这样浪费了太多的存储空间。一个较好的方法是empirical mean的更新可以被写成Incremental Mean的方式，在实际操作时更新平均收获时，不需要存储所有既往收获，而是每得到一次收获，就计算其平均收获。

通过下面的公式可以更好的理解（相当于在上一次数值的基础上，加上一个带学习率的残差进行更新）：

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course4.4.JPG" style="zoom:67%;" />

把这个方法应用于蒙特卡洛策略评估，就得到下面的蒙特卡洛累进更新。在处理非静态问题时，使用这个方法跟踪一个实时更新的平均值是非常有用的，还可以引入参数 $\alpha$ 每得到一轮episode后更新状态价值的速度：

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course4.5.JPG" style="zoom:80%;" />

以上就是蒙特卡洛学习方法的主要思想和描述，蒙特卡洛学习方法实际应用并不多。接下来着重介绍实际常用的TD学习方法。



# Temporal-Difference Learning

## TD算法流程

时序差分学习简称TD学习，它的特点如下：和蒙特卡洛学习一样，它也从Episode学习，不需要了解模型本身；但是它可以学习**不完整**的Episode，通过**自举（bootstrapping）**，猜测Episode的结果，同时持续更新这个猜测。

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course4.6.JPG" style="zoom:80%;" />

所谓bootstrapping，就是TD算法在估计某一个状态的价值时，用到了以前估计的值，这里很类似Bellman方程的形式。图中TD target包括了模型对价值函数的预测值和环境的实际奖励，对于这个概念的理解可以参考这个视频（第二节） https://www.bilibili.com/video/BV1BE411W7TA?p=2

## n-step TD

先前所介绍的TD算法实际上都是TD(0)算法，括号内的数字0表示的是在当前状态下往前多看1步，要是往前多看2步更新状态价值会怎样？这就引入了n-step的概念。

对于n步时序差分来说，和普通的时序差分的区别就在于收获的计算方式的差异。那么既然有这个n步的说法，那么n到底是多少步好呢？如何衡量n的好坏呢？我们将在下一讲讨论。

## MC与TD的对比

**（1）**TD 在知道最终结果之前可以学习，MC必须等到最终结果才能学习；TD 可以在没有终止状态时的持续进行的环境里学习。

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course4.8.JPG" style="zoom:80%;" />

**（2）** 偏差/方差分析：这也很好理解，MC的方式会经历更为长时间的状态过程，MDP的随机性会对经验平均带来大的方差；而对于TD变换的主要原因在于的即时奖励，方差自然会小但是如果即使奖励不准确会使得对价值函数的估计出现偏差

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course4.9.JPG" style="zoom:80%;" />

**（3）** 通过比较可以看出，TD算法使用了MDP问题的马尔可夫属性，在Markov 环境下更有效；但是MC算法并不利用马尔可夫属性，通常在非Markov环境下更有效。

![](https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course4.10.JPG)

# 三种强化学习算法对比

Monte-Carlo, Temporal-Difference 和 Dynamic Programming 都是计算状态价值的一种方法，区别在于，前两种是在不知道Model的情况下的常用方法，这其中又以MC方法需要一个完整的Episode来更新状态价值，TD则不需要完整的Episode；DP方法则是基于Model（知道模型的运作方式）的计算状态价值的方法，它通过计算一个状态S所有可能的转移状态S’及其转移概率以及对应的即时奖励来计算这个状态S的价值。

下面的几张图直观地体现了三种算法的区别：

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course4.11.JPG" style="zoom:67%;" />

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course4.12.JPG" style="zoom:67%;" />

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course4.13.JPG" style="zoom:67%;" />

**关于是否Bootstrapping：**MC 只使用实际收获；DP和TD使用了

**关于是否用样本来计算:** MC和TD都是应用样本来估计实际的价值函数；而DP则是利用模型直接计算得到实际价值函数，没有样本或采样之说。

![](https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course4.14.JPG)

从**采样深度和广度**两个维度来解释了四种算法的差别，多了一个穷举法。

* 当使用单个采样，同时不走完整个Episode就是TD；
* 当使用单个采样但走完整个Episode就是MC；
* 当考虑全部样本可能性，但对每一个样本并不走完整个Episode时，就是DP；
* 当既考虑所有Episode又把Episode从开始到终止遍历完，就变成了穷举法。

![](https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course4.15.JPG)

