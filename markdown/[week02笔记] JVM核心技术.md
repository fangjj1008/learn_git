### 第二周 JVM核心技术



### 一、jvm分析调优最重要三个点

#### 并行GC 日志解读与分析

jdk8默认是并行GC

![image-20210405213801641](E:\data\Geek\learn_git\markdown\resource\image-20210405213801641.png)

![image-20210405214543896](E:\data\Geek\learn_git\markdown\resource\image-20210405214543896.png)

没有配置Xmx，Xms这些参数，默认情况下会使用1/4内存作为当前虚拟机最大的堆内存；如果总的物理内存小于1G，会使用1/2作为虚拟机的堆内存。

**最大堆内存、启动堆内存**：-Xmx1g -Xms1g（指定为1g会使得gc更频繁）

**将GC执行的时间戳也打印出来**：PrintGCDateStamps 

**将GC执行日志记在文件里**：-Xloggc:gc.demo.log

**命令：**java  -XX:+PrintGCDetails -XX:+PrintGCDateStamps -Xloggc:gc.demo.log -Xmx1g -Xms1g GCLogAnalysis

**young GC日志分析**

****

![image-20210405221504545](E:\data\Geek\learn_git\markdown\resource\image-20210405221504545.png)

**full GC日志分析**

![image-20210405222859616](E:\data\Geek\learn_git\markdown\resource\image-20210405222859616.png)

**堆内存不够时的GC分析**

![image-20210405223121149](E:\data\Geek\learn_git\markdown\resource\image-20210405223121149.png)

GC的一些概念：

1.youngGC：young区的gc

2.full GC：young区+old区的gc

3.minor GC：小的gc，就是youngGC

4.major GC：大的gc，就是full GC



#### 串行GC 日志解读与分析

指定串行GC

java  -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:+UseSerialGC -Xmx1g -Xms1g GCLogAnalysis

![image-20210405224502681](E:\data\Geek\learn_git\markdown\resource\image-20210405224502681.png)

**在不关闭自适应参数的情况下**（以这里jdk8为例）：

young区对象用不了15次就晋升到了old区，就像并行GC中前几次youngGC就会晋升



堆内存变大的情况：

1.GC次数变少了；2.每次执行时间增加了

![image-20210405225134286](E:\data\Geek\learn_git\markdown\resource\image-20210405225134286.png)



#### 当Xmx（最大堆）、Xms（初始堆）不一致时

只配置Xmx=1g，不配置Xms：会导致full GC比较快

堆内存设置大和小，本身类似于蓄水池作用。最小设置大小，一定要满足应用程序正常运行，能够容纳当前所有对象。

![image-20210405225603401](E:\data\Geek\learn_git\markdown\resource\image-20210405225603401.png)



#### ParNew GC + CMS GC 日志解读与分析

java  -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:+UseConcMarkSweepGC -Xmx1g -Xms1g GCLogAnalysis

![image-20210405231104630](E:\data\Geek\learn_git\markdown\resource\image-20210405231104630.png)

在不同堆内存大小下的一些变化：

1.4G大小：一次CMS GC也没有。只发生5次youngGC，次数也减少了

![image-20210405231216672](E:\data\Geek\learn_git\markdown\resource\image-20210405231216672.png)



#### G1 GC 日志解读与分析

java  -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:+UseG1GC -Xmx1g -Xms1g GCLogAnalysis --- 这样的日志很复杂，容易看晕

**使用PrintGC** 代替 PrintGCDetails（只看概要信息）

java  -XX:+PrintGC -XX:+PrintGCDateStamps -XX:+UseG1GC -Xmx1g -Xms1g GCLogAnalysis 

![image-20210405232007907](E:\data\Geek\learn_git\markdown\resource\image-20210405232007907.png)

4G内存下，G1也只对young区进行了垃圾回收

纯年轻代模式转移暂停：都是年轻代，就不用进行接下来老年代标记到清理的操作

G1的几个阶段日志解读：

![image-20210405232150358](E:\data\Geek\learn_git\markdown\resource\image-20210405232150358.png)



#### GC日志的分析工具

1.GCEasy 在线工具，上传日志就可以自动分析 gceasy.io

![image-20210405232721553](E:\data\Geek\learn_git\markdown\resource\image-20210405232721553.png)

2.GCViewer 一个jar包，拷到本地运行

![image-20210405232753692](E:\data\Geek\learn_git\markdown\resource\image-20210405232753692.png)



#### JVM线程堆栈数据分析

用户自定义的线程：

