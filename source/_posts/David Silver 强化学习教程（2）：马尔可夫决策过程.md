---
title: David Silver 强化学习教程（2）：马尔可夫决策过程
date: 2020-5-15 20:22:09
index_img: /img/post_index_img/david silver.jpg
math: true
tags: 
- Reinforcement Learning
- AI
categories: 技术
---

# 马尔科夫过程

**Markov Property**

是随机过程中的概念，因俄国数学家马尔可夫的研究而得名。它表明在给定现在状态及所有过去状态情况下，只要当前状态可知， 就可以决定未来状态的条件概率分布，所有的历史信息都不再需要（即一个**无记忆**的随机过程）。我们称当前状态具有**马尔科夫性**，用下面的公式化描述来说明马尔科夫性：

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.1.PNG" style="zoom:75%;" />

**Markov Chain**

对于一组状态和时间都有限，且满足马尔可夫性质的随机过程，称**马尔可夫过程（Markov Process**）或**马尔科夫链（Markov Chain）**。通过一个**元组<S,P>**表示，其中S是状态的集合，P是状态转移概率矩阵。

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.2.PNG" style="zoom:75%;" />

**示例——学生马尔科夫链**

David的课程中，将多次用到下面这个学生马尔科夫过程的案例，来解释有关概念和计算。

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.3.PNG" style="zoom: 80%;" />

可以看到从某一状态开始，其后的过程根据状态转移矩阵存在多种可能情况，在强化学习中这被称为**episode或者trajectory**

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.4.JPG" style="zoom: 67%;" />

** 为什么在强化学习中要引入马尔可夫性**

我们在上节提到了环境的状态转化模型，它可以表示为一个概率模型$P_{ss^{''}}^{a}$。显然在真实的环境状态转移不仅和前一状态有关，还与上上个以及更早前的状态相关，这一会导致我们的环境转化模型非常复杂难以建模。因此我们需要在强化学习的环境转移模型中进行简化。这个办法就是假设状态转化的马尔科夫性。

马尔科夫过程（MP）被用于**对完全可观测的环境进行描述**，而几乎所有的强化学习问题都可以被视作为接下来要介绍的马尔可夫决策过程（MDP，即使部分可观测环境问题也可以转化为POMDP），因此理解从马尔可夫性到马尔科夫决策过程是理解强化学习问题的基础。



# 马尔科夫奖励过程 

现在我们逐渐加入强化学习中的一些要素来拓展马尔科夫过程，首先在马尔科夫过程的基础上**增加了奖励R和衰减系数γ**，就得到了马尔科夫奖励过程 

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.5.PNG" style="zoom:75%;" />

## Reward

