---
layout: post
title:  "Disruptor学习"
date:   2018-03-25 23：22
categories: 源码学习
---

### 目录
- 整体介绍
- 核心概念
- 架构设计
- 示例代码
- 适用场景

# 整体介绍

The LMAX Disruptor is a high performance inter-thread messaging library. 
It grew out of LMAX's research into concurrency, performance and non-blocking algorithms and
today forms a core part of their Exchange's infrastructure.

Disruptor是一个高性能的线程间通信类库，注意是线程间，也就是同一个进程里的多个线程。
说白了就是典型的生产者-消费者模型。通常情况下我们实现生产者-消费者模型大都选择队列+锁的机制。
队列和锁分别存在其自身的缺点，比如队列的使用对内存不太友好，每次生产都会创建新对象，而消费后
对象则不再有用，会导致GC，并且需要维护队头，队尾指针。而锁会导致争用，导致线程切换，再加上
CPU的伪共享问题，造成了这种机制的性能并不是太好。而LMAX采用了RingBuffer这种环形数组结构，
加上预分配内存策略，只需维护一个“序列号”指针；另外通过内存填充避免伪共享问题；通过Unsafe提供的
CAS方式和内存屏障来降低争用，提供性能。

# 核心概念
Before we can understand how the Disruptor works, it is worthwhile defining a number of terms that will be used throughout the documentation and the code. For those with a DDD bent, think of this as the ubiquitous language of the Disruptor domain.

- Ring Buffer: The Ring Buffer is often considered the main aspect of the Disruptor, however from 3.0 onwards the Ring Buffer is only responsible for the storing and updating of the data (Events) that move through the Disruptor. And for some advanced use cases can be completely replaced by the user.
- Sequence: The Disruptor uses Sequences as a means to identify where a particular component is up to. Each consumer (EventProcessor) maintains a Sequence as does the Disruptor itself. The majority of the concurrent code relies on the the movement of these Sequence values, hence the Sequence supports many of the current features of an AtomicLong. In fact the only real difference between the 2 is that the Sequence contains additional functionality to prevent false sharing between Sequences and other values.
- Sequencer: The Sequencer is the real core of the Disruptor. The 2 implementations (single producer, multi producer) of this interface implement all of the concurrent algorithms use for fast, correct passing of data between producers and consumers.
- Sequence Barrier: The Sequence Barrier is produced by the Sequencer and contains references to the main published Sequence from the Sequencer and the Sequences of any dependent consumer. It contains the logic to determine if there are any events available for the consumer to process.
- Wait Strategy: The Wait Strategy determines how a consumer will wait for events to be placed into the Disruptor by a producer. More details are available in the section about being optionally lock-free.
- Event: The unit of data passed from producer to consumer. There is no specific code representation of the Event as it defined entirely by the user.
- EventProcessor: The main event loop for handling events from the Disruptor and has ownership of consumer's Sequence. There is a single representation called BatchEventProcessor that contains an efficient implementation of the event loop and will call back onto a used supplied implementation of the EventHandler interface.
- EventHandler: An interface that is implemented by the user and represents a consumer for the Disruptor.
- Producer: This is the user code that calls the Disruptor to enqueue Events. This concept also has no representation in the code.


# 架构设计

# 示例代码

# 适用场景
