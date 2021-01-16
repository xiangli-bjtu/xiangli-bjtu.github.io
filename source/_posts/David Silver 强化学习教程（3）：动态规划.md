---
title: David Silver 强化学习教程（3）：动态规划
date: 2020-05-28 22:20:08
index_img: /img/post_index_img/david silver.jpg
math: true
tags: 
- Reinforcement Learning
- AI
categories: 技术
---

从广义上讲，强化学习是序贯决策问题。在上一节，我们已经将强化学习纳入到马尔科夫决策过程MDP的框架之内，根据是否建立了环境模型（即状态转移概率），可以分为**基于模型的动态规划方法和基于无模型的强化学习方法**。

DP算法在增强学习领域应用十分有限，因为它们不仅要求理想的模型，同时计算量也非常大，但是在理论方面依然非常重要。 DP算法为本书后面章节的理解提供了必要的基础，强化学习所有的方法都可以看成是为了实现和DP相似的效果，只是弱化已知精确环境模型的假设或者计算量更小。



# 动态规划 

动态规划算法是解决复杂问题的一个方法，算法通过把复杂问题分解为子问题，通过求解子问题进而得到整个问题的解。在解决子问题的时候，其结果通常需要存储起来被用来解决后续复杂问题。当问题具有下列特性时，通常可以考虑使用动态规划来求解：

- 最优子结构(Optimal substructure)：意味着我们的问题可以拆分成一个个的小问题，通过解决这个小问题，最后，我们能够通过组合小问题的答案，得到大问题的答案
- 重叠子问题(Overlapping subproblems)：子问题在复杂问题内重复出现，使得子问题的解可以被存储起来重复利用。

马尔科夫决定过程（MDP）满足上述的两个属性，可以使用动态规划来求解MDP

* Bellman方程把问题递归为求解子问题，
* 价值函数就相当于存储了一些子问题的解，可以复用。

**动态规划求解MDP**

使用动态规划解决MDP问题时，通常假设环境是有限马尔可夫决策过程。也就是说，我们假设环境的状态，动作，和奖励集合有限，且给出了他们的动态特性即转移概率

* 动态规划应用于MDP的**规划问题(planning)**而不是学习问题(learning)，我们必须**对环境是完全已知的(Model-Based)**，才能做动态规划
* 动态规划对于prediction（评估策略）和control问题（找到最优策略可分为策略迭代和价值迭代）均能求解（定义请看第一章）

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course3.1.JPG" style="zoom:67%;" />



# 策略评估求解预测问题

首先要介绍的是策略评估，**策略评估就是给定任意策略 $\pi$怎么判断该策略到底有多好即计算其状态值函数**。这个评价由基于当前策略的值函数 $v_{\pi}(s)$衡量。下面是整个流程：

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course3.2.JPG" alt="david_course3.2" style="zoom: 80%;" />

1. 给定要评估的策略 $\pi$ 和MDP中所有状态的 value function $v_1$，一般是全部置为0。这个初始化不会影响迭代结果，并且它不会影响收敛速率。

2. 如果要获得状态 $s$ 的价值函数，需要看在该状态下通过策略 $\pi$ 其状态能转移到了哪些后续状态式 $s^{'}$ ；而在具体要计算的时候，利用第 $k$ 步迭代得到的这些后续状态 $s^{'}$ 的价值函数，带入Bellman Expectation Equation，得到新一轮 $k+1$ 步迭代的价值函数

   <img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course3.3.JPG" style="zoom:67%;" />

3. 反复第2步一直到价值函数值恒定不变，这个迭代过程的结果 $v_{\pi}$ 就是对当前策略的评估

   

**示例：Evaluating a Random Policy in the Small Gridworld**

在这节课程中用到的例子是一个最快达到网格中的灰色点，我们从最开始的随机策略开始，对其进行policy evaluation

![](https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course3.4.JPG)