![image-20210406205851251](E:\data\Geek\learn_git\markdown\resource\image-20210406205851251.png)

JVM内部线程：

![image-20210406210105090](E:\data\Geek\learn_git\markdown\resource\image-20210406210105090.png)

jvm怎么控制在gc时所有的线程都暂停下？（STW基本原理）

-- 安全点技术：jvm调用代码干活时，会在代码里插入很多做检查的安全点。线程在运行时，会**不断的检查自己的安全点**。如果现在gc判断要对整个堆内存操作要暂停，这时就会**改写安全检查状态**，这时所有线程**进入检查点的临界区**时，判断他们需要被暂停住，则在检查点上暂停。等到gc干完活后，闸门打开，所有线程继续工作。

**fastThread**：线程分析工具，网址：fastThread.io

选择一个Dump的JVM线程栈的文件，上传后分析（类似GCEasy，同一家公司）

步骤：

1.jstack -l pid : -l 表示显示线程详细信息，包括锁的信息

2.粘贴到fastThread

![image-20210406212232419](E:\data\Geek\learn_git\markdown\resource\image-20210406212232419.png)



#### 一个Java对象占用多少内存

为什么有空白、对齐？

1.内部对齐：属性需要为32位或64位的整数倍，所以有内部对齐

2.外部对齐：对象本身要为32或64位整数倍

这是cpu决定的

![image-20210406212518471](E:\data\Geek\learn_git\markdown\resource\image-20210406212518471.png)

还可以用 jmap -histo pid 查看对象的内存布局

![image-20210406213705422](E:\data\Geek\learn_git\markdown\resource\image-20210406213705422.png)

 jmap -histo pid > histo.txt

通过分析 histo.txt，会发现包装类非常占用内存空间（eg：Integer占用16个字节）

这是因为64位JVM中，对象头就占了12字节，但是以8字节对齐，所以最少是16字节

![image-20210406214441121](E:\data\Geek\learn_git\markdown\resource\image-20210406214441121.png)

new出来100w个对象，就至少占用了16*100w=16M的空间

所有指向对象的指针，一般32位系统是32位的、64位系统是64位的

堆内存小于32G的系统上，默认开启对象的指针压缩，一个指针是4个字节（而不是64位系统的64位指针）

如果需要使用2维3维数组，尽量使用1维数组去模拟：

（**这里的坑**：在int二维数组中，每个嵌套的数组都是一个对象，至少占16个字节的空间）

![image-20210406215733689](E:\data\Geek\learn_git\markdown\resource\image-20210406215733689.png)

String类对象至少有24个字节的额外开销（其他信息包装）

上图的例子，为了对齐，需要至少填补24-17=7个字节（其中Integer大小注意是引用），多了45%左右的空间。

**一个简单的OOM例子**

HashMap判断俩个值是否相等时，会先判断hashCode值，再去判断equals方法是否true，这里没有重写equals，所以每个对象即使id相同，但都被放进去了

![image-20210406223643652](E:\data\Geek\learn_git\markdown\resource\image-20210406223643652.png)



#### 内存泄漏和内存溢出

内存泄漏：总的堆内存大小一直在缓慢增长，但还没到内存溢出

内存溢出：需要使用的内存超出当前容量，申请不到新的内存了

1.OutOfMemoryError: Java heap space  堆内存溢出

![image-20210406224039758](E:\data\Geek\learn_git\markdown\resource\image-20210406224039758.png)

2.OutOfMemoryError: PermGen space   持久代内存溢出(<=jdk7)

   OutOfMemoryError: Metaspace   元数据区内存溢出（>=jdk8）

![image-20210406224604253](E:\data\Geek\learn_git\markdown\resource\image-20210406224604253.png)

在高版本jdk上，或者cms gc可以打开一个开关来解决这个问题：

-XX:+CMSClassUnloadingEnabled（元数据区class可以被卸载）

如果系统中使用的这些类就是超过了元数据区大小，这样

加这个开关也没有用

3.OutOfMemoryError: Unable to create new native thread

不能够创建本地的线程

![image-20210406224944899](E:\data\Geek\learn_git\markdown\resource\image-20210406224944899.png)

是因为当前创建的线程数已经达到创建值的限制

Xss线程栈大小，jdk默认是1M。最低也有限制（eg：老师的电脑不能小于160k）



#### 对堆内存分析的工具

1.Eclipse MAT

2.jhat

![image-20210406225832863](E:\data\Geek\learn_git\markdown\resource\image-20210406225832863.png)



### 二、GC调优、java技能综合性分析

#### 晋升速率&分配速率

