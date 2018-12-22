---
category: java
published: true
layout: post
title: 『 Java 』Java内存分析工具的使用
description: 
---

场景：线上项目的不稳定，经常时不时的就OutOfMemory了，由于线上当时没有开启JMX远程调试，不能从本机上用jvisualvm或jmc工具连上查看，所以学会了几个jdk自带工具用于调试分析线上JAVA程序的健康状况。

## jps(Java Virtual Machine Process Status Tool)

### 常见用法

#### 1. jps
<pre><code>
[root@localhost ~]# jps
11211 ad-server-1.0-SNAPSHOT.jar
11489 ad-server-1.0-SNAPSHOT.jar
11355 ad-server-1.0-SNAPSHOT.jar
11566 ad-server-1.0-SNAPSHOT.jar
11079 ad-server-1.0-SNAPSHOT.jar
11283 ad-server-1.0-SNAPSHOT.jar
11020 ad-server-1.0-SNAPSHOT.jar
3346 Jps
11140 ad-server-1.0-SNAPSHOT.jar
11417 ad-server-1.0-SNAPSHOT.jar
11661 ad-server-1.0-SNAPSHOT.jar
</code></pre>

#### 2. jps -v
<pre><code>
[root@localhost ~]# jps -v
11211 ad-server-1.0-SNAPSHOT.jar ....-Dcom.sun.management.jmxremote.port=5104
11489 ad-server-1.0-SNAPSHOT.jar ....-Dcom.sun.management.jmxremote.port=5108
11355 ad-server-1.0-SNAPSHOT.jar ....-Dcom.sun.management.jmxremote.port=5106
11566 ad-server-1.0-SNAPSHOT.jar ....-Dcom.sun.management.jmxremote.port=5109
11079 ad-server-1.0-SNAPSHOT.jar ....-Dcom.sun.management.jmxremote.port=5102
11283 ad-server-1.0-SNAPSHOT.jar ....-Dcom.sun.management.jmxremote.port=5105
11020 ad-server-1.0-SNAPSHOT.jar ....-Dcom.sun.management.jmxremote.port=5101
11140 ad-server-1.0-SNAPSHOT.jar ....-Dcom.sun.management.jmxremote.port=5103
11417 ad-server-1.0-SNAPSHOT.jar ....-Dcom.sun.management.jmxremote.port=5107
3378 Jps -Dapplication.home=/home/software/jdk1.7.0_60 -Xms8m
11661 ad-server-1.0-SNAPSHOT.jar ....-Dcom.sun.management.jmxremote.port=5110
</code></pre>

#### 3. jps -l
<pre><code>
[root@localhost ~]# jps -l
11211 /root/ad-server/ad-server/bin/ad-server-1.0-SNAPSHOT.jar
11489 /root/ad-server/ad-server/bin/ad-server-1.0-SNAPSHOT.jar
11355 /root/ad-server/ad-server/bin/ad-server-1.0-SNAPSHOT.jar
11566 /root/ad-server/ad-server/bin/ad-server-1.0-SNAPSHOT.jar
11079 /root/ad-server/ad-server/bin/ad-server-1.0-SNAPSHOT.jar
3360 sun.tools.jps.Jps
11283 /root/ad-server/ad-server/bin/ad-server-1.0-SNAPSHOT.jar
11020 /root/ad-server/ad-server/bin/ad-server-1.0-SNAPSHOT.jar
11140 /root/ad-server/ad-server/bin/ad-server-1.0-SNAPSHOT.jar
11417 /root/ad-server/ad-server/bin/ad-server-1.0-SNAPSHOT.jar
11661 /root/ad-server/ad-server/bin/ad-server-1.0-SNAPSHOT.jar
</code></pre>

## jmap(Memory Map)

### 常见用法

#### 1.jmap -heap pid
<pre><code>
[root@localhost ~]# jmap -heap 11211
Attaching to process ID 11211, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 24.60-b09

using thread-local object allocation.
Parallel GC with 4 thread(s)

