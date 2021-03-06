---
typora-root-url: java并发编程实战\image
typora-copy-images-to: java并发编程实战\image
---

###### 并发编程可以总结为三个核心问题：分工、同步、互斥。

所谓**分工**指的是如何高效地拆解任务并分配给线程，

而**同步**指的是线程之间如何协作，

**互斥**则是保证同一时刻只允许一个线程访问共享资源。

例如 ：**Fork/Join** 框架就是一种分工模式，**CountDownLatch** 就是一种典型的同步方式，而**可重入锁**则是一种互斥手段。

工作中的线程协作问题，基本上都可以描述为这样的一个问题：**当某个条件不满足时，线程需要等待，当某个条件满足时，线程需要被唤醒执行**。例如，在生产者 - 消费者模型里，也有类似的描述，“当队列满时，生产者线程等待，当队列不满时，生产者线程需要被唤醒执行；当队列空时，消费者线程等待，当队列不空时，消费者线程需要被唤醒执行。”

**管程**是一种解决并发问题的通用模型，除了能解决线程协作问题，还能解决互斥问题。

导致不确定的主要源头是**可见性问题、有序性问题和原子性问题**，为了解决这三个问题，Java 语言引入了**内存模型**，内存模型提供了一系列的规则，利用这些规则，我们可以避免可见性问题、有序性问题，但是还不足以完全解决线程安全问题。解决线程安全问题的核心方案还是**互斥**。

