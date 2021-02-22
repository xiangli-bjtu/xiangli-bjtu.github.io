---
title: 《CSAPP》读书笔记（2）：信息的表示和处理
date: 2019-11-28 20:36:23
index_img: /img/post_index_img/CSAPP.jpg
mathjax: true
tags: 计算机基础
categories: 技术
---

# 信息存储

## 字节、计算机进制

现代计算机是采取二进制来表示信息的，一个0-1状态为一个**比特**。更为实用的单位是连续8位的块即一个**字节**。计算机程序将内存视为一个非常大的字节数组，内存中的每个字节由一个唯一的数字表示，称为它的**地址**。所有地址的集合称为**虚拟地址空间**

信息采取0-1bit的比特串来表示是很麻烦的，更为通用的表示是采取**16进制**。2进制、10进制以及16进制的转换关系如下：

<img src="C:\Users\XiangLi\Documents\xiangli-bjtu.github.io\source\_posts\《CSAPP》读书笔记（2）：信息的表示和处理\Hex.png" style="zoom:67%;" />

对于较大数值的进制转化可以借助软件工具来实现（如系统自带的科学计算器）

## 字长

每台计算机都有一个字长，也就是平常所谓的32位、64位机器。它决定着**虚拟地址空间的大小（虚拟内存的字节总数）**，对于字长为$W$的计算机，其虚拟地址范围为：$0~2^W-1$

对于程序是“32位程序”或“64位程序”，**针对该程序是如何编译的**，而不是运行机器的类型。

以C语言为例，其不同的数据类型占据内存空间的字节数目也不同

<img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/C%20data%20type.png" style="zoom:67%;" />

## 寻址和字节顺序

对于跨越多个字节的对象，需要建立的规则就是：这个对象的地址是什么，以及在内存中如何排列组成对象的这些字节？

* 几乎所有计算机都是以该**对象所占据若干字节中最小的地址，作为对象的地址**
* 接下来对于对象的存储顺序，主要有两种规则的排列：
  * **小端法**（little endian）：大多数Intel兼容机采用的方法，对象的最低有效字节放在内存的低地址端
  * **大端法**（big endian）：IBM 和Oracle的机器，对象的最低有效字节放在内存的低地址端

<img src="C:\Users\XiangLi\Documents\xiangli-bjtu.github.io\source\_posts\《CSAPP》读书笔记（2）：信息的表示和处理\bitorder.png" style="zoom:67%;" />

## 字符的表示

C语言中的字符串被编码为以**NULL字符结尾**的字符串数组，因此对于字符串'abcde'虽然看起来只有5个字符，但在C语言表示下其实际长度为6.

