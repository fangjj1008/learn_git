





### 第一周 JVM进阶-Java开发者大厂面试必知必会

#### 栈帧

1.操作数栈（不是操作码，是操作码后面跟着的操作数，相当于操作时放变量的地方）

2.局部变量表（本地变量数组，长度固定）

3.一个class引用（指向当前方法在运行时常量池中对应的Class）



变量，通过栈的操作指令，从本地变量表压到栈上，运行完后，再写入到本地变量表



CPU本身相当于操作栈



#### 字节码与助记符

每个操作码是2位十六进制数，也就是说最多有16*16=256个操作码

invokespecial 调用构造函数

![image-20210320225713274](E:\data\Geek\learn_git\markdown\image-20210320225713274.png)

java虚拟机操作最小类型是int

![image-20210320231625249](E:\data\Geek\learn_git\markdown\image-20210320231625249.png)

for+if 流程控制、invokevirtual 调用虚拟方法

![image-20210320233125422](E:\data\Geek\learn_git\markdown\image-20210320233125422.png)



#### 类的加载过程（7步）

（总结为3步：加载、链接、初始化）

1.找到class文件，读出来

2.验证格式；解析里面的字段方法表；然后把所有的符号转换为实际引用

```
前16个字符
“cafe babe” java的模数，class文件的前8个字节
“0000 0034” 十六进制52.0，java版本号
```

3.类相关的初始化，之后整个加载完成



#### 类的加载时机（8个）

![image-20210320235410987](E:\data\Geek\learn_git\markdown\image-20210320235410987.png)

#### 类会被用到，但是不会被初始化的情况（6点）

![image-20210320235904408.png](E:\data\Geek\learn_git\markdown\image-20210320235904408.png)

#### 三种类加载器

![image-20210321001001170](E:\data\Geek\learn_git\markdown\image-20210321001001170.png)

#### 加载器特点

1.双亲委托：

当AppClassLoader要加载一个类时，会首先去找ExtClassLoader有没有加载过这个类，同样ExtClassLoader也会去找BootstrapClassLoader有没有加载过这个类。这样做有个有点，就是同样的类不会被多个加载器加载多次（例如jdk里的类）

2.负责依赖

当加载一个类时，加载器会将它所依赖的类都加载出来

3.缓存加载

当加载过一个类时，就会将其缓存，不会被重复加载



启动类加载器是找不到的，是JVM底层实现，没有办法直接拿到



xxx.class.getClassLoader 拿到的是应用类加载器				

xxx.class.getClassLoader.getParent() 拿到的是拓展类加载器	

他俩在上层父类都是URLClassLoader，可以通过反射拿到ucp，再拿到path，得到加载类的列表



#### 在运行期动态给JVM添加新的jar或类路径文件夹

![image-20210321145912742](E:\data\Geek\learn_git\markdown\image-20210321145912742.png)

jdk9以后：应用类加载器，扩展类加载器，URL类加载器 平级了，没有继承关系，就不能用上述方法拿到URL加载器了，直接用Class.forName(URLClassLoader)方法即可获得URL类加载器。



#### JVM内存模型

栈：局部变量、方法参数、异常处理语句的参数（分为各线程栈，每个线程栈又有多个栈帧）

堆：对象的实例以及他的成员变量、静态变量、数组（由GC算法决定，所有线程共享）

```
以分代GC举例：
堆：年轻代，老年代
年轻代（新生代和存活区）：Eden区、Survivor0区、Survivor1区
```

非堆：元数据区（常量池、方法区），JIT压缩的指针等

其他：JVM自身使用部分

非堆里的元数据区，在jdk8之前叫``持久代``，且在堆里。jdk8和之后叫Metaspace，在非堆里



#### JVM的启动参数

系统属性

![image-20210321153322188](E:\data\Geek\learn_git\markdown\image-20210321153322188.png)

堆内存（一台性能高的机器最好做虚拟化、逗号化，应用隔离出来）

![image-20210321154253658](E:\data\Geek\learn_git\markdown\image-20210321154253658.png)

jdk8默认的GC算法是？

JavaAgent

![image-20210321155104239](E:\data\Geek\learn_git\markdown\image-20210321155104239.png)



#### JDK自带的JVM命令行工具

开发、编译、分发安全（打包安全）相关的命令

![image-20210324214155970](E:\data\Geek\learn_git\markdown\resource\image-20210324214155970.png)

JVM运行起来后，帮助我们了解JVM运行状态的工具

