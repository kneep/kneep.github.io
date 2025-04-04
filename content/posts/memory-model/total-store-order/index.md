+++
title = '内存模型 3：TSO'
series = '内存模型'
date = '2025-03-15'
keywords = ['计算机原理', '操作系统', '内存模型', '并发', '内存屏障']
tags = ['工程实践']
isCJKLanguage = true
+++

## 介绍
从[本系列第二篇文章](posts/memory-model/sequential-consistency/)中我们可以看出，SC 这种内存模型，优点是符合直觉，符合直觉非常重要，它意味着写代码不容易犯错。但是，它同时又束缚了处理器设计者的手脚，无法运用可能的优化手段来提升处理器性能。

本文中，我们来给 SC 稍微松松绑。什么情况下需要松绑呢？先来看下面的场景。
![](img/tso1.svg)

在这个场景中，core 0 将数据写入内存中，core 1 将数据从内存中读出。略有不同的是，在 core 0 和内存之间加了一个 write buffer。为什么要加这个缓冲？我们可以注意到，图中的内存系统写的是 Cache-Coherent Memory System，因为缓存的存在，多核处理器的缓存之间需要通过某种协议来确保**所有处理器通过缓存看到的内存内容是一致且有效的**，只有被写的地址的内容出于缓存一致（Cache-Coherent）的状态，写指令才能被执行。而实施这种缓存一致性协议是需要花费时间的，所以写指令经常需要等待。加了这个缓冲，处理器可以先把写指令提交到缓存中，等到它对应的地址处于一致状态时，再把写指令取出执行，提升了写的性能。

但是，这也带来了副作用。因为数据从进入 write buffer 到出来，也是需要时间的。Core 1 同时在读内存的内容，如果它读取的内容与 core 0 写的内容无关，那么皆大欢喜。但如果 core 1 必须读到 core 0 刚刚写入的内容呢？这就有点复杂了。从图上可以看到，假设从上到下代表了时间轴，那么 core 0 的写操作虽然发生在 core 1 的读操作之前，但它写的内容真正进入内存，却发生在读操作之后。也就是说，core 1 没有读到它想要的值。更糟糕的是，程序员通常很难意识到这里有问题，因为它违反了直觉。

同时，当读和写是操作同一地址的时候，无论写指令是否还在缓冲中，后面的读指令都能读到最新的值，这种现象称为 bypassing。

Total Store Order（TSO），我们要介绍的第二种内存模型，它和 SC 的主要区别就是允许上面这种特性带来的乱序。

## 场景分析
因为 TSO 的大部分特性和 SC 相同，我们仅仅讨论差别的两个特性：写读乱序、bypassing。

### 场景一
写读乱序我们继续使用[本系列第二篇文章](posts/memory-model/sequential-consistency/)中的例子，再次把伪代码列如下：
```c
// Core C1
S1: x = NEW;
L1: r1 = y;
```

```c
// Core C2
S2: y = NEW;
L2: r2 = x;
```
在场景四中，我们构造了如下的执行序列：
![](img/sc4.svg)
我们之前提到，因为`S2 <p L2`，但是同时`L2 <m S2`，所以它是不符合 SC 的。它造成了`(r1, r2) == (0, 0)`这个非常不符合直觉的结果。

现在我们知道它的出处了，它就是我们上面分析的 write buffer 的场景，虽然不符合 SC，但它是符合 TSO 的。`S2: y = NEW;`这个写操作虽然是最早发生（更准确地应该称为「提交」），但因为 write buffer 的存在，它的生效发生了延迟。

### 场景二
下面我们为 TSO 构造了一个更复杂点的例子来说明 bypassing：

```c
// Core C1
S1: x = NEW;
L1: r1 = x;
L2: r2 = y;
```
```c
// Core C2
S2: y = NEW;
L3: r3 = y;
L4: r4 = x;
```
{{< alert "triangle-exclamation" >}}
程序运行后，有没有可能`(r1, r3) == (NEW, NEW)`且`(r2, r4) == (0, 0)`？
{{< /alert >}}

答案是**有可能**。
![](img/tso2.svg)

从这个执行序列可以看出，`r1`能得到`NEW`值，哪怕`NEW`尚未实际进入`x`的内存位置，原因就是这两条指令操作的是同一地址，存在 bypassing 效应，红点即为 bypassing 发生处。同理`r3`也一样。

bypassing 是符合直觉的，因为我们直观上认为，同一处理器写出去的值，无论是否真正进入内存，处理器自己理应知道最新的值。就像你可以欺骗别人，但骗不了自己。