Heap Configuration:
   MinHeapFreeRatio = 0
   MaxHeapFreeRatio = 100
   MaxHeapSize      = 629145600 (600.0MB)
   NewSize          = 1310720 (1.25MB)
   MaxNewSize       = 17592186044415 MB
   OldSize          = 5439488 (5.1875MB)
   NewRatio         = 2
   SurvivorRatio    = 8
   PermSize         = 67108864 (64.0MB)
   MaxPermSize      = 67108864 (64.0MB)
   G1HeapRegionSize = 0 (0.0MB)

Heap Usage:
PS Young Generation
Eden Space:
   capacity = 208666624 (199.0MB)
   used     = 15378456 (14.666038513183594MB)
   free     = 193288168 (184.3339614868164MB)
   7.369868599589745% used
From Space:
   capacity = 524288 (0.5MB)
   used     = 393216 (0.375MB)
   free     = 131072 (0.125MB)
   75.0% used
To Space:
   capacity = 524288 (0.5MB)
   used     = 0 (0.0MB)
   free     = 524288 (0.5MB)
   0.0% used
PS Old Generation
   capacity = 419430400 (400.0MB)
   used     = 132753144 (126.60326385498047MB)
   free     = 286677256 (273.39673614501953MB)
   31.650815963745117% used
PS Perm Generation
   capacity = 67108864 (64.0MB)
   used     = 23354368 (22.2724609375MB)
   free     = 43754496 (41.7275390625MB)
   34.80072021484375% used

11382 interned Strings occupying 991048 bytes.
</code></pre>

#### 2.jmap -histo:live pid | more
<pre><code>
 num     #instances         #bytes  class name