![image-20210324215023620](E:\data\Geek\learn_git\markdown\resource\image-20210324215023620.png)

jps -mlv

jps命令的俩个坑：

1.Linux服务器上很多个账户，不同账户都运行了很多java进程，不同账户权限不一样，jps查看不到全部的进程。

**解决**：使用sudo提升一下当前命令能够探查命令的权限。

2.一个机器上，安装了很多不同jdk版本，可能有些不同的java进程是用不同jdk版本run起来的，当使用某一个版本下的jps查看java进程的时候可能也显示不全，或者看见了但是操作不了。

**解决**：尽量在一台机器上安装一个确定版本的jdk，所有的java程序都使用这个版本的jdk下的java命令来运行。



jstat -gc pid 行数 间隔 

（这时候显示的都是每个区的字节数）

![image-20210324222614081](E:\data\Geek\learn_git\markdown\resource\image-20210324222614081.png)

jstat -gcutil pid 行数 间隔

（这时候显示的都是每个区百分比）

![image-20210324222842999](E:\data\Geek\learn_git\markdown\resource\image-20210324222842999.png)

要加时间戳加-t即可：jstat -gcutil -t pid 1000 1000



#### 对jstat -gc(字节数)/-gcutil(百分比)信息的解读

![image-20210324224251416](E:\data\Geek\learn_git\markdown\resource\image-20210324224251416.png)

![image-20210324223647735](E:\data\Geek\learn_git\markdown\resource\image-20210324223647735.png)



#### jmap（内存使用的一个快照）与jstack（所有的线程情况）

![image-20210324225721198](E:\data\Geek\learn_git\markdown\resource\image-20210324225721198.png)

![image-20210324230036273](E:\data\Geek\learn_git\markdown\resource\image-20210324230036273.png)

运行jstack -pid（等同于kill -3 pid） ，可以在运行java程序的端口把线程情况dump出来（当然jstack窗口有一份）

实测并没有（老师的视频显示了）

![image-20210324233932467](E:\data\Geek\learn_git\markdown\resource\image-20210324233932467.png)



#### jcmd VM的命令

```
jcmd pid VM.version 查看版本
```

```
jcmd pid VM.flags 打印重要的jvm参数
```

![image-20210328125917047](E:\data\Geek\learn_git\markdown\resource\image-20210328125917047.png)

```
jcmd pid VM.command_line 命令行参数
```

![image-20210328130739288](E:\data\Geek\learn_git\markdown\resource\image-20210328130739288.png)

```
jcmd pid VM.system_properties 查看系统变量
```

![image-20210328131140949](E:\data\Geek\learn_git\markdown\resource\image-20210328131140949.png)

```
jcmd pid VM.uptime 看jvm的启动时间
```

![image-20210328132421940](E:\data\Geek\learn_git\markdown\resource\image-20210328132421940.png)



#### jcmd Thread的命令

```
jcmd pid Thread.print 查看当前进程状态，和jstack pid、kill -3 pid效果一样
```

![image-20210328132718063](E:\data\Geek\learn_git\markdown\resource\image-20210328132718063.png)



#### jcmd GC的命令

```
jcmd pid GC.class_histogram  GC的直方图，和jmap pid -histo一样
```

![image-20210328133553457](E:\data\Geek\learn_git\markdown\resource\image-20210328133553457.png)

```
jcmd pid GC.heap_info 可以看到当前old区、young区、meta区整体大小使用了多少，使用率
```

![image-20210328133751887](E:\data\Geek\learn_git\markdown\resource\image-20210328133751887.png)

**在比较高（jdk8及以上）的版本上，一般推荐大家使用jcmd**

jdk8还有俩个js命令，下面讲下jrunscript和jjs



#### jrunscript 有crul、执行js的作用

```
jrunscript -e "cat('http://www.baidu.com')"
```

![image-20210328134158278](E:\data\Geek\learn_git\markdown\resource\image-20210328134158278.png)

这个命令和curl是一样的

![image-20210328134238332](E:\data\Geek\learn_git\markdown\resource\image-20210328134238332.png)

还有其他的用法

![image-20210328135254707](E:\data\Geek\learn_git\markdown\resource\image-20210328135254707.png)

#### jjs 可以理解为一个js控制台

和chrome打开f12的控制台类似，类似于shell执行python

![image-20210328134703525](E:\data\Geek\learn_git\markdown\resource\image-20210328134703525.png)