## 形式化定义
我们在 SC 的形式化定义基础之上，看看 TSO 的定义有什么区别。

---
- 所有处理器按照它们的`<p`把读（`Load`）写（`Store`）指令插入到`<m`中，不管这些指令是否针对同一内存地址（`a == b` 或 `a != b` 都适用），具体包括四种情况：
  1. 如果 `L(a) <p L(b)`，则 `L(a) <m L(b)`（`Load -> Load`）
  2. 如果 `L(a) <p S(b)`，则 `L(a) <m S(b)`（`Load -> Store`）
  3. 如果 `S(a) <p L(b)`，则 `S(a) <m L(b)`（`Store -> Store`）
  4. ~~如果 `S(a) <p L(b)`，则 `S(a) <m L(b)`（`Store -> Load`）~~

{{< alert "triangle-exclamation" >}}
变更一：Write Buffer 效应，使 `Store -> Load` 的顺序无法保证
{{< /alert >}}

---
- 针对同一内存地址，读（`Load`）会得到 `<m` 中最近一次写（`Store`）的值，或者同一处理器上一个尚位于 Write Buffer 中的值：
```
L(a) = Max<m { S(a) | S(a) <m L(a) } 或 S(a) <p L(a)
```
{{< katex >}}
$$
{L(a) = MAX_{<m} \{ S(a)\ |\ S(a) \text{  <m  } L(a)\} \text{ or } S(a) \text{  <p  } L(a)}
$$

`S(a) <p L(a)`是指针对同一地址的最新一个写指令。
{{< alert "triangle-exclamation" >}}
变更二：对地址`a`的`Load`，要么读到`<m`中的最新值，要么读到`<p`中的最新值，而且后者优先。
{{< /alert >}}

---
- 引入新的`FENCE`指令，其特性满足以下规则：
  1. 如果 `L(a) <p FENCE`，则 `L(a) <m FENCE` （`Load -> FENCE`）
  2. 如果 `S(a) <p FENCE`，则 `S(a) <m FENCE` （`Store -> FENCE`）
  3. 如果 `FENCE <p FENCE`，则 `FENCE <m FENCE` （`FENCE -> FENCE`）
  4. 如果 `FENCE <p L(a)`，则 `FENCE <m L(a)` （`FENCE -> Load`）
  5. 如果 `FENCE <p S(a)`，则 `FENCE <m S(a)` （`FENCE -> Store`）

`FENCE`的原意是「栅栏」，这是一个非常形象的词汇，它的作用是代码执行序列分割成上下两部分，上半部内部，顺序无法保证，下半部内部，顺序也无法保证。但是，上半部的指令一定会在下半部的指令之前生效。

由于 TSO 只破坏了一条 SC 的规则，它实际只需要规则 2 和 4 就可以了。但为了方便后续学习更多的内存模型，我们把完整的 FENCE 规则都放在这里。
|             | Load 2 | Store 2 | RMW 2 | FENCE 2 |
|:-----------:|:------:|:-------:|:-----:|:-------:|
| **Load 1**  | Yes    | Yes     | Yes   | **Yes** |
| **Store 1** | **B**  | Yes     | Yes   | **Yes** |
| **RMW 1**   | Yes    | Yes     | Yes   | **Yes** |
| **FENCE 1** |**Yes** | **Yes** |**Yes**| **Yes** |

粗体的字代表了 TSO 相对 SC 变化的点，其中 **B** 代表了：针对同一内存地址的`Store->Load`的需要 bypassing，其他情况则顺序无法保证。

在第一个场景中，我们看到结果可能出现`(r1, r2) == (0, 0)`，现在我们来看下，有了`FENCE`指令后，如何确保这个有违直觉的结果不再出现。
```c
// Core C1
S1: x = NEW;
FENCE;
L1: r1 = y;
```
```c
// Core C2
S2: y = NEW;
FENCE;
L2: r2 = x;
```
在这里，`FENCE`强制了`S1->L1`和`S2->L2`的顺序，使得`(r1, r2) == (0, 0)`不会再出现。

## 总结
SC 是最符合直觉的内存模型，TSO 也接近直觉，它能提供更好的性能，代价是无法保证`Store->Load`的顺序，程序员必须意识到这里需要`FENCE`，没有系统学习过这个主题的程序员很容易在这里犯错。

[据信](https://dl.acm.org/doi/10.1145/1785414.1785443)，Intel 和 AMD 的 x86 处理器用的内存模型是 TSO。