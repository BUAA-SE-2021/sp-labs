# Week14 Assignment 参考答案

## 1. 在进程部分，有父子进程的说法，有没有父子线程的说法，能说说理由吗？

因为在进程部分，父子进程存在着严格的关系，当父进程消失了，子进程就变成了孤儿进程，同时父子进程使用的是独立的资源区域。而在线程部分，当父子线程可以并发执行，父线程结束了子线程照常执行，同时父子线程共享相同的资源，并无严格关系，因此一般也就没有父子线程的说法。

## 2. 进程和多线程，都有同步和互斥的问题，分析下异同点。对比下两者同步和互斥机制的特点及异同之处

- 同步
  - 线程同步：当多个线程相互协作，存在相互依赖的关系；当它没有得到另一个线程的消息时应等待，直到消息到达时才被唤醒。
  - 进程同步：它是指为了完成某个任务而建立起的两个或多个进程，这些进程在完成任务时，需要协调它们之间的执行顺序。

主要区别在于进程一般需要协调执行顺序，而线程没有，且线程（或进程）之间的通信方式不同。

- 互斥
  - 线程互斥：包括临界资源等的访问，相互线程之间是互斥访问有若干个线程都要使用某一共享资源时，任何时刻最多只允许一个线程去使用，其它要使用该资源的线程必须等待，直到占用资源者释放该资源。
  - 进程互斥：当一个进程访问临界资源时，另一个进程想访问临界资源就必须等待。等第一个进程访问完，第二个进程才能去访问。

进程互斥需遵守空闲让进、忙则等待、有限等待、让权等待的规则。