#### jconsole 可视化工具

![image-20210328222049636](E:\data\Geek\learn_git\markdown\resource\image-20210328222049636.png)



#### jvisualvm 高于jdk8.275等以上，jdk自身不带

jdk8.231及以下还有。高版本jdk可以去官网下载：VisualVM.github.io

jvisualvm 强大的地方在于它的抽样器：可以知道一定时间段内，内存有哪些对象、实例数、以及他们一些变化的量。这时就可以用抽样器进行一个监控。

![image-20210328222829220](E:\data\Geek\learn_git\markdown\resource\image-20210328222829220.png)

是进行JVM内部状态分析的一个非常重要的手段。



#### IDE中的JVM图形化工具：visualGC（eclipse,idea都有插件）

![image-20210328223109557](E:\data\Geek\learn_git\markdown\resource\image-20210328223109557.png)



#### jmc  --Java Mission Control 最强大的图形化工具

不仅有上述几款软件的功能，还有一个叫做 Java飞行记录器的功能：

1.可以记录一段时间内JVM内部的变化（内存、cpu等）

2.不仅监控和回收原始数据，更对原始数据进行了汇总加工分析，还可以对代码进行分析（热点包、热点类），热点执行了多少次，占的百分比。

3.2020.7之后，不再打包到jdk7（7u271），8（8u261）及之后版本

4.如果jmc连不上进程，需要在启动的进程加上如下参数开启jmx



#### jenv 管理多个不同的jdk环境

了解一下



#### GC的背景及一般原理

内存资源的稀缺性，共享使用

**引用计数** 没有被引用或者引用次数随时间推移都使用完了，即可回收；被引用1次引用计数+1 。

把这个原理对应到jvm的堆内存的管理上，即对象被引用，引用次数为0则被回收。

**循环依赖** 形成循环依赖的环的对象无法被回收，久而久之->内存泄漏（这些对象越来越多，真正能使用的内存越来越少，造成程序崩溃）->内存溢出。

**引用跟踪** 从根对象（？）出发，找到他们引用的对象，再找到这些对象引用的对象....找到所有可达的对象，即我们当前程序正在使用的对象集合；剩下的就是不可达对象，需要被回收掉。

**引用跟踪的实现：**

1.标记-清除 算法Mark and Sweep

![image-20210331205829547](E:\data\Geek\learn_git\markdown\resource\image-20210331205829547.png)

STW：Stop the World 让世界停止下来，作用是标记和清除对象（也叫JVM的GC暂停）

2.标记-整理 算法

-- 不仅清除了，还压缩了

清除有时候也叫内存整理；碎片整理有时候也叫压缩

**分代假设**

![image-20210331210428149](E:\data\Geek\learn_git\markdown\resource\image-20210331210428149.png)

有数据的存活区叫from，目标的存活区叫to

存活过一定的GC周期次数（15次  -XX:+MaxTenuringThreshold），就会被挪到老年代

youngGC只作用young区；oldGC只作用old区

oldGC直接标记-清除不可达-移动到老年代空间开始的地方（直接做了内存的碎片整理）

老年代为什么清除后，直接移动处理？

是因为没有像年轻代分区，做复制处理

**可以作为GCRoot的对象**

  ![image-20210331211230928](E:\data\Geek\learn_git\markdown\resource\image-20210331211230928.png)

GC在扫描根对象的速度是非常快的，因为线程个数是有限的

**所以** 这块在做STW的时候暂停的时间，和堆内存大小、对象总数量没有关系，而是由当前存活对象的总数量有关系

**所以** 各种GC算法增加堆内存的时候，并不会影响这块（做GC标记）产生的暂停时间



**三种GC算法总结**

1.标记-清除：Young区

2.标记-复制：Eden+S0->S1，Young区->Old区

3.标记-清除-整理：Old区



#### 串行/并行 GC

串行GC：Serial GC        -XX:+UseSerialGC

在串行GC工作时，不管是young区，还是old区，都会出发STW

年轻代：标记-复制；老年代：标记-清除-整理

CPU占用率高、暂停时间长，仅适合几百MB的堆内存使用

单核CPU比较有用

改进版ParNewGC，可以对年轻代进行并行的垃圾回收，配合CMS使用

![image-20210331221529336](E:\data\Geek\learn_git\markdown\resource\image-20210331221529336.png)

并行GC：Parallel GC

在JDK678，并行GC都是默认GC；Java9及之后的版本，默认的GC是G1

