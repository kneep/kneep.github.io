---
title: "内存模型 4：宽松模型"
date: "2025-09-01"
keywords:
- "计算机原理"
- "操作系统"
- "内存模型"
- "并发"
- "内存屏障"
tags:
- "工程实践"
- "内存模型"
summary: "嵌入式领域中占主导地位的 ARMv8 处理器使用了一种限制更少的内存模型"
katex: true
cover: "/img/memory_model.png"
---

## 介绍
在 TSO 这一篇中谈到过，给内存模型松绑，有助于硬件设计上实现更多的优化，TSO 对 SC 的一种顺序进行了松绑。如果我们进一步松绑，有机会带来更多的优化。比如，在分析 TSO 的优化的时候有一个场景，写指令发生在读指令之前，但生效却在之后，因为中间加了一层缓存。对于写指令来说，这个缓存是先进先出的（FIFO），因为对于 TSO 来说，写之间的顺序是强制的。如果我们松绑了写之间的顺序，那么这个缓存就没必要是先进先出，这样会带来更多的优化机会。本篇我们介绍宽松的内存模型，有不少处理器都采用了这种模型，为了叙述方便，我们就把它统称为 RMC（Relaxed Memory Model）。

## 场景分析
考虑以下代码：
```c
// Core C1
S1: data1 = NEW;
S2: data2 = NEW;
S3: flag = SET;
```

```c
// Core C2
L1: r1 = flag
B1: if (r1 != SET) goto L1;
L2: r2 = data1;
L3: r3 = data2;
```

如果你熟悉操作系统，那么很可能已经看出，`flag`就是一个自旋锁（spinlock）。这个锁的作用，就是确保`r2`和`r3`都能得到`NEW`值。

要使`r2`得到`NEW`，那么以下执行序列必须得到保证：`S1 -> S3 -> L1 -> L2`。同理，要使`r3`得到`NEW`，则`S2 -> S3 -> L1 -> L3`。在 SC 和 TSO 两种内存模型下，这都可以得到保证。但是，SC 和 TSO 还额外保证了`S1 -> S2`和`L2 -> L3`，**这种保证是没有必要的**，所以我们有了第三种内存模型 RMC。

在 RMC 下，除了对同一地址的读写，其他的操作之间都是没有顺序保证的，所以上面的场景是无法产生预期效果的，我们还是需要借助 FENCE 来保证顺序：

```c
// Core C1
S1: data1 = NEW;
S2: data2 = NEW;
F2: FENCE
S3: flag = SET;
```

```c
// Core C2
L1: r1 = flag
B1: if (r1 != SET) goto L1;
F2: FENCE
L2: r2 = data1;
L3: r3 = data2;
```

![](img/rmc1.svg)

## 形式化定义
先来定义`FENCE`，和 TSO 是一样的：
  1. 如果 `L(a) <p FENCE`，则 `L(a) <m FENCE` （`Load -> FENCE`）
  2. 如果 `S(a) <p FENCE`，则 `S(a) <m FENCE` （`Store -> FENCE`）
  3. 如果 `FENCE <p FENCE`，则 `FENCE <m FENCE` （`FENCE -> FENCE`）
  4. 如果 `FENCE <p L(a)`，则 `FENCE <m L(a)` （`FENCE -> Load`）
  5. 如果 `FENCE <p S(a)`，则 `FENCE <m S(a)` （`FENCE -> Store`）

RMC 要求保证对同一地址的操作顺序：
  1. 如果`L1(a) <p L2(a)`，则`L1(a) <m L2(a)`（同一地址`Load -> Load`）
  2. 如果`L(a) <p S(a)`，则`L(a) <m S(a)`（同一地址`Load -> Store`）
  3. 如果`S1(a) <p S2(a)`，则`S1(a) <m S2(a)`（同一地址`Store->Store`）

至于同一地址`Store->Load`的规则，和 TSO 是一样的：
针对同一内存地址，读（`Load`）会得到 `<m` 中最近一次写（`Store`）的值，或者同一处理器上一个尚位于 Write Buffer 中的值：

$$
{L(a) = MAX_{<m} \{ S(a)\ |\ S(a) \text{  <m  } L(a)\} \text{ or } S(a) \text{  <p  } L(a)}
$$

除此以外，其他的顺序没有保证。

|             | Load 2 | Store 2 | RMW 2 | FENCE 2 |
|:-----------:|:------:|:-------:|:-----:|:-------:|
| **Load 1**  | **A**  | **A**   | **A** |   Yes   |
| **Store 1** | B      | **A**   | **A** |   Yes   |
| **RMW 1**   | **A**  | **A**   | **A** |   Yes   |
| **FENCE 1** | Yes    | Yes     | Yes   |   Yes   |

这里引入了一个新的符号 **A**，代表了仅保证对相同地址的操作的顺序。

## 总结
在这种内存模型下，编程变得更困难了。

程序员需要想清楚很多事情，人为地把一个个`FENCE`码放在代码的合适位置，确保实现出来的逻辑真正符合设计意图。同时，`FENCE`是有成本的，所以又要反过来思考哪些地方不需要`FENCE`，避免滥用。总结起来一句话——用最少的`FENCE`实现完全正确的代码逻辑。

在嵌入式领域中最流行的 ARM 处理器就属于这类模型。
