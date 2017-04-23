---
layout: post
title: "Farewell, Tensorflow"
subtitle:  "笔记"
author: "icbcbicc"
header-img: "img/gray.png"
---


### Tensorflow太难用了，目前发现的缺点如下（不分先后）

1. if/else语句很不好写，必须用tf.case，而且还没有找到通过它来调用函数的方法。这导致没有办法在使用同一段网络结构代码的情况下通过区分training和validation。

    - Q: tf区分不了，但只要人能够区分就行了，也就是说在training时输入validation的数据，得到结果后注明一下是val再输出就好了。

    - A: 然并卵，虽然能输出val的结果。但我需要在tensorboard中分别画出train和val的相关信息，这就不得不在网络结构中明确区分两者。但我又不想把几乎完全相同的网络写2遍。

2. 修改tensor的值，必须用tf.assign。局部修改1维tensor，有相应的函数。局部修改多维tensor，呵呵，难如上青天。

    - Q: 深度学习中的参数不应该都是自动计算得到的吗？为什么要去手动修改？

    - A: 说的也有道理，但万一真的需要呢？


3.  重新造了一堆轮子，tf不再是一个库，而像是一门语言。使用tf的时候，你根本不会发现自己在用python，因为python有的它也有，而且它还不让你用python自带的（很多函数只接受tensor输入）。

4.  tensor与numpy array难以相互转换。毕竟太好转换就显示不出自己是静态图了。

    - Q: 那么有人会说，你sess.run一下你的tensor不就是输出numpy array了吗？

    - A: 说的也有道理，但是万一你在定义网络结构的时候中间过程需要使用numpy array呢？

    - Q: 你为什么非要用numpy array呢？只用tensor不久没这些破事了吗？

    - A: 的确，但是有时候tensor真的太难用了，参考前面2点。

### 当然，tf还是有一些值得称赞的地方

- 社区活跃，用的人多。

- tensorboard太完美了。

- tensorlayer很好用(大雾)。

### 最后，我想说

我还是滚去用pytorch吧，动态图就是叼。当初入tf的坑主要是被它【骚黄色的官网/充实的教程/详细的文档/直观方便的tensorboard】所诱惑，当然还有google爸爸撑腰。最后事实证明，起确实中看不中用啊。目前打算把之前的几个项目迁移到pytorch上，就当是熟悉一下pytorch了。
听说google也在开发tf的动态图版本Tensorflow fold，但目前看起来没啥人用，先观望一段时间再说。
