+++
title = '我们读什么计算机书'
date = '2023-04-20'
featuredImage = '1.webp'
tags = ['计算机', '读书']
isCJKLanguage = true
+++

作为一个软件工程师，提升自己能力的最重要的手段之一是读书。资深的工程师需要防止自己能力的腐化，防止自己代码的腐化，通过不断读书来补齐知识的短板。新加入职场的工程师，前面几年更应该是大量密集读书的时期，多花一些业余的时间，从一开始就树立对软件开发的正确认知，价值回报会伴随整个职业生涯。

<!--more-->

# 我们读什么计算机书

## 前言
作为一个软件工程师，提升自己能力的最重要的手段之一是读书。资深的工程师需要防止自己能力的腐化，防止自己代码的腐化，通过不断读书来补齐知识的短板。新加入职场的工程师，前面几年更应该是大量密集读书的时期，多花一些业余的时间，从一开始就树立对软件开发的正确认知，价值回报会伴随整个职业生涯。

通过陈列一个书单，希望能够帮助大家大致梳理下软件开发的知识脉络，有针对性地去加强和巩固自己的知识，也作为我自己的总结。对于经验丰富的工程师，也强烈建议大家继续学习。以下推荐的书籍，至少我都读过，有些可能是挑着章节读的，少数没读过的都会单独说明。

希望把这篇文章一直更新下去，不断加入更新更高质量的书籍。

我们从两个维度来评价每本书籍：

{{< admonition info "评价维度" true >}}
**推荐度**：代表作者对书的价值的主观评价，1～5分，越高代表越有价值。

**优先级**：代表读这本书的紧急程度（尤其针对新人），1～5分，越小代表越早读。
{{< /admonition >}}

对于读书，还有几点建议。

### 重视基础知识
软件开发正在变成一个工具学科，几乎所有的行业，都需要软件工程师；同时它又是一门很复杂的工具学科。这是一个利好，这决定了我们的职业生涯具有广阔的前景。但是，支持我们兑现自己职业生涯广阔前景的，是基础知识是否扎实，而不掌握了多少新鲜的名词。学会分辨哪些知识是易变，哪些知识是不变的。恰恰是不变的东西，没多少人学得好。

### 尽量读英文原版
中文书籍的出版经常会滞后于英文原版几年，读英文原版能确保我们读到最新的版本。大部分计算机书籍的中文翻译质量也不好，有时候比英文更难理解。更重要的一点，我们需要信息自由。当我们阅读英文的速度能接近母语的时候，会感觉无比自由。

### 发现真正的问题，并用学到的软件知识去解决
把书本知识转化固化为内在的认知，一定要做真正的项目。这个项目不一定是你的工作职责给你的任务。生活和工作中有大量可以使用软件去解决的问题，尝试发现这些问题（比如帮助同事处理一个表格），然后去解决它。在解决过程中，会产生更深刻的认知。

{{< admonition note "注意" true >}}
本文以建设我当前工作团队的能力为目标，以建设基础能力为主，以实用为主，不准备做到面面俱到，适用的对象可能是从事系统编程和嵌入式编程的工程师。有不少计算机方面重要著作，跟当前工作相关性不大，就不一一介绍。
{{< /admonition >}}

## C
### The C Programming Language, 2nd Edition
C 语言是一门「简单」的语言，如果要推荐一本正统的教程，就是这本。这是 C 语言作者写的书。没有系统学习过C语言的同学，可以通过这本书来学习。学校里如果学过，可以作为参考书。
这本书的缺憾是以 C89 为标准写的，而且两位作者一位已经去世多年，另一位也已近暮年，以后也不会再更新了。但 C99 的新特性可以通过互联网上的其他资料来弥补。
{{< admonition tip "评价" true >}}
推荐度：3

优先级：1
{{< /admonition >}}

### C-FAQ
> https://c-faq.com

C-FAQ 收集了从远古时代至今的在```comp.lang.c```新闻组中被问到的最重要问题。强烈推荐读一遍。
{{< admonition tip "评价" true >}}
推荐度：3

优先级：2
{{< /admonition >}}

### C Traps and Pitfalls
C 语言是一门「简单」的语言，「简单」的意思是，把刀子递到你手里，你怎么用是你自己的事情。这个文档是一篇论文，后来发展成了一本书。其实只要看论文就可以了。它的关注点是 C 语言的阴暗面，那些容易犯错的地方。
{{< admonition tip "评价" true >}}
推荐度：3

优先级：2
{{< /admonition >}}