年轻代和老年代垃圾回收都会出发STW

年轻代：标记-复制；老年代：标记-清除-整理

在垃圾回收的时候使用多个线程并行的来处理垃圾

-XX:ParallelGCThreads:来指定用来垃圾回收的线程数

默认情况下线程数就是核心数，调用所有的CPU资源，创建跟CPU核心数数量相等的GC线程处理垃圾

**总结**   并行GC设定的目标就是增加整个系统提供的吞吐量

1.在一定的时间周期内，把所有线程都用来垃圾回收（在一定周期内，STW会更短）

2.俩个GC周期相隔时间内，没有GC线程在运行（所有线程都去做业务处理了），不会额外消耗系统资源

![image-20210331222718614](E:\data\Geek\learn_git\markdown\resource\image-20210331222718614.png)

吞吐量最优不一定意味STW最短

**吞吐量是指应用程序线程用时占程序总用时的比例：**

**应用程序线程用时 / 应用程序线程用时 + GC用时**



#### 运行时指定GC的演示

![image-20210331224028609](E:\data\Geek\learn_git\markdown\resource\image-20210331224028609.png)

![image-20210331224324682](E:\data\Geek\learn_git\markdown\resource\image-20210331224324682.png)



#### CMS GC

最大可能性的并发的标记清除垃圾回收算法

 -XX:+UseConcMarkSweepGC启动后，会：

1.年轻代使用的 标记-复制 算法（对串行GC算法的并行改进，ParNew算法）

2.老年代使用的 标记-清除 算法（没有整理，即CMS算法）

![image-20210401204046172](E:\data\Geek\learn_git\markdown\resource\image-20210401204046172.png)

1.不进行碎片整理，即压缩。而是使用free-lists来记录所有有用的内存

2.回收老年代时，业务线程和GC线程同时进行（默认情况下GC并发线程数等于CPU核心数的1/4）

#### CMS GC 6个阶段

阶段1，4是要做STW的，并发的不需要

**阶段1-初始标记（STW）：**（因为做了STW，所以这次标记很精确）

![image-20210401204817958](E:\data\Geek\learn_git\markdown\resource\image-20210401204817958.png)

标记所有的根对象 GCRoot

标记所有的根对象直接引用的对象

标记年轻代指向老年代的对象（JVM内部有个remmber Set，RSet集合：专门用来记录跨代之间对象引用的关系。这一步主要是扫描RSet里对老年代一些引用的这些对象）

**阶段2-并发标记**

![image-20210401211022597](E:\data\Geek\learn_git\markdown\resource\image-20210401211022597.png)

把所有的对象都标记完（这个阶段是GC和业务线程并发执行的，随时可能会因为业务线程执行变化而变化，不一定非常精确）

**阶段3-并发预清理**

![image-20210401211125482](E:\data\Geek\learn_git\markdown\resource\image-20210401211125482.png)

卡片标记（card marking）：把前面发生变化的对象识别出来，所在的区域标记为脏区。这样就知道整个老年代，哪些区域存在着一些脏的对象（引用关系发生变化的对象）

**阶段4-最终标记（STW）**：比较短暂的STW

![image-20210401212445333](E:\data\Geek\learn_git\markdown\resource\image-20210401212445333.png)

理清楚前面脏区的逻辑。经过这次之后，所有对象的引用关系就被固定下来了。

CMS会在young区对象比较少的时候做最终标记的这个事。

**阶段5-并发清理**

![image-20210401212804473](E:\data\Geek\learn_git\markdown\resource\image-20210401212804473.png)

并发的将不再使用的对象，从堆内存里清理掉

**阶段6-并发重置**

![image-20210401212917613](E:\data\Geek\learn_git\markdown\resource\image-20210401212917613.png)

重置JVM内部的，CMS相关的各种数据和状态，为下一次GC循环做准备



**CMS GC的使用**

java -Xmx1g -Xms1g -XX:-UseAdaptiveSizePolicy -XX:+UseConcMarkSweepGC -jar gateway-server-0.0.1-SNAPSHOT.jar

（关掉自适应选项，它会自动的配置一些参数）

![image-20210401214122831](E:\data\Geek\learn_git\markdown\resource\image-20210401214122831.png)

jmap -heap 9492

![image-20210401220323022](E:\data\Geek\learn_git\markdown\resource\image-20210401220323022.png)

**当设置-Xmx=1g，且java8时：**

1.并行GC的young区大小 = 341M