对于英文文本（字符数组）的表示方法，采用的是**ASCII码**表示，文本数据应具备更强的平台独立性。对于更为复杂的其他语言字符表示，[这个视频](https://www.bilibili.com/video/av23469929?from=search&seid=3003390791592500042)是个很好的说明：

## 布尔代数

英国数学家乔治布尔所创立的逻辑体系，也是现代计算机的基础之一；香农是第一个把布尔运算和数字电路联系起来的人（1937年的硕士论文）。

现在计算机的编程语言都支持布尔运算，以C语言为例，包括了位级运算和逻辑运算（这也是初学者容易混淆的几个运算符号）

* `&`，`|`，`~`：按位进行布尔运算，位运算的一个重要用途是实现**掩码操作**
* `&&`，`||`，`！`：逻辑运算符，支持**短路特性**

## 移位运算

C语言中包括三种移位操作，三者实现如下：

* **左移**（<<）：移出去的位丢弃，空缺位用 0 填充
* 右移（>>）
  * **逻辑右移**：移出去的位丢弃，空缺位用 0 填充；
  * **算术右移**：移出去的位丢弃，空缺位用符号位（最高位）来填充。

![](https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/1.PNG)

**几乎所有的编译器和机器组合，对于有符号数使用算术右移；而对于无符号数，右移一定是逻辑右移**



# 整数的表示

C语言支持多种整型数据，以64位机器为例给出了每种数据类型的表示范围

![](https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/C_data_range.PNG)

可以看到C语言为每种整型数据类型支持**有符号（signed）**和**无符号（unsigned）**两个版本，常量默认是有符号版本。有符号数的取值范围是不对称的，负数的范围比正数范围大1。

## 无符号数的编码

假设一个整型数类型为w位，我们可以将它的位向量写成x，可以得到如下的映射关系B2Uw（Binary to Unsigned）表示将w位的二进制转化为无符号数

<img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/B2U.PNG" style="zoom:67%;" />

## 有符号数的编码

有符号数有很多不同的编码方式，比如**补码（two's complement）**、**反码（one's complement）**和**原码（sign-magnitude）**。其中最常见的是补码编码，C语言标准虽然没有要求要用何种形式的编码来表示有符号整数，但是**几乎所有机器都会使用补码编码。**

同样可以定义映射关系B2Tw（Binary to Two's complement）表示将w位的二进制转化为补码编码的有符号数

<center><img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/B2T.PNG" style="zoom:67%;" /></center>

补码编码为最高位当作符号位，并赋予负权重。我们可以看出补码表示的两点性质

* 补码编码的范围是不对称的，这是因为通过设置符号位，将一半的位模式表示为负数，将另一半的位模式表示为非负数，而0是非负数，所以负数就比正数多1个

* -1的补码表示法，和同等字节大小的无符号表示最大值有相同的位模式

![img](https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/v2-d5f671dada9952846dabfe60cbe859f5_1440w.jpg)

上图提供了一些比较重要的数字及其编码，C库中的文件`<limits.h>`定义了一组常量，来限定编译器运行的这台机器的不同整型数据类型的取值范围，比如它定义了常量`INT_MAX`、`INT_MIN`和`UINT_MAX`，就分别对应上面推导的最大值和最小值。

*补充：[一文读懂原码、反码与补码](https://segmentfault.com/a/1190000021511009)*

*原码表示和反码表示都有一个共同**特点：对于数字0有两种不同的编码方式**，这也是在计算机中不推荐使用这两者表示有符号数的原因，因为会造成浪费。*

## 有符号数和无符号数之间的转换（同字长类型转换）

C语言可以在各种不同的数字类型之间做强制类型转换，它的具体实现要从位级角度来看，它保持位模式不变，只是改变了解释这些位的方式，即相同位模式下由于不同的映射关系带来数值的差异。

根据上文的映射公式，可以进行如下的推导，得到转换关系如下：

![](https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/TC_Un_convert.PNG)

* 对于大于0的补码，符号位并没有使用，所以它能得到有无符号数相同的值；对于小于0的补码，由于符号位导致数值少了2^w，需要加上，这就使得小于0的有符号数转换为补码时，数值发生了发生跳转，一下变得很大。

![img](https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/v2-f33fb7859f18848379bf27e11cd517e5_1440w.jpg)

* 当无符号数小于补码表示最大值，它还能保证最高有效位为0，这和补码表示正数时相同；而大于该最大值，由于它的最高有效位为1，使得它的值相对补码大了2^w，因此需要减去2^w，这就使得大于补码表示最大值得无符号数转化为补码时会发生跳转，一下变得很小。

![img](https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/v2-2abbadf32c7bd2ba571bdb976c09ad05_1440w.jpg)

**为什么需要了解二者的转换关系：**

在C语言中，当一个有符号数和一个无符号数进行计算时，会**隐式地将有符号数转化为无符号数**。当进行逻辑判断时，可能会导致错误或漏洞，因此建议**绝不使用无符号数**。

<center><img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/CSAPP_post2_ex.PNG" style="zoom:50%;" /></center>



## 不同字长的类型转换

在不同字长的整数之间进行类型转换，要保持在数据类型范围内的数值是不变的。以下有两种情况：从较短字长的数据类型转换到较长字长的数据类型，比如short到int，就需要进行**扩展位**；从较长字长的数据类型转换到较短字长的数据类型，比如int到short，就需要**截断位**。

**扩展**

* 将一个无符号数进行扩展，直接在其前面填充零即可，根据无符号数的定义不会改变其数值大小，这称为**零扩展**
* 与无符号数相比，把有符号数转化为一个更大的数据类型需要进行**符号位扩展**。当符号位为0/1，此时扩展的数位进行补0/1

**截断**

截断位时，数值通常会发生变化。

* 对于w位无符号整数，保留$w'$，只需丢弃$w'$之前的位即可。截断操作相当于二进制取模运算
* 截断有符号数得操作，需要先将二进制数转换成无符号数，通过上面的公式进行截断后，再转换成补码



# 整数的运算

计算机内部通过二进制进行整数运算，由于有限位以及编码方式的限制，可能会导致计算机计算的结果和真实结果之间存在差异，也就发生了**溢出**（完整的计算结果不能放到数据类型的字长限制中）

## 加法

### 无符号数加法

w位的无符号数，其表示范围在[0,$2^w$），当计算结果超过最大范围（即超过最大的位数），计算机会直接去掉w+1之后的位，相当于是计算结果对$2^w$进行了取模，所以该方法称为**模数加法**。在C语言的运行中这种情况并不会产生报错，如果我们希望判定计算结果是否溢出，可以使用以下的代码：

**判定溢出**：可以看出**两无符号数相加发生溢出时，返回的结果小于二者中任一元素**。

![](https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/overflow1.PNG)



### 有符号数加法

使用补码的一个优势在于：补码加法可以使用和无符号数加法相同的硬件，相同的算法，就得到有符号数的加法。所以大多数计算机用**相同的机器指令来执行补码和无符号数加法**。

与无符号加法不同的是，有符号数加法分为**正溢出和负溢出**。

![](https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/overflow2.PNG)

**判定溢出**：

* 两正数相加结果为负，发生正溢出
* 两负数相加结果为正，发生负溢出

## 减法

**加法逆元**：每个元素都有一个加法逆元，当与自己的加法逆元相加时，其结果得到0。

* 无符号数的加法逆元：

由于无符号数的数值表示范围不包括负数，因此无符号数的加法逆元和数学上的相反数数值不同。为得到w位无符号数x的无符号表示加法逆元，需要利用溢出效应，如下所示：

<center><img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/add_inverse1.PNG" style="zoom:80%;" /></center>

* 无符号数的加法逆元：

大多数无符号数的逆元是直接对其取相反数即可。需要注意的是由于无符号数表示范围为非对称的区间，因此对于最小值的逆元需要使用负溢出来实现。根据下述计算公式，**补码最小值的逆元是其本身。**

<center><img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/add_inverse2.PNG" style="zoom:67%;" /></center>

根据以上的定义，**计算机中实现减法的实现本质上还是加法运算，是通过加上减数的加法逆元。**

## 乘法

两个w位的数相乘实际计算结果最大为2w位，但在C语言中会截取2w位的低w位作为结果（也就是上文提的截断操作进行取模运算）

<img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/us_mul.PNG" style="zoom:67%;" />

无论是无符号数和有符号数，乘法的位级运算都是一样的。只不过补码乘法比无符号数乘法多一步，由于截断操作会得到无符号数（计算结果为正），此时就要将无符号数转化为对应的补码。

从下面的表格可以看到，相同位模式的无符号数和有符号数乘法，在按照上述乘法的定义进行操作后，虽然完整结果的位级表示可能不同，但保证了阶段操作后位模式一致。[关于这部分的数学证明](https://www.bilibili.com/video/BV1Ff4y1q7Kf)

<img src="C:\Users\XiangLi\Documents\xiangli-bjtu.github.io\source\_posts\《CSAPP》读书笔记（2）：信息的表示和处理\mul_ex.PNG" style="zoom:67%;" />

大多数机器中，整数乘法指令的执行通常需要几十个时钟周期，所以计算机通常会用移位和加减法的组合来代替。

* **乘上2幂**

首先我们讨论乘上2幂的特殊情况，在允许的w位表示范围内，乘上2的k此幂等于左移k位，如果超过了表示范围，就会发生溢出。

* **乘上任意数**

对于任意整数k，我们可以先对计算关于2幂次的展开。如14=8+4+2。由此就将一个乘法运算转化为了多个移位操作和加法操作的组合。

## 除法

**除以2幂**

同理对于除法我们使用右移，正如前面关于移位操作提到的，要注意**对于有符号数使用算术右移；而对于无符号数，右移一定是逻辑右移**

在除法运算中，比较麻烦的是出现除不尽的情况，此时就需要舍入，我们希望**计算结果都是向0舍入**的。

* **无符号数舍入**

我们引入如下的变量代表无符号数x逻辑右移k位、右移结果再进行左移、低k位的位向量。

<img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/image-20210221114627920.png" alt="image-20210221114627920" style="zoom:67%;" />

因此对于原始的无符号数x，其对2的k此幂进行除法的结果并进行向下取整可以表示：**可见无符号数的除法运算直接进行右移操作即可，会自动完成向下取整。**

<img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/div_2.PNG" style="zoom:67%;" />

* **补码除法**

对于数值为非负数的补码其规则和无符号数一致；但是对于数值为负数的补码，我们需要先加上一个**偏移量**，使其满足向0舍入的预期。下面通过一个例子说明：

<img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/div_3.PNG" style="zoom:67%;" />

可以看到-12340除以16，如果直接取移位后的结果为-772，不符合整数除法向0舍入的原则-771。因此负数值的符号数在进行除法的右移k位操作前，要先加上一个修正值。<u>修正值的计算为1左移k位再减1</u>

<img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/div_bias.PNG" style="zoom:67%;" />

可以看出，在偏置之后，在负结果舍入时，移位运算的结果将会是我们期望得到的

<u>和乘法不同，对于任意的整数除法我们不能采取将其拆分为若干2的幂次加和，再分别相除。</u>



# 浮点数

## IEEE浮点表示方法

IEEE浮点表示涉及三个部分

- **符号（Sign）s：**用来确定V的正负性，当s=0时表示正数，s=1时表示负数。用一个单独的符号位直接进行编码。
- **尾数（Significand）M：** 是一个二进制小数，通常介于1和2之间的小数。使用k位二进制进行编码的小数。
- **阶码（Exponent）E：**对浮点数进行加权。使用n位进行编码的正数

<center><img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/float.PNG" style="zoom:67%;" /></center>

不同精度浮点数的区别在于尾数和阶码的长度不同：32位单精度浮点数具有8位的阶码和23位的尾数长度，而64位双精度浮点数则为11位的阶码和52位的尾数长度）

我们可以根据**阶码**的不同取值，将浮点数的类型分成三种情况：

* **规格化的值**

需要注意阶码部分的实际取值并不是阶码对应比特串的数值，而是要减去一个偏置（取决于阶码字段的长度），这么做的目的使得阶码的数值能重新投影到正负值，并且能够和非规格化数据进行平滑。

<center><img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/float_type1.PNG" style="zoom:67%;" /></center>

而对于尾数部分M的取值，将其设定为介于1和2之间的小数，因此尾数字段表示小数点之后的部分，如下的公式

![](https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/float_type11.PNG)

结合以上的定义，我们就能带入浮点数计算公式得到规格化数值

* **非规格化的值**

阶码字段全为0时，表示非规格化的数值。非规格化数的用途在于：

1. 提供了正负0的表示
2. 趋近于0的数的表示，这里需要注意，此时对于阶码和尾数的计算方式和规格化数据有所不同

<img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/float_type2.PNG" style="zoom:67%;" />

* **特殊值**

特殊值包括正负无穷大以及NaN（Not a Number）

<img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/float_type3.PNG" style="zoom:67%;" />

**示例：整数表示为浮点数**

<img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/int2float_ex.PNG" style="zoom: 67%;" />

## 浮点数的舍入

由于表示位数的限制，浮点数运算只能近似的表示真实的实数运算。因此我们需要一种''最接近真实结果'的舍入方法。

IEEE定义了4种舍入格式

![img](https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/v2-098cc0a1bd3c8a46fae91bfc9a110071_1440w.jpg)

比较特殊的是**向偶数舍入**，其舍入结果遵循：**如果处于中间值，就朝着令最后一个有效位为偶数来舍入；否则朝着最近的值舍入**。比如1.40，由于靠近1就朝1舍入；1.6靠近2就朝2舍入；1.50位于十进制的中间值，就朝着偶数舍入，所以为2。

> 向偶数舍入的意义：如果对一系列值进行向上舍入，则舍入后的平均值会比真实值更大；使用向下舍入，则舍入后的平均值会比真实值更小。通过向偶数舍入，每个值就有50%概率变大、50%概率变小，使得总的统计量保持较为稳定。

当需要舍入到小数点后的情况，以上的原则依然适用

<img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/float_round.PNG" style="zoom:67%;" />

## 浮点数的计算

浮点数运算由于计算结果溢出或舍入的操作，不具备结合律，比如下面的几个典型例子

<img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/float_oprate.PNG" style="zoom:67%;" />

**建议：**对于从事科学计算的程序来说，需要考虑好清楚数值的范围，如果计算的数值范围变化很大，需要重新结合或改变运算顺序，避免由于溢出或舍入出现计算问题。

**C语言中的浮点数**

<img src="https://raw.githubusercontent.com/xiangli-bjtu/Blog-images-hosting/main/img/float_in_C.PNG" style="zoom:67%;" />