**奖励函数**：需要注意一下David在这里的表述：在时间点 $t$, agent处于状态 $s$ ，而在 $t+1$ 时刻获得环境反馈的奖励 $R_{t+1}$，因为$s$ 转移过去的下一状态是随机的，因此这里对奖励值取了一次期望得到$R_s$。照此理解起来相当于**离开当前状态 $s$ 获得了奖励**，David指出这是本门课程的习惯表示，如果把奖励改为 **$R{t+1}$** 只要在表述上描述成：$t+1$时刻进入某个状态 $s^{'}$ 获得的相应奖励即可，本质上意义是相同的。

下图是例子中各个状态的即时奖励情况

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.6.PNG" style="zoom: 80%;" />

## Discount Factor

**折扣因子** $\gamma$ 介于 [0, 1]区间，David列举了不少理由来解释为什么引入衰减系数：

* 避免在循环或者无限的MDP过程中，产生无穷大或者无穷小的值的值函数，陷入无限循环
* 金融学上，立即回报可以赚取比延迟汇报更多的利息，因而更有价值
* 远期利益具有一定的不确定性，符合人类和动物更偏爱对立即利益的追求，折扣因子用来模拟这样的认知模式

## Return

译为**“收益”或"回报"**，定义为在一个马尔科夫奖励链上从 $t$ 时刻开始，往后所有奖励的有衰减的总和。其中衰减系数体现了对未来的奖励的重视程度。$\gamma$ 接近0，则表明趋向于“近视”性评估；$\gamma$ 接近1则表明偏重考虑远期的利益。

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.7.PNG" style="zoom:75%;" />

结合上面提到的那张学生马尔可夫链说明Return的计算

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.8.PNG" style="zoom:75%;" />

从上图也可以理解到，收益是针对一个马尔科夫链中的**某一具体的状态转移过程**来说的。

## State Value Function

回报是个随机值，其随机性来源于策略本身和环境。为了评价观测到某一状态的价值，引入价值函数的定义：**从该状态出发，后续所有可能的状态转移过程的return的期望**

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.9.PNG" style="zoom:75%;" />

## Bellman Equation

根据Value Function的定义，可以将其拆分为以下两部分：

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.10.PNG" style="zoom:67%;" />

* 第一部分是该状态的即时奖励期望
* 另一部分是进入下一可能状态的价值函数值的期望，可以根据状态转移矩阵的概率分布得到

下图很好地阐述了贝尔曼方程的过程：

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.11.PNG" style="zoom:75%;" />

以学生马尔可夫过程为例，可以得到如下的推演：

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.12.PNG" style="zoom: 80%;" />

根据系统的n个状态，不难得到整个马尔可夫奖励过程Bellman方程的矩阵形式：

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.13.PNG" style="zoom: 67%;" />

这本质上是一个线性方程组，可以直接得到解析解：

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.14.PNG" style="zoom:75%;" />

可以看到直接求解的复杂度是$O(n^3)$，仅适用于小规模的MRP，当状态数目很大时矩阵的求逆会非常困难。大规模MRP的求解通常使用迭代法。常用的迭代方法有：动态规划Dynamic Programming、蒙特卡洛评估Monte-Carlo evaluation、时序差分学习Temporal-Difference，后文会逐步讲解这些方法。



# 马尔科夫决策过程

在马尔科夫奖励过程基础上增加**动作集合A**，就得到了马尔科夫决定过程，它是这样的一个5元组: 

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.15.PNG" style="zoom:75%;" />

引入Action之后，最重要的区别在于：原本MRP中全部由概率表示的过程，现在我们有了更多的控制权，这个决策是由agent来决定的；另一个方面，**agent的决策并不能直接决定他下一步进入哪个状态，而是由action和environment共同决定**。

下图给出学生状态的MDP过程，图中红色的文字表示的是采取的行为，而不是先前的状态名。对比之前的学生MRP示例可以发现，同一个状态 $s$下采取不同的行为，得到的环境奖励 $R_s^a$ 是不一样的。

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.16.PNG" style="zoom:75%;" />

## Policy

策略$\pi$是一个概率分布或集合，其元素$\pi(a|s)$代表**在给定状态$s$下采取可能action的可能性**。

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.17.PNG" style="zoom:75%;" />

- 一个策略完整定义了个体的行为方式，也就是说定义了个体在各个状态下，所采取的可能行为方式及其概率
- 在具有Markov性质的决策中，概率分布只取决于当前的状态，与历史无关
- 某一确定的Policy是与时间无关，或者说静态的，只和当前面临状态有关。但是个体可以利用算法更新Policy

> 注意策略是静态的、关于整体的概念，不随状态改变而改变。变化的是在某一个状态时，依据策略可能产生的具体行为。因为具体的行为是有一定的概率的，策略就是用来描述各个不同状态下执行各个不同行为的概率。

## 理解MP、MRP、MDP的联系

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.18.PNG" style="zoom:75%;" />

* 给定一个MDP和策略$\pi$
* MDP中的状态序列可以构成一个马尔可夫过程
* 状态和奖励序列组成一个马尔可夫奖励过程
* 在这个过程中满足两个方程：
  1. 在执行某个策略下，状态$s$转移到$s'$发生的概率，等于执行某一个行为的概率与该行为能使状态从$s$转移至$s'$的概率的乘积之和
  2. 同理，当前状态$s$下执行某一指定策略得到的即时奖励，是该策略下所有可能行为得到的奖励与该行为发生的概率的乘积之和

## MDP下的两种Value Function

在MDP中, 价值函数可以用来描述针对状态的价值，也可以描述某一状态下执行某一动作的价值。对应**状态价值函数和动作价值价值函数**(严格意义上应该叫状态-动作价值函数的简写)。

需要注意的是这两种**价值函数的定义都是建立在确定的策略$\pi$上**

1. **基于策略$\pi$的状态价值函数 $v_\pi(s)$**：表示从状态s开始，遵循策略时所获得的期望收益；反映在执行当前策略$\pi$时，个体处于状态s时的价值大小

2. **基于策略$\pi$的动作价值函数 $q_\pi(s,a)$**：遵循策略在状态$s$下执行某一具体动作a所获得的期望收益；或者说在遵循策略$\pi$时，衡量对当前状态执行行为a的价值大小。行为价值函数一般都是与某一特定的状态相对应的。

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.19.PNG" style="zoom:75%;" />

> 这里需要注意的是关于状态$s$和动作$a$的对应关系，在状态价值函数中后续所有可能产生的$G_t$，受到所采取的策略$\pi$的限制（确定性策略的话期望符号其实可以取消，随机性策略仍然是做了期望加权）；而对于动作价值函数，应该理解为在当前状态$s$下强制执行动作$a$（可能依据当前的策略$\pi$并不会采取该动作，当然后续的状态转移是受到策略影响的）。

**MDP两种价值函数的关系**

图中空心较大圆圈表示状态，黑色实心小圆表示的是动作本身。可以看出，在遵循策略$\pi$时，状态s的价值体现为在该状态下，遵循某一策略而采取所有可能行为的价值按行为发生概率的乘积求和

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.21.PNG" style="zoom: 67%;" />

类似的，一个行为价值函数也可以表示成状态价值函数的形式。它表明某一个状态s下采取一个行为a的价值可以分为两部分：其一是离开这个状态的立即奖励，其二是所有进入新的状态的价值与其转移概率乘积的和。

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.22.PNG" style="zoom:67%;" />

## Bellman Expectation Equation

上述状态价值函数和动作价值函数之间关系的两个式子结合起来，就能得到MDP下的贝尔曼期望方程（Bellman Expectation Equation）

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.23.PNG" style="zoom:67%;" />

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.24.PNG" style="zoom:67%;" />

## MDP的最优求解

解决强化学习问题意味着要寻找一个最优的策略让个体在与环境交互过程中获得始终比其它策略都要多的收获，一旦找到这个最优策略我们就解决了这个强化学习问题。

**Optimal Value Function**

首先需要对**最优状态价值函数和最优行为价值函数**给出定义：

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.25.PNG" style="zoom: 80%;" />

可见最优状态价值函数/最优动作价值函数是所有策略下产生的众多状态价值函数中的最大者

**Optimal Policy**

* 首先需要定义什么是最优策略;

当对于任何状态$s$，遵循策略$\pi$的价值不小于遵循策略$\pi'$下的价值，则策略$\pi$优于策略$\pi'$：

![](https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.26.PNG)

对于任何MDP，下面几点成立：

1. 存在一个最优策略，比任何其他策略更好或至少相等
2. 可能不止一个最优策略，如果有多个最优策略存在，那么对于任意的状态$s$，所有的最优策略有相同的最优价值函数值$v_*(s)$
3. 与2同理，所有的最优策略具有相同的行为价值函数$q_*(s,a)$

* 寻找最优策略

MDP的最优策略可以根据最优行为价值函数$q_*(s,a)$来确定，我们之前说到Bellman Expectation Equation中的策略都是关于某一状态的动作概率分布，而对于最优策略则是在某一状态下采取能获得最大action-value的动作（即argmax操作，使得原本的随机动作变成确定性动作）

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.27.PNG" style="zoom:80%;" />

**Bellman Optimality Equation**

当取得最优策略时，在上面提到的4个Bellman Expectation Equation需要进行改动，得到下面Bellman Optimality Equation（把里面的求状态期望改成取argmax的操作）

<img src="https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/david_course2.28.PNG" style="zoom:80%;" />

虽然MDP可以直接用方程组来直接求解简单的问题，但是更复杂的问题却没有办法求解，因此我们还需要寻找其他有效的求解强化学习的方法。下一篇讨论用动态规划的方法来求解强化学习的问题。