## C++
### C++ Primer, 5th Edition
这是公认的最好的 C++ 入门书，C++ 标准委员会权威专家的著作。虽然说是「入门书」，但它的内容非常详尽，周边材料也比较齐全。这本书的编排，跟一般书思路有点不一样，注重实用再逐步深入，所以把常见 STL 的介绍放在比较靠前的章节。对于真正要入门的同学来说，可以挑着读。如果按照常见的 C++ 教学思路，可以先读 Part I 和 Part III（最后一章泛型也可以先不看），对 C++ 语言特性有一个较为全面的了解，就可以利用手上的技能在工作中去做一些实际的事情了，逐步再去学习 STL 和其他高级主题。

这本书的更新也相对较慢，目前最新版是针对 C++ 11，略有点老。新的第 6 版也在规划中，但出版日期一直不确定（作者之一 Stanley Lippman 已于 2022 年[去世](https://news.ycombinator.com/item?id=37106408)）。不过 C++ 14 和 17 的新特性，也可以通过其他的一些补充材料去学习。
{{< admonition tip "评价" true >}}
推荐度：4

优先级：1
{{< /admonition >}}

### The C++ Programming Language, 4th Edition
这是 C++ 之父的著作，权威程度不言而喻，内容上也是最全的，一千三百多页。也只讲到 C++ 11。这本书我的个人感受是不如 C++ Primer 好懂，没看完。两者读一本就可以。
{{< admonition tip "评价" true >}}
推荐度：3

优先级：1
{{< /admonition >}}

### Effective C++ & Effective Modern C++
来自 Scott Meyers 的 C++ 著作，不关注完整的 C++ 特性，而是关注如何写出高效的 C++ 程序。先读 Effective C++，再读 Effective Modern C++，两本书没有替代关系，是互补的。第一本讲的是通用的 C++ 特性，但不排除某些观点已经过时，第二本专注于 C++ 11 和 14 带来的新特性。
{{< admonition tip "评价" true >}}
推荐度：4

优先级：2
{{< /admonition >}}

### C++ Core Guidelines
> https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines

官方建议的 C++ 编程的核心原则，由 C++ 之父 Bjarne Stroustrup 和 C++ 标准委员会主席 Herb Sutter 维护。强烈推荐看一遍，很多疑问都可以找到答案。
{{< admonition tip "评价" true >}}
推荐度：4

优先级：1
{{< /admonition >}}

## 计算机系统
你可能会发现你 C 语言学得再熟，写出来的程序问题还是很多，你的瓶颈在于你不理解计算机系统是如何工作的。
### Computer System - A Programmer's Perspective, 3rd Edition（深入理解计算机系统）
这本书的中文名翻译成《深入理解计算机系统》，有点意译的成份。书名的本意是从程序员的视角去看计算机系统，也就是那些能帮助程序员写出好程序的计算机系统知识。这些知识的更新并不快，但真正敢说掌握的人，很少。我经常在面试中会问负数的二进制表示法以及这么设计的理由，能答上来的人不会超过一半。

这本书蕴含了软件开发过程中非常重要的一种思维方式：像计算机一样去思考——反复问自己，我写出来的程序，对计算机来说，究竟意味着什么？这本书能给你一个系统性的答案。

我刚开始工作的时候，这本书几乎没人知道，现在都已经被很多国内大学作为教材了，但这本书很厚，学校能教多少内容就不好说了。
{{< admonition tip "评价" true >}}
推荐度：5

优先级：2
{{< /admonition >}}

### A Primer on Memory Consistency and Cache Coherence, 2nd Edition
> https://pages.cs.wisc.edu/~markhill/papers/primer2020_2nd_edition.pdf

讲清楚内存模型和缓存一致性协议的最好的书。曾经在公司级的代码质量委员会中听人抱怨，面试的候选人里面，几乎没有人听说过内存模型这个概念。就是这本书了。
{{< admonition tip "评价" true >}}
推荐度：4

优先级：3
{{< /admonition >}}

### What Every Programmer Should Know About Memory
> https://people.freebsd.org/~lstewart/articles/cpumemory.pdf

这是 Red Hat 工程师 Ulrich Drepper 早年写的一篇长论文，解释内存和缓存是如何工作的，这是我见过最好的讲内存知识的资料。跟上一本书同样的意义，从软件的视角看计算机系统，从计算机系统的视角看软件，割裂了两者，写不好程序。
{{< admonition tip "评价" true >}}
推荐度：4

优先级：3
{{< /admonition >}}

## 操作系统
我们写不好程序的另一个原因可能是不理解操作系统的工作原理。学习操作系统，大量的开源操作系统源码是最好的资料，但书能起到知识梳理的作用，让你能更好地理解源码。理论类的选一本读就够了。

### Modern Operating System, 4th Edition（现代操作系统）
操作系统的畅销书有不少，这是其中一本，优点是比较新，里面讲到了 Windows 8、Android、VMware 等传统操作系统书不会讲的内容。
{{< admonition tip "评价" true >}}
推荐度：4

优先级：2
{{< /admonition >}}

### The Linux Kernel Module Programming Guide
> https://sysprog21.github.io/lkmpg

二十年前学 Linux 驱动编程必读。是的，现在还在推荐这本古老的书，说明它复活了！有位台湾的朋友 Jim Huang 接手了，并努力把它更新到适用于最新的内核版本。
{{< admonition tip "评价" true >}}
推荐度：4

优先级：2
{{< /admonition >}}

### Linux Kernel Development, 3rd Edition（Linux内核开发）
讲 Linux 内核的书，都比较老了，这本是相对较新的。作者 Robert Love 是资深的 Linux 社区开发者。
{{< admonition tip "评价" true >}}
推荐度：3

优先级：3
{{< /admonition >}}

### linux-kernel-labs.github.io
> https://linux-kernel-labs.github.io

这份文档是布加勒斯特理工大学的操作系统教学材料，相当详尽，实操性很强，针对 Linux。
{{< admonition tip "评价" true >}}
推荐度：3

优先级：4
{{< /admonition >}}

### Linux Insides
> https://0xax.gitbooks.io/linux-insides

这是一位国外网友还在编写中的 Linux 内核书籍，但内容已经非常多了。
{{< admonition tip "评价" true >}}
推荐度：3

优先级：4
{{< /admonition >}}

## POSIX系统编程
### Advanced Programming in the UNIX Environment, 3rd Edition（UNIX环境高级编程）
这本书通常被称为 APUE，是学习类 UNIX 系统编程的不二选择。这本书历史也比较悠久，前两版的作者 Richard Stevens 不幸于1999年去世，直到2013年才有人续写了第三版。虽然是以 UNIX 系统为目标，但因为 UNIX 和 Linux 都兼容POSIX接口，所以对于学习 Linux 系统编程也是完全没有问题的。
{{< admonition tip "评价" true >}}
推荐度：4

优先级：2
{{< /admonition >}}

### Linux System Programming, 2nd Edition（Linux系统编程）

这本书同样是讲系统编程，对 Linux 的针对性更强，同样是 Robert Love 的作品。他自己对这本书的评价：这是 Linux 时代的 APUE（is Stevens for the Linux era）。
{{< admonition tip "评价" true >}}
推荐度：3

优先级：3
{{< /admonition >}}

## 算法
### Algorithm, 4th Edition（算法）
我最推荐的是这本，作者是 Knuth 教授的弟子，Knuth 教授本人的书我就不推荐了，估计没几个人会有时间去读。这本书的一大特色就是注重实用，图表很详尽。示例代码是 Java。
{{< admonition tip "评价" true >}}
推荐度：4

优先级：2
{{< /admonition >}}

### Introduction to Algorithm, 3rd Edition（算法导论）
麻省理工的著名教材，重理论，数学多。用伪代码表示。我只翻过几次。
{{< admonition tip "评价" true >}}
推荐度：4

优先级：3
{{< /admonition >}}

## 软件设计
经常看代码的人会有一种感觉，为什么别人的代码设计都很精妙，而自己的代码就像小学生作文一样。你学习过面向对象设计，当书本上讲到封装、继承、多态的时候，你不停地点头，觉得好有道理哦，但放下书本写出来的程序还是一塌糊涂。

很多时候我们会混淆工具和思想。编程语言给你的特性是工具，你可以设计一个子类来继承父类的特性并且增加自己的特性，你可以让多个不同的对象看起来是一个样子的，这些都是工具。但编程语言没法告诉你这么多对象应该如何组织、分工以及交互，设计模式能回答这些问题。

### Design Patterns Elements of Reusable Object-Oriented Software（设计模式）
所有设计模式的其他书籍，都可以认为是这本书的衍生品。由于书籍的作者有四个人，这本书也被称为 Gang of Four。这本书的例子是用 C++ 写的，所以需要基本的 C++ 基础。有丰富编程经验的人，看这本书是最好的选择，非常简单明了。
{{< admonition tip "评价" true >}}
推荐度：5

优先级：2
{{< /admonition >}}

### Head First Design Patterns（深入浅出设计模式）
这本书是畅销的 Head First 系列，好处就是讲得很生动，对于编程经验不多的人是比较好的选择，缺点就是长。我看了一点，感觉节奏太慢了，像几十集的言情剧。
{{< admonition tip "评价" true >}}
推荐度：4

优先级：2
{{< /admonition >}}

### Test Driven Development（测试驱动开发）
TDD 是一种重要的方法论。TDD 不能帮你发现新的缺陷，但 TDD 能帮你大大改善设计。这本书是 TDD 方法论的鼻祖，得过 Jolt 大奖，整本书有大量的实例，每一章节通常只有两三页，阅读这本书的体验本身也有点 TDD 的感觉，小步快跑。这本书也已经绝版了，二手书较贵。
{{< admonition tip "评价" true >}}
推荐度：5

优先级：1
{{< /admonition >}}

### Agile Software Development, Principles, Patterns and Practices（敏捷软件开发——原则、模式与实践）
也是获 Jolt 大奖的书，早期普及敏捷开发的重要书籍。这本书的融合了设计模式、TDD、架构原则等重要主题，辅助以大量的实例代码，让我们明白敏捷最重要的事情是设计出可以让我们敏捷起来的软件。

这本书已经很难买到了，二手的价格偏高。
{{< admonition tip "评价" true >}}
推荐度：4

优先级：2
{{< /admonition >}}

## 计算机网络
### Computer Networks（计算机网络）
我上大学时代的计算机网络教材的不二选择，不知道现在还用不用。基于 OSI 七层模型来阐述。和前面的 Modern Operating System 同样来自 Tanebaum 教授。
{{< admonition tip "评价" true >}}
推荐度：3

优先级：2
{{< /admonition >}}

### TCP/IP Illustrated Volume 1（TCP/IP详解）
来自已故大师 Richard Stevens 的另一本大作，想起了我刚毕业那几年。时隔很多年，也是由其他人又更新了一版。这本书专注TCP/IP。

这本书还有 Volume 2，专讲实现。一千多页的书，没有人再为它更新了 T_T。
{{< admonition tip "评价" true >}}
推荐度：4

优先级：3
{{< /admonition >}}

## 密码学


## 工具
### Pro Git, 2nd Edition
> https://git-scm.com/book/en/v2

这本书是 Git 官方推荐的书籍，它本身也是个开源项目，处于不断更新中，官网的 pdf 下载链接会定期更新，也可以下载项目的源码自己编译出最新的 pdf。

Git 已经不仅仅是一个版本管理工具，已经成为一种管理数据的通用手段。做软件开发的人，对Git必须非常熟悉。我们鼓励大家跟着这本书，使用命令行工具 Git 来一步步学习，养成使用原生软件的习惯。我们可以接受大家使用各种带 GUI 的二手 Git 工具，但我们会按照原生 Git 工具能达成的结果去设定要求和规范，而不会关心二手的工具能做哪些不能做哪些。
{{< admonition tip "评价" true >}}
推荐度：5

优先级：1
{{< /admonition >}}

## 管理
### The Mythical Man Month（人月神话）
对于软件项目的管理，无论如何都绕不开这本书。在写这篇文档的时候，作者Brooks教授刚去世三周。这本书提出了一些时隔四十几年后依然很重要的观点，比如：什么情况下给项目加人是可以加快进度的，什么情况下加人会适得其反；为了保证设计的一致性，开发团队的角色应该如何设置；以及最终的结论——没有银弹。
{{< admonition tip "评价" true >}}
推荐度：3

优先级：3
{{< /admonition >}}
## 其他好书（未读）
### The Joy of Cryptography
> https://joyofcryptography.com

这是一本很新的密码学教材。

### A Graduate Course in Applied Cryptography
> http://toc.cryptobook.us

斯坦福的教材，免费的书，虽然名字看起来很土，但内容非常详尽，而且难能可贵的是，非常新，持续更新中。

### Algorithms (by Jeff Erickson)
> http://jeffe.cs.illinois.edu/teaching/algorithms/

高级算法教程，需要有基础算法的基础。

### Introduction to Applied Linear Algebra – Vectors, Matrices, and Least Squares
> https://web.stanford.edu/~boyd/vmls/

斯坦福大学的线性代数教程。

### Linear Algebra Done Right
> https://linear.axler.net

又一本线性代数。

### Is Parallel Programming Hard, And, If So, What Can You Do About It?
> https://mirrors.edge.kernel.org/pub/linux/kernel/people/paulmck/perfbook/perfbook.html

又是一本很厉害的书，讲并发的。近千页。更新很勤。作者 Paul McKenney 是并发领域的专家，Linux RCU 实现的维护者。

### Rust Atomics and Locks
> https://marabos.nl/atomics/

关于 Rust 的 原子操作和锁的书，Paul McKenney 作序。

## 去哪里买绝版书
多抓鱼是一个二手流转平台（有App和微信小程序），应该说大部分书都能找到，但抢手的二手书需要等，可以注册一个到货通知，但也不一定抢得过别人。可以预定，但需要支付多抓鱼专用的积分——「鱼」，获取「鱼」先要卖抢手的书给多抓鱼。

淘宝有少量的二手书商，偶尔能淘到想要的正版二手书，基本看运气。

对于有高质量电子版的绝版书，可以找淘宝上的打印商打印和装订，质量很高，价格很便宜，一本200页的书不到20元，还可以用道林纸。这是盗版行为，不鼓励，但如果已经决定了要买盗版，这种途径的质量远远好于印刷厂翻印的。
