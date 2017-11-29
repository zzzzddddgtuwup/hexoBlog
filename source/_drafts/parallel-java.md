---
title: parallel-java-general
tags: java
---

在并行计算中，WORK定义为所有任务需要的时间，而SPAN为计算图中最长的路径所需要的资源。如果只有一个核，那么需要的时间就是WORK。如果有无数的核，需要的时间为SPAN。
定义Tp为p个核所需要的时间，那么speedup = WORK／Tp的上限可能是p。也可能是WORK/SPAN(这个有一个专门的定义叫做Ideal parallelism).最佳情况是WORK/SPAN >> p,这样就可以通过增加核的数量进行计算的加速而不受到计算图的限制。

在实际应用中很难得到计算图，所以有了Amdahl's law.
q = 串行程序所占的比例
那么speedup <= 1/q
