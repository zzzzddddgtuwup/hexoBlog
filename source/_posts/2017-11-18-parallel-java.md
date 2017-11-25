---
title: 循环的并行优化 - Barrier
date: 2017-11-18 09:51:44
tags: parallel-java
---


如果是pointer-chasing的循环，可以使用finish async来进行并行。
```
  finish{
    for (p = head; p != null; p = p.next) async compute(p);
  }
```

如果提前知道要循环的次数，counted-for循环， 那么可以进一步优化。
```
  forall (i : [0:n-1]) a[i] = b[i] + c[i]
```
也可以用stream来做
a = Intstream.rangeClosed(0, N - 1).parallel().toArray(i -> b[i] + c[i]);
stream有一个缺点，只能返回一个结果。

### Barrier
就是一个waiting/coordination point，所有线程等待都做完，然后再继续
```
  forall (i : [0:n-1]) {
    myId = lookup(i)
    print Hello, myId
    Barrier
    print Bye, myId
  }
```
通过添加barrier，所有的线程会先打印hello，都打印完成后，才会打印bye

这里有一个使用barrier优化的例子：
We have a simple stencil computation to solve the recurrence, Xi = (Xi−1 + Xi+1)/2 with boundary conditions, X0 = 0 and Xn = 1. 

```
for (iter: [0:nsteps - 1]){
  forall (i : [1:n-1]) {
    newX[i] = (oldX[i - 1] + oldX[i + 1]) / 2;
  }
  swap pointers newX and oldX
}
```
这里的缺点是每一个forall都会重新创建N个线程，这里创建了nSteps * (n - 1)个线程。如果使用barrier，我们可以只需要n-1个线程。
```
forall (i : [1:n-1]) {
  for (iter: [0:nsteps - 1]){
    newX[i] = (oldX[i - 1] + oldX[i + 1]) / 2;
    NEXT; //barrier
    swap pointers newX and oldX
  }
}
```

### 循环并行中的grouping/chunking
使用forall的时候，如果N很大，创建很多线程，反而会降低效率.我们可以把数据分块，每个线程负责一块数据。
```
  forall (g: [0:ng-1])
    for (i : mygroup(g, ng, [0:n-1])) a[i] = b[i] + c[i]
```
ng的数量可以是核的数量或者更多。