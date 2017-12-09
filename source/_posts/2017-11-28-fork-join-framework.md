---
title: fork/join框架
tags: parallel-java
date: 2017-11-13 23:00:59
---

## fork/join框架
它是ExecutorService接口的实现，可以更好地利用多核处理器。这个框架独特的地方在于使用了work-stealing算法。没事做的线程可以从其他忙碌的线程偷一些事情来做。

这个框架里比较关键的是ForkJoinPool这个类，它是AbstractExecutorService接口的拓展。它实现了work-stealing算法，而且可以执行ForkJoinTask(抽象类)的进程。

### 基本的使用
```
	if (my portion of the work is small enough <= some Threshold)
	  do the work directly
	else
	  split my work into two pieces
	  invoke the two pieces and wait for the results
```

Threshold在这里有很关键的作用，因为并行有overhead。如果设置的太小，会使得并行比串行慢

java8 Arrays的parallelSort就是使用了这个框架。

### RecursiveAction vs RecursiveTask
RecursiveTask和RecursiveAction实现了ForkJoinTask。RecursiveTask可以有返回值，而RecursiveAction没有返回值。

#### 使用RecursiveTask求和
```java
Future<E> compute() {
	if (hi - lo > threshold) {
		// sequential calculate
	} else {
		mid = (lo + hi) / 2;
		L = new Sum(lo, mid, arr);
		R = new Sum(mid + 1, hi, arr);
		R.fork(); // create a child thread to compute
		return L.compute() + R.join(); //R.join() will get the value of future
	}
}
```

#### 使用RecursiveAction求和
```java
void compute() {
	if (hi - lo > threshold) {
		// sequential calculate
	} else {
		mid = (lo + hi) / 2;
		L = new Sum(lo, mid, arr);
		R = new Sum(mid + 1, hi, arr);
		R.fork(); // create a child thread to compute
		L.compute();
		R.join(); // make sure R finish the calculation
		this.value = L.getValue() + R.getValue();
	}
}
```

### Memoization 记忆化
可以把future存起来，如果要进行相同的计算就直接返回这个future。

### Determinism
functional determinism 指同样的输入会有同样的输出。
structual determinism 指parallel program对于同样的输入每次会产生同样的计算图。
在某些情况下，我们是不需要determinism的，叫做“benigh non determinism”。比如搜索一个符合某个正则表达式的字符串，任意的字符串都可以。那么即使两次结果不一样也是可以接受的。
### Data race
两种：
Read - write
Write - write



链接：
[forkjoin](https://docs.oracle.com/javase/tutorial/essential/concurrency/forkjoin.html)
[RecursiveAction](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/RecursiveAction.html)