2.CMS GC的young区大小 = 332.8M

在java8中，CMS算法的 young区Max = 64M * GC线程数4 * 13/10（固定的参数）

我们可以通过配置JVM的启动参数中的GC并行线程数就可以来调整young区占用堆内存大小

*这里CMS是老年代GC，为啥还要管young区的事情？*

**当设置-Xmx=1g，且java11时：**

并行GC和CMS是一样的 341M



#### G1 GC

垃圾优先，哪块垃圾多，就优先处理

进一步控制STW，启发式算法

-XX:+UseG1GC -XX:MaxGCPauseMillis=50  表示GC暂停时间控制在50毫秒（但不一定能达到）

![image-20210401222830806](E:\data\Geek\learn_git\markdown\resource\image-20210401222830806.png)

![image-20210401223937680](E:\data\Geek\learn_git\markdown\resource\image-20210401223937680.png)

把整个堆内存划分成很多小块region，默认是2048个，这块可能是eden、s、old区。每次只处理垃圾最多的小块，需要回收的这些region称作是collection set（GC的会收集）

每次GC暂停都会回收所有年轻代的region，只会处理一部分标记成老年代的内存块region

在并发阶段估算每个region的垃圾数量

**G1 GC-配置参数**

![image-20210401224024239](E:\data\Geek\learn_git\markdown\resource\image-20210401224024239.png)

![image-20210401224639534](E:\data\Geek\learn_git\markdown\resource\image-20210401224639534.png)

**IHOP**：在老年代使用量超过整个堆45%的时候（默认），就启动老年代的垃圾回收



#### G1 GC 的注意事项

![image-20210401225733662](E:\data\Geek\learn_git\markdown\resource\image-20210401225733662.png)



#### G1 GC 启动参数和内存布局

![image-20210401230746966](E:\data\Geek\learn_git\markdown\resource\image-20210401230746966.png)



#### ZGC java11

通过解锁实验性的开关，打开它

和shenadoahGC一样，从jdk15之后支持MacOs和windows，就可以直接使用了（不用打开实验性开关）

![image-20210403203132095](E:\data\Geek\learn_git\markdown\resource\image-20210403203132095.png)

1.暂停时间非常短，一般在10ms内（无论堆内存大小）

2.支持堆内存范围广，jdk13后支持16TB

3.与G1比，标准的测试吞吐量下降一般不超过15%

4.java11只支持Linux/64，jdk15后支持MacOs和windows

jdk8中就可以配置使用（jdk做了模块化处理，使用很方便）

着色指针、读屏障？



#### Shenandoah GC Java 12

由于是红帽开发，在RedHat和同源的Centos上，把很多好的JVM相关的特性都往回兼容到了jdk8，9，10。即如果在红帽系统上安装了JDK8高版本以上，就可以使用。

Mac和windows一样到jdk15可以使用

jdk12解锁实验性开关，再+UseShenandoahGC就可以配置使用

![image-20210403203615455](E:\data\Geek\learn_git\markdown\resource\image-20210403203615455.png)

能够更大限度的使用GC线程和业务处理线程并发运行，极大改善GC暂停时间。

橙色为GC暂停：

![image-20210403204128760](E:\data\Geek\learn_git\markdown\resource\image-20210403204128760.png)

ShennandoahGC在young区也进行了暂停



#### GC总结

![image-20210403204619177](E:\data\Geek\learn_git\markdown\resource\image-20210403204619177.png)

​                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             

#### 各个GC对比

![image-20210404155400222](C:\Users\fjj\AppData\Roaming\Typora\draftsRecover\resource\image-20210404155400222.png)

![image-20210404155647859](C:\Users\fjj\AppData\Roaming\Typora\draftsRecover\resource\image-20210404155647859.png)



#### 常见的GC组合

1.串行化：单线程低延迟

2.ParNew + CMS：多线程低延迟

3.Parallel Scavenge + Parallel ScavengeOld：多线程高吞吐量

![image-20210404160035473](C:\Users\fjj\AppData\Roaming\Typora\draftsRecover\resource\image-20210404160035473.png)



#### GC如何选择

![image-20210404160304784](C:\Users\fjj\AppData\Roaming\Typora\draftsRecover\resource\image-20210404160304784.png)

TODO：不同jdk的默认GC是什么？

![image-20210404160600493](C:\Users\fjj\AppData\Roaming\Typora\draftsRecover\resource\image-20210404160600493.png)