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