- 状态空间 S：图中灰色方格所示两个位置为终止状态
- 行为空间 A：{n, e, s, w} 对于任何非终止状态可以有向北、东、南、西移动四个行为
- 转移概率 P：任何试图离开方格世界的动作其位置将不会发生改变，其余条件下将100%地转移到动作指向的状态（即在边界格子时，任何试图冲出边界的行为都会维持在原地，其他情况下移动不会出现打滑走对角线的可能）
- 即时奖励 R：任何在非终止状态间的转移得到的即时奖励均为-1，进入终止状态即时奖励为0
- 衰减系数 $\gamma$：1
- 初始化为随机策略，对于任何非终止状态可以等概率朝4个方向移动，目标是最快达到灰色格子的状态

迭代过程的详细步骤可以参考知乎叶强大佬的文章： [迭代法评估4*4方格世界下的随机策略](https://zhuanlan.zhihu.com/p/28084990)

![](https://pic3.zhimg.com/v2-0082e5876e2ea59bdcedc7912bb15a3a_b.jpg)

可以看到，动态规划的策略评估计算过程并不复杂，但是如果我们的问题是一个非常复杂的模型的话，这个计算量还是非常大的。

> 除了通过迭代进行策略评估外，这个视频还提到了解析解的方法，感兴趣的可以参考https://www.bilibili.com/video/BV1nV411k7ve?p=3



# 策略迭代求解控制问题

现在我们正式介绍最优策略的解法之一，**Policy Iteration策略迭代**，它包括上面介绍的策略评估（policy evaluation）和策略改进（policy improvement）两个步骤。

现在我们已经通过策略评估确定了一个确定性策略$\pi$的状态价值函数，那么接下来的问题就是：是否可以找到一个更好的策略$\pi^{'}$。最直接的办法就是对$\pi^{'}$也进行策略评估得到状态价值函数，但这本身是个很耗时的工作。

下面要介绍的策略改进定理提供了更加简洁的思路：我们无需对新的策略$\pi^{'}$直接求价值函数，而是考虑在状态 $s$下强制执行动作 $a=\pi^{'}(s)$（注意在当前策略$\pi$下并不一定会采取这个动作$a$），然后遵从现有的策略$\pi$并计算出若$q{\pi}(s,\pi^{'}(s))$的值。若相比于$V_{\pi}(s)$更大，新的策略事实上总体来说也会比较好。

我们一般采取贪婪策略提升的方法，即新的确定性策略 $\pi^{'}$是根据当前状态$s$下所有动作中，贪婪地选使得后继状态价值增加最多的行为（即利用评估得到的$V_{\pi}(s)$求出$q{\pi}(s,\pi(s))$并从中选出最大的一项更新策略）

下面图展示Policy Iteration的过程：

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course3.8.JPG" style="zoom:67%;" />

现在我们从理论的角度，来给出Policy Iteration一定会收敛到最优值函数和策略的证明：

* 采取贪婪策略提升的方法得到新的策略

![](https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course3.9.JPG)

* 在新的策略 $\pi^{'}$ 下，可以得到下述的不等式关系

![](https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course3.10.JPG)

![](https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course3.11.JPG)

* 当policy improvement 停止的时候，上述的不等式关系变成全等式。此时也意味着bellman optimal Equation 被满足。找到了最优的策略

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course3.12.JPG" style="zoom:67%;" />

在small gridworld的例子中结合贪婪策略提升的方法，可以看出策略的不断提升

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course3.5.JPG" style="zoom:67%;" />

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course3.6.JPG" style="zoom:67%;" />

很多时候，**策略的更新较早就收敛至最优策略，而状态价值的收敛要慢很多**，是否有必要一定要迭代计算直到状态价值得到收敛呢？

从上面方格的例子中，我们可以看出来$k=3$的时候，策略已经达到最优，虽然此时还没有满足Bellman Optimal Equation的条件，但是最优的policy已经出来了。所以，在这之后我们做的一次次迭代实际上是无用功，Modified Policy Iteration希望解决的就是砍掉这些无用的迭代的过程，我们有以下解决方案：

- 引入变量 $\epsilon $ 作为停止条件，在精度允许范围内即可结束
- 设定一个固定的策略评估次数$k$，策略评估在$k$次迭代之后就截至。事实上当$k=1$的时候，Policy Iteration算法就变成了接下来介绍的Value Iteration



# 价值迭代求解控制问题

策略迭代存在一个问题，就在于**策略迭代每次迭代都需要进行策略评估，主要时间都花费在策略评估上，而策略评估本身可能需要多次迭代才能收敛**，对一个简单的问题来说，在策略评估上花费的时间不算长；但对复杂的问题来说，这个步骤的时间实在有些长。那么是否可以提前结束策略评估呢？这就引出了下面要介绍的价值迭代。

**最优策略原则**

一个最优的策略，我们可以从两步来思考：

- 从状态 $s$ 到下一个状态 $s^{'}$，采取了最优的动作
- 后继状态每一步都按照最优的policy去做，那么我最后的结果就是最优的

一个策略能够使得状态s获得最优价值，当且仅当：**对于从状态s可以到达的任何状态s’，该策略能够使得状态s’的价值是最优价值：**

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course3.13.JPG" style="zoom:80%;" />

我们可以知道我们期望的最终（goal）状态的位置以及反推需要明确的状态间关系，认为它是一个确定性的价值迭代**。**因此，我们可以把问题分解成一些列的子问题，**从最终目标状态开始分析，逐渐往回推，直至推至所有状态。**

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course3.14.JPG" style="zoom:67%;" />

**价值迭代流程**

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course3.16.JPG" style="zoom:67%;" />

有关策略迭代和价值迭代的对比：[策略迭代和价值迭代](https://zhuanlan.zhihu.com/p/26699028)



# 小结

我们将这节课学到的一种Prediction方法和两种Control的方法信息总结一下：

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course3.17.JPG" style="zoom:67%;" />

上图中时间复杂度的分析，$m$为可选动作，$n$为MDP中的可能状态。

进一步我们对比下价值迭代和策略迭代，这里借用Stackoverflow的一张图：

![](https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course3.18.JPG)

DP方法的一个特点是：所有的更新都依赖其后继状态的估计值，这称为自举（bootstrapping）。另一个特点就是开篇提到的需要精确知道环境模型。

对于非常大的问题，DP可能不实用，但与其他解决MDP的方法相比，DP方法实际上非常有效。在接下来的两节笔记中，会介绍不需要模型也不需要自举的方法——蒙特卡洛法；不需要模型但是需要自举的方法——时间差分法。



# 补充内容：异步动态规划算法

在前几节我们讲的都是同步动态规划算法，即每轮迭代我会计算出所有的状态价值并保存起来，在下一轮中，我们使用这些保存起来的状态价值来计算新一轮的状态价值。

另一种动态规划求解是异步动态规划算法，在这些算法里，每一次迭代并不对所有状态的价值进行更新，而是依据一定的原则有选择性的更新部分状态的价值，这类算法有自己的一些独特优势，当然有额会有一些额外的代价。

常见的异步动态规划算法有三种：

* 第一种是原位动态规划 (in-place dynamic programming)， 此时我们不会另外保存一份上一轮计算出的状态价值。而是即时计算即时更新。这样可以减少保存的状态价值的数量，节约内存。代价是收敛速度可能稍慢。
* 第二种是优先级动态规划 (prioritised sweeping)：该算法对每一个状态进行优先级分级，优先级越高的状态其状态价值优先得到更新。通常使用贝尔曼误差来评估状态的优先级，贝尔曼误差即新状态价值与前次计算得到的状态价值差的绝对值。这样可以加快收敛速度，代价是需要维护一个优先级队列。
* 第三种是实时动态规划 (real-time dynamic programming)：实时动态规划直接使用个体与环境交互产生的实际经历来更新状态价值，对于那些个体实际经历过的状态进行价值更新。这样个体经常访问过的状态将得到较高频次的价值更新，而与个体关系不密切、个体较少访问到的状态其价值得到更新的机会就较少。收敛速度可能稍慢