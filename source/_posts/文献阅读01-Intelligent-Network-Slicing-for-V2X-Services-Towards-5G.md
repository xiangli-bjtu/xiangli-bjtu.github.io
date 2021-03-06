---
title: '[论文阅读02]Intelligent Network Slicing for V2X Services Towards 5G'
date: 2020-08-11 11:00:55
tags: V2X
categories:
- 论文阅读
---

> 论文出处：[*《Intelligent Network Slicing for V2X Services Towards 5G》*](https://arxiv.org/abs/1910.01516)*（IEEE Network 2019，中科院SCI期刊分区 Q1，来自北邮的研究团队）*

## Abstract

摘要里大概谈了这么几点：

- 得益于目前广泛部署的LTE基础设施，5G网络对于推进车联网发展起了至关重要的作用（即NR-V2X）
- 现有LTE网络满足不了严格而动态的V2X业务，克服这一问题的有效方法是网络切片。通过切片可以实现在通用物理基础架构之上构建多个逻辑网络，以支持不同的V2X业务
- 为缓解5G网络切片日益复杂的问题，作者探讨了AI技术在自动化网络操作的使用。具体来说，这篇文章里提出了一个V2X业务的智能网络切片架构，在这一架构里将网络功能（VNF）和多维网络资源（3C资源，即computing + communication + caching）虚拟化，并分配到不同的网络切片。为智能地实现最优的切片，包括移动数据收集和ML算法设计在内的几个关键问题将被探讨，以。然后，我们开发了一个仿真plAI能为实现网络的自动化操作发挥其优势，充分调度各项资源实现各类V2X业务的QoS需求
- 设计了一个仿真，来说明作者提出的智能网络切片的有效性（我比较好奇这个，综述看多了还是想落回到怎么开展仿真）

## 1. INTRODUCTION

伴随自动驾驶技术和车辆数量的增长，未来会催生出大量的V2X业务，这些业务大体上可以分为：safety releted和entertainment related（感觉可以理解为车联网背景下的URLLC和eMBB业务）。这些业务在数据速率、可靠性以及时延存在等性能上着多样性的需求。例如，自动驾驶需要低于10毫秒的通信延迟，并且通信可靠性为99.999%；相比之下，娱乐业务主要关注高数据速率，对通信延迟和可靠性的容忍度较高。尽管LTE为支持V2X业务已经做了一定工作（比如3gpp在R14标准化工作里引入了LTE-V2X的概念），但LTE网络本质上还是个“one size fits all”（中文好像翻译成一刀切？）的架构，不能很好满足多样化的V2X业务要求。

5G比4G引入了很多的新技术，除了在空口上的NOMA、Massive MIMO等，网络的软件化、虚拟化对于满足各项V2X业务也是较为关键的技术。

1. 网络软件化旨在提供高度灵活的端到端通信，SDN和NFV是两项关键的技术。SDN可以提供全局视角的网络信息，以及实现可编程化的网络控制；NFV则使得网络功能和各种资源不再被限制于专用的物理设备内。通过无线网络软件化，移动网络运营商可以依据业务要求定制专用逻辑网络，以更好地保证业务的QoS需求，这种逻辑网络即网络切片。网络切片横跨接入网和核心网中功能的灵活配置。显然，网络切片的优势十分适用于车联网环境，满足不同V2X业务的差异化需求。
2. 另外，由于车联网的高复杂性和网络拓扑的高动态性，对车联网网络环境的精准感知和快速决策响应变得非常具有挑战。与传统系统／网络设计方法相比（？这个具体是啥有人懂吗），AI技术可以自动提取网络动态并根据历史观察做出决策，无需对系统需要专业和完善的知识，可应用于无线网络的不同领域。因此，有必要利用AI技术来促进无线网络的部署和管理，实现5G网络的自动化和自进化。

基于以上，为V2X业务提供高度自动的网络切片非常有前景。利用人工智能技术，我们可以智能地提取具有复杂结构和内部关联的网络切片的高层模式，而这些通过传统的基于模型的方法很难实现。因此，这篇文章的研究范围是弥补网络软件化和网络智能化之间的差距，对基于AI的5G网络片架构在车载网络V2X业务中的应用进行了初步研究，主要要回答两个问题：

- 如何设计一种灵活的网络切片体系结构，对车辆网络中各种异构资源进行虚拟化，用来实现网络切片?
- 如何利用先进的AI方法定制网络切片，同时考虑到车辆网络的具体需求

（INTRODUCTION看完后还是很虚，这种有关网络架构的综述性文章一般都这样，AI、SDN、VNF、网络切片等比较热门的词都会涉及到，但限于本人的知识范畴，目前还没有找到哪篇文章能把这些繁杂的概念梳理得令人眼前一亮的，接着往下看吧：）

## 2. DIVERSE REQUIREMENTS OF V2X SERVICES

V2X根据通信对象不同，主要包括了Vehicle-to-Pedestrian (V2P), Vehicle-to-Vehicle (V2V), Vehicle-to-Infrastructure (V2I) 和Vehicle-to-Network (V2N)四种通信模式，如图所示：

![img](https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/v2-497bb425f6cfb0cc27d6138e2dc001ef_b.jpeg)

这篇文章的作者把车联网所支持的业务分为了以下3类，并且给出了各自QoS的对比：

![img](https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/v2-2a9e01603d8619d4cf3f4cf45706ef9f_b.jpeg)

> (不过有一点，这篇文章里单独把自动驾驶和交通安全拆开来看是出于啥考虑？自动驾驶所涉及的不应该都和安全相关吗？看了看别的论文会有这样分的：以车辆自动驾驶为核心的交通安全类应用，和非安全应用；后者又可分为以用户体验为核心的信息服务类应用，和以协同为核心的交通效率类应用，感觉这个逻辑上来讲是不是更通点？） * 交通安全类应用：主要与车辆行驶过程中的智能化决策相关，这类应用中的大部分将能够在车辆之间发送和接收数据，而不需要它们与远程云服务器进行连接。这种通信有效地将公路上的每辆车变成其他车辆传感器的延伸，为高速公路环境中提供最佳信息； * 信息服务类应用：是蜂窝移动通信业务面向新的车辆终端的延伸，包括车载视频、车载AR/VR、车载视频通话、车载智慧家庭、路径导航等; * 交通效率类应用：构建智慧城市的重要环节，智能地监控交通运行状况，实现智能的道路监控，缓解交通环境拥堵以期提高交通效率，达到车、路、环境之间的大协同。

不管哪一种划分的角度，面向V2X服务的智能网络切片主体上就是先划出3个大的网络切片吧？至于在每个逻辑网络内又怎么细分呢这应该是后续考虑的事情。

## 3. INTELLIGENT NETWORK SLICING ARCHITECTURE FOR V2X SERVICES

这一章讨论如何设计一种具有高灵活性的智能网络切片结构。

首先，为了提供集中式的网络切片控制，提出了一种基于SDN技术的车辆网络云架构。在这种集中式的架构上，我们可以方便地构建出V2X业务的智能网络切片。在这样的体系结构能发挥两个优点：

1. 通过使用NFV，不同种类的资源是从专用的网络基础设施虚拟化，然后网络切片可以定义为一个合适的收集资源，从而进一步增加灵活性。
2. 另一方面，在考虑车辆特性的同时，通过采用AI技术有效管理网络切片

（唔，给我的大概感觉就是，SDN和云计算是用来搭建整体的网络架构，然后NFV的作用是将各项可以调度的资源抽象出来，而怎么结合车联网的特性调度资源则需要借助AI技术的分析，最终实现的目标是为各类QoS要求的V2X业务打造其独有的网络切片）

### A. Cloud-based Framework for Vehicular Networks

这篇论文将基于云的车联网框架中划分两个网络域：边缘云和远端云。

边缘云显然更为重要，其功能决定了V2X业务的服务质量。服务区域囊括了单个或多个宏蜂窝覆盖区域，由于更加接近终端，可以较好地确保较低的端到端响应时间

远端云具有强大的处理能力和大量的存储资源，由于边缘云资源的有限性，边缘云会存在无法满V2X业务需求的情况，这时需要向远端云申请资源，但为此需要付出额外的信令开销，并且将付出加大响应时延的代价。

![img](https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/v2-23a5092389fc98136bef34bc65a73b2e_b.jpeg)

（不过我看有些论文，还有一层车云的概念即构成了3层的车联网架构。未来的车辆本身具有一定的资源可以使用，但考虑到移动中的车辆会使得这层所谓的云结构太不稳定了吧。不过之前看过一个论文提到个场景比较有意思：考虑在一个大型停车场里的环境下，用泊车的资源来承担计算任务。但是这些车辆的异构熟悉、私有属性，怎么允许你使用他们的资源？）

### B. Design of Intelligent Network Slicing Architecture

上述的车联网架构，为不同V2X业务的网络切片和基于AI的网络控制奠定了基础。作者把网络切片划分为以下图的四层结构（不过我很好奇这个四层，和上面讲的那个基于云的两层结构逻辑上是怎么个对应，理解得很模糊？）

![img](https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/v2-67f4f8fd0fe3c87fe89bd67ed6e4aa36_b.jpeg)

- **Network infrastructure virtualization layer:** 

网络基础设施层，这一层要解决两件事：

1. 每个网络域中的不同设备的资源都被映射到资源池，这个资源池分为了3个维度的资源：communication, computing and storage三种资源（应该要借助各种底层硬件的虚拟化技术，具体怎么实现的不是很了解）

\2. VNF池：VNF即虚拟化网络功能，这一层在上述的3C资源池之上。为实现不同的网络功能，不同的VNF单元要使用到数量彼此不同的3C资源。控制层可在VNF集合中选择需要的VNF组合，提供不同V2X服务奠定基础。

- **Intelligent control layer：**

控制层主要是配置和管理网络中的切片，具体来说选择合适的VNF来定制专门的网络切片；此外，控制层也负责收集、保存与分析车联网中生成的移动数据，可以利用先进的AI算法分析高级特征。（看来是整个架构里负责智能处理的环节所在）。

而这一层需要注意的是：由于车联网环境的移动特性和高动态网络拓扑，而且网络规模通常很大。AI算法通常需要更大的移动数据量和更长的处理时间来获得满意的控制的性能。因此直接利用AI算法来处理和优化每个V2X业务QoS要求，很难保障V2X业务的实时性，也会带来相关的处理复杂性。

为了使智能控制层可以快速且高效地部署和管理网络切片，作者对控制层采用了分级控制的结构：

1. Slicing Deployment Controller(SDCon) ：切片配置控制器。位于控制层的底层，在较大的时间尺度上运行，利用ＭＬ算法分析车联网的宏观历史信息和负责网络切片的部署（部署具体是指的啥？）
2. Slicing Management Controller (SMCon)：切片管理控制器，负责及时响应和实时管理每个切片中的资源。

需要注意：AI技术只在切片配置控制器中(SDCon) 中应用（这里估计就是各种论文里提的AI算法具体落地的位置）

（不太能理解细节：大概就是说要根据两个时间尺度来考量切片的配置，具体每个时间尺度上做些啥，不举例子的话也很难理解）

- **Network slice layer:**

网络切片层，正如一开始对V2X业务分类的，存在着3大类别的网络切片。（不过这一层要做啥呢？下面的控制层不是把资源分配做完了吗？这一层就是做个分类？）

- **Service customized layer:** 

网络切片定制层，感觉是个面向上层的接口，需要定义各类V2X业务的QoS要求和优先级吧，它们会被作为控制目标来指导下面网络切片的定制工作

（这一段更多的是讲了网络切片架构的搭建思路，i只能理解个大概的脉络，具体每一项的细节怎么做，和有啥学术上的研究点，还是得去专门找具体的文章结合着来看）

## 4. CHALLENGES AND SOLUTIONS IN INTELLIGENT NETWORK SLICING

### A. Mobile Data Collection for Intelligent Network Slicing

- 在上面所提的SDCon控制器，需要适当地收集车联网中的数据，并进行适当地预处理，以分析车联网宏观状态的动态变化。直接上图，作者把车联网中需要观测的数据给划归了两类，

![img](https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/v2-7cce6d5721748f5976e2011c8f526fcb_b.jpeg)

对于这些数据要考虑到它们具有很强的时空关联性 spatio-temporal diversity，这给数据的收集和处理实际上带来了一定的考虑角度：

一方面：作者把车联网中数据的采集类比于拍照，在不同的地点采集数据会有区别，而同一个地点连续的数据采集，其实会有一定程度上的关联，能反映该地点的平均V2X服务需求情况

![img](https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/v2-1ad81bb20b98d25c4d023a97287290a1_b.jpeg)

这带来的一点启发是，由于车联网的宏观状态只会在较大的时间维度上变化，因此不需要实时收集数据，每隔一段时间收集数据。实际上，在一个长期的时间尺度上，周期性地收集移动数据是合理避免冗余的方法。

### B. Intelligent Control Layer Powered by Deep Reinforcement Learning

Intelligent Control Layer 中，对于寻找最优的网络切片部署策略，强化学习无疑是最为有效的工具

![img](https://pic3.zhimg.com/v2-2e9c7f665b445676f385a2b4d98827de_b.jpeg)

此外，作者认为由于时间上的关联，需要结合LSTM网络来考虑。

![img](https://cdn.jsdelivr.net/gh/xiangli-bjtu/Image-hosting-site/img/v2-f44389565b15944f50cb6c2392ac02ea_b.jpeg)

（读完这一章节两个感悟：

1. 不知道这种实际的车联网数据去哪里获取，或者有啥仿真可以做到的；
2. 好好学强化学习吧，感觉车联网里的都是拿这套方法来灌水论文）

## 5. SIMULATION RESULTS AND ANALYSIS

最后是给了一个实验，不赘述了，感兴趣的话去原文看吧。