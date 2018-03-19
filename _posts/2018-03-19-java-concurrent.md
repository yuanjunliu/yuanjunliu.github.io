---
layout: post
title:  "Java并发包一些类库"
date:   2018-03-19 21:16
categories: Java并发
---

# atomic 原子性的数据结构
![image](/assets/atomic.jpg)

前面几个都比较容易理解，基本上就是通过CAS方式保证原子性。主要看看后面几个：
```
-->Number
  -->Striped64
    -->LongAdder
    -->LongAccumulator
    -->DoubleAdder
    -->DoubleAccumulator
```
Striped64看名字好像是“条带化”的意思，其实思想类似与ConcurrentHashMap，主要是一个计数器，线程安全的高并发高性能计数器（也可以看作是累加器）  
核心思想是提供一个base字段供没有竞争的时候计数，并发竞争时，每个线程对一个Cell的数组里的一个进行操作。