----------------------------------------------
   1:        555245       17347408  [C
   2:        554924       13318176  java.lang.String
   3:        255309        8169888  java.util.HashMap$Entry
   4:        250554        8017728  com.ad.server.dal.domain.PhoneAreaProvinceInfo
   5:         42038        5806512  [constMethodKlass]
   6:         42038        5392624  [methodKlass]
   7:          3667        4081920  [constantPoolKlass]
   8:          8361        3324072  [B
   9:          3667        2648056  [instanceKlassKlass]
  10:          3206        2474976  [constantPoolCacheKlass]
  11:           980        2215064  [Ljava.util.HashMap$Entry;
  12:         40025        1601000  com.ad.server.dal.domain.IpRange
  13:          3425        1504024  [methodDataKlass]
  14:          2480        1270800  [Ljava.lang.Object;
  15:         17626         705040  org.jboss.netty.handler.codec.http.DefaultHttpHeaders$HeaderEntry
  16:          3943         473952  java.lang.Class
  17:          5142         307448  [S
  18:          5833         306784  [[I
  19:          2327         186160  java.lang.reflect.Method
  20:          5798         185536  java.util.concurrent.ConcurrentHashMap$HashEntry
  21:           259         140896  [objArrayKlassKlass]
  22:          1501         132088  [Lorg.jboss.netty.handler.codec.http.DefaultHttpHeaders$HeaderEntry;
  23:          1495         119600  net.sf.ehcache.Element
  24:          3505         112160  java.util.UUID
  25:          2417          96680  java.lang.ref.SoftReference
  26:          2038          81520  java.util.LinkedHashMap$Entry
</code></pre>

#### 3.jmap -dump:format=b,file=/tmp/java_pid.hprof pid

dump出来的堆文件信息可以用MAT、VisualVM、jhat、jprofile等工具查看
<pre><code>
[root@localhost ~]# jmap -dump:format=b,file=/tmp/java_11211.hprof 11211
Dumping heap to /tmp/java_11211.hprof ...
Heap dump file created
</code></pre>

## jstack(Stack Trace)

### 常见用法

#### 1. jstack -l pid | more
<pre><code>
2015-01-31 17:30:57
Full thread dump Java HotSpot(TM) 64-Bit Server VM (24.60-b09 mixed mode):

"Attach Listener" daemon prio=10 tid=0x00007fb49c001000 nid=0xe64 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

   Locked ownable synchronizers:
	- None

"RMI Scheduler(0)" daemon prio=10 tid=0x00007fb45819c000 nid=0x2e0c waiting on condition [0x00007fb4a0810000]
   java.lang.Thread.State: WAITING (parking)
	at sun.misc.Unsafe.park(Native Method)
	- parking to wait for  [0x00000000dc79d7f0] (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
	at java.util.concurrent.locks.LockSupport.park(LockSupport.java:186)
	at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(AbstractQueuedSynchronizer.java:2043)
	at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(ScheduledThreadPoolExecutor.java:1079)
	at java.util.concurrent.ScheduledThreadPoolExecutor$DelayedWorkQueue.take(ScheduledThreadPoolExecutor.java:807)
	at java.util.concurrent.ThreadPoolExecutor.getTask(ThreadPoolExecutor.java:1068)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1130)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
	at java.lang.Thread.run(Thread.java:745)

   Locked ownable synchronizers:
	- None

"Druid-ConnectionPool-Destroy-1805521817" daemon prio=10 tid=0x00007fb478008800 nid=0x2dea waiting on condition [0x00007fb4a0a12000]
   java.lang.Thread.State: TIMED_WAITING (sleeping)
	at java.lang.Thread.sleep(Native Method)
	at com.alibaba.druid.pool.DruidDataSource$DestroyConnectionThread.run(DruidDataSource.java:1898)

   Locked ownable synchronizers:
	- None

"Druid-ConnectionPool-Create-1805521817" daemon prio=10 tid=0x00007fb478008000 nid=0x2de9 waiting on condition [0x00007fb4a0b13000]
   java.lang.Thread.State: WAITING (parking)
	at sun.misc.Unsafe.park(Native Method)
	- parking to wait for  [0x00000000dc770018] (a java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject)
	at java.util.concurrent.locks.LockSupport.park(LockSupport.java:186)
	at java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.await(AbstractQueuedSynchronizer.java:2043)
	at com.alibaba.druid.pool.DruidDataSource$CreateConnectionThread.run(DruidDataSource.java:1824)
</code></pre>

#### 2. 获取指定JAVA进程内，最耗CPU的线程信息
<pre><code>
[root@localhost ~]# top -Hp 11211
top - 11:43:33 up 17 days, 18:18,  1 user,  load average: 0.00, 0.00, 0.00
Tasks:  39 total,   1 running,  38 sleeping,   0 stopped,   0 zombie
Cpu(s):  7.9%us,  1.4%sy,  0.0%ni, 89.4%id,  0.0%wa,  0.8%hi,  0.6%si,  0.0%st
Mem:   8058056k total,  7918656k used,   139400k free,    91616k buffers
Swap:        0k total,        0k used,        0k free,   176336k cached

  PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND                                                                                                   
11739 root      20   0 2930m 733m 6228 S  1.0  9.3  75:18.03 java                                                                                                       
11740 root      20   0 2930m 733m 6228 R  1.0  9.3  75:16.37 java                                                                                                       
11741 root      20   0 2930m 733m 6228 S  1.0  9.3  72:34.54 java                                                                                                       
11211 root      20   0 2930m 733m 6228 S  0.0  9.3   0:00.00 java                                                                                                       
11213 root      20   0 2930m 733m 6228 S  0.0  9.3   0:02.87 java                                                                                                       
11214 root      20   0 2930m 733m 6228 S  0.0  9.3   0:25.47 java                                                                                                       
11217 root      20   0 2930m 733m 6228 S  0.0  9.3   0:25.37 java                                                                                                       
11218 root      20   0 2930m 733m 6228 S  0.0  9.3   0:25.28 java                                                                                                       
11219 root      20   0 2930m 733m 6228 S  0.0  9.3   0:25.32 java                                                                                                       
11221 root      20   0 2930m 733m 6228 S  0.0  9.3   0:57.26 java                                                                                                       
11223 root      20   0 2930m 733m 6228 S  0.0  9.3   0:12.37 java                                                                                                       
11224 root      20   0 2930m 733m 6228 S  0.0  9.3   0:19.65 java                                                                                                       
11232 root      20   0 2930m 733m 6228 S  0.0  9.3   0:00.00 java                                                                                                       
11233 root      20   0 2930m 733m 6228 S  0.0  9.3   0:09.59 java                                                                                                       
11235 root      20   0 2930m 733m 6228 S  0.0  9.3   0:08.30 java                                                                                                       
11236 root      20   0 2930m 733m 6228 S  0.0  9.3   0:00.00 java                                                                                                       
11244 root      20   0 2930m 733m 6228 S  0.0  9.3   0:00.03 java                                                                                                       
11245 root      20   0 2930m 733m 6228 S  0.0  9.3   0:00.00 java                                                                                                       
11246 root      20   0 2930m 733m 6228 S  0.0  9.3   0:00.00 java
[root@localhost ~]# printf "%x\n" 11739
2ddb
[root@localhost ~]# jstack 11211 |grep 2ddb
"pool-3-thread-1" prio=10 tid=0x00007fb46804d800 nid=0x2ddb waiting on condition [0x00007fb4a1018000]
[root@localhost ~]#
</code></pre>

## jhat(Java Heap Analysis Tool)

###  常见用法

#### 1. jhat -port 8080 /tmp/java_11211.hprof
<pre><code>
$ jhat -port 8080 java_11211.hprof
Reading from java_11211.hprof...
Dump file created Mon Feb 02 10:59:54 CST 2015
Snapshot read, resolving...
Resolving 3700703 objects...
Chasing references, expect 740 dots...........................................
..............................................................................
..............................................................................
..............................................................................
..............................................................................
..............................................................................
..............................................................................
..............................................................................
..............................................................................
.......................................................
Eliminating duplicate references..............................................
..............................................................................
..............................................................................
..............................................................................
..............................................................................
..............................................................................
..............................................................................
..............................................................................
..............................................................................
....................................................
Snapshot resolved.
Started HTTP server on port 8080
Server is ready.
</code></pre>
服务启动后，用浏览器打开`http://localhost:8080/`即可，如下图截图所示：
![jhat-browser]({{ site.baseurl }}/images/jvm_status/jhat-browser.png)

## jstat(Java Virtual Machine Statistics Monitoring Tool)

### 常见用法

#### 1. jstat -gc pid 250 10
<pre><code>
[root@localhost ~]# jstat -gc 11211 250 7
 S0C    S1C    S0U    S1U      EC       EU        OC         OU       PC     PU    YGC     YGCT    FGC    FGCT     GCT   
1024.0 1024.0  0.0   448.0  202752.0 26476.8   409600.0   70803.2   65536.0 22749.4  23149   77.627   3      0.702   78.329
1024.0 1024.0  0.0   448.0  202752.0 28942.7   409600.0   70803.2   65536.0 22749.4  23149   77.627   3      0.702   78.329
1024.0 1024.0  0.0   448.0  202752.0 29258.5   409600.0   70803.2   65536.0 22749.4  23149   77.627   3      0.702   78.329
1024.0 1024.0  0.0   448.0  202752.0 33997.8   409600.0   70803.2   65536.0 22749.4  23149   77.627   3      0.702   78.329
1024.0 1024.0  0.0   448.0  202752.0 35550.0   409600.0   70803.2   65536.0 22749.4  23149   77.627   3      0.702   78.329
1024.0 1024.0  0.0   448.0  202752.0 38130.6   409600.0   70803.2   65536.0 22749.4  23149   77.627   3      0.702   78.329
1024.0 1024.0  0.0   448.0  202752.0 41785.2   409600.0   70803.2   65536.0 22749.4  23149   77.627   3      0.702   78.329
</code></pre>
根据JVM的内存布局

- 堆内存 = 年轻代 + 年老代 + 永久代
- 年轻代 = Eden区 + 两个Survivor区（From和To）

以上统计数据各列的含义为

- S0C、S1C、S0U、S1U：Survivor 0/1区容量（Capacity）和使用量（Used）
- EC、EU：Eden区容量和使用量
- OC、OU：年老代容量和使用量
- PC、PU：永久代容量和使用量
- YGC、YGT：年轻代GC次数和GC耗时
- FGC、FGCT：Full GC次数和Full GC耗时
- GCT：GC总耗时
