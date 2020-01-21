
    
##  <div style="border-bottom:1px solid #DEDEDE">JVM内存模型</div>
<div align=center><img height=250px src="https://user-gold-cdn.xitu.io/2020/1/21/16fc5a2456c8def8?w=858&h=376&f=png&s=6078360"></img></div>
<blockquote style="line-height: inherit; display: block; padding: 15px 15px 15px 1rem; font-size: 0.9em; margin: 1em 0px; color: rgb(0, 0, 0); border-left: 5px solid rgb(239, 112, 96); background: rgb(239, 235, 233); overflow: auto; overflow-wrap: normal; word-break: normal;">思考：JVM为什么有1个Eden区和2个Survivor区。 </blockquote>


##  <div style="border-bottom:1px solid #DEDEDE">程序计数器</div>
<strong style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; font-weight: bold; color: rgb(233, 105, 0);">产生</strong>：由于CPU时间片轮转机制，众多线程在并发执行过程中，任何一个确定的时刻，一个处理器或者多核处理器中的一个内核，只会执行某个线程中的一条指令。为了保证恢复时指令执行的正确性，产生了程序计数器</br>

<strong style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; font-weight: bold; color: rgb(233, 105, 0);">定义</strong>：程序计数器是一个非常小的内存空间，它可以看成是当前线程所执行的字节码的行号指示器。如果线程执行的是java方法，这个计数器记录的是正在执行的字节码指令的地址。如果正在执行的是native方法，那么这个计数器的值undefined。</br>

<strong style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; font-weight: bold; color: rgb(233, 105, 0);">特点</strong>：占用内存小、线程私有、唯一一个不会出现OutOfMemoryError的区域、随着线程的创建而创建，随着线程的结束而死亡

##  <div style="border-bottom:1px solid #DEDEDE">本地方法栈</div>
虚拟机栈是为虚拟机执行Java方法(也就是字节码)服务,本地方法区则为虚拟机使用到的Native方法服务.</br>

在JVM内存布局中，虚拟机栈“主内”，而本地方法栈“主外”。这个“内外”是针对JVM来说的，本地方法栈为Native方法服务。线程开始调用本地方法时，会进入一个不再受JVM约束的世界。</br>

本地方法可以通过JNI(Java NativeInterface)来访问虚拟机运行时的数据区，甚至可以调用寄存器,具有和JVM相同的能力和权限。当大量本地方法出现时,势必会削弱JVM对系统的控制力,因为它的出错信息都比较黑盒.

##  <div style="border-bottom:1px solid #DEDEDE">Java堆</div>
Java虚拟机所需要管理的内存中最大的一块。</br>

堆内存物理上不一定要连续，只需要逻辑上连续即可,就像磁盘空间一样。堆是垃圾回收的主要区域,所以也被称为GC堆。</br>

堆的大小既可以固定也可以扩展，主流的虚拟机堆的大小是可扩展的(通过-Xmx和-Xms控制)，但是在通常情况下，服务器在运行过程中，堆空间不断地扩容与回缩，势必形成不必要的系统压力，通常JVM的Xms和Xmx设置成一样大小，避免在GC后调整堆大小时带来的额外压力。</br>

当线程请求分配内存，但堆已满,且内存已满无法再扩展时，就抛出OutOfMemoryError。</br>

整个Java虚拟机只有一个堆,所有的线程都访问同一个堆.它是被所有线程共享的一块内存区域,在虚拟机启动时创建。

##  <div style="border-bottom:1px solid #DEDEDE">Java虚拟机栈</div>
JVM中的虚拟机栈是描述<strong style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; font-weight: bold; color: rgb(233, 105, 0);">Java方法执行的内存区域</strong>，它是线程私有的，而且随着线程的创建而创建,随着线程的死亡而死亡栈中的元素用于支持虚拟机进行方法调用，每一个方法从调用直至执行结束，就对应着一个栈帧从虚拟机栈中创建入栈到出栈销毁的过程。</br>

<strong style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; font-weight: bold; color: rgb(233, 105, 0);">StackOverFlowError</strong> :若Java虚拟机栈的内存大小不允许动态扩展,那么当线程请求的栈深度大于虚拟机允许的最大深度时(但内存空间可能还有很多),就抛出此异常。</br>

<strong style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; font-weight: bold; color: rgb(233, 105, 0);">OutOfMemoryError</strong>:若Java虚拟机栈的内存大小允许动态扩展,且当线程请求栈时内存用完了,无法再动态扩展了,此时抛出该异常

<div align=center><img height=360px src="https://user-gold-cdn.xitu.io/2020/1/21/16fc5bdc974bfef6?w=748&h=513&f=png&s=423177"></img></div>
<strong style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; font-weight: bold; color: rgb(233, 105, 0);">局部变量表</strong>：</br>
　&emsp; 存放方法参数和局部变量</br>
　&emsp; 如果是非静态方法，则在index[0]位置上存储的是方法所属对象的实例引用，随后存储的是参数和局部变量</br>

<strong style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; font-weight: bold; color: rgb(233, 105, 0);">操作栈</strong>：</br>
　&emsp; 操作栈是一个初始状态为空的桶式结构栈</br>
　&emsp; 在方法执行过程中，会有各种指令往栈中写入和提取信息</br>

<strong style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; font-weight: bold; color: rgb(233, 105, 0);">动态链接</strong>：</br>
　&emsp; 每个栈帧中包含一个在常量池中对当前方法的引用，目的是支持方法调用过程的动态连接</br>

<strong style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; font-weight: bold; color: rgb(233, 105, 0);">方法返回地址</strong>：</br>
&emsp;&emsp; 方法正常退出，正常执行到任何方法的返回字节码指令，如RETURN、IRETURN、ARETURN等</br>
　&emsp; 异常退出



##  <div style="border-bottom:1px solid #DEDEDE">i++与++i在内存中发生了什么</div>
<div align=left><img height=500px src="https://user-gold-cdn.xitu.io/2020/1/21/16fc5c6d28633e12?w=1070&h=662&f=png&s=406015"></img></div>

##  <div style="border-bottom:1px solid #DEDEDE">元空间</div>
JDK8中使用了元空间替代原先的方法区，元空间在本地内存中分配，只要本地内存足够，不会出现像永久代中java.lang.OutOfMemoryError。但是对永久代的设置参数PermSize和MaxPermSize也会失效。</br>

在JDK8及以上版本中，设定MaxPermSize参数，JVM在启动时并不会报错，但是会提示:</br>
&emsp;&emsp;~Java HotSpot 64Bit Server VM warning:ignoring option MaxPermSize=2560m; support was removed in 8.0</br>

元空间的大小可以使用参数MaxMetaspaceSize来限制本地内存分配给类元数据的大小。</br>
    
在JDK8里，Perm 区中内容分配</br>
&emsp;&emsp;~字符串常量移至堆内存</br>
&emsp;&emsp;~其他内容包括类元信息、字段、静态属性、方法、常量等都移动至元空间


##  <div style="border-bottom:1px solid #DEDEDE">方法区</div>
JDK8之前，方法区作为堆的逻辑分区存在，信息一般都要长期存在，方法区空间由参数-XX:PermSize和-XX:MaxPermSize设置，在某些场景下，如果动态类加载的过多，比较容易产生OOM错误。</br>

类及方法的信息等比较难确定其大小，因此对于永久代的大小指定比较困难，太小容易出现永久代溢出，太大则容易导致老年代溢出（因为堆空间有限，此消彼长）。</br>

永久代会为 GC 带来不必要的复杂度，并且回收效率偏低  

##  <div style="border-bottom:1px solid #DEDEDE">运行时常量池</div>

方法区中存放三种数据：类信息、常量、静态变量、即时编译器编译后的代码。其中常量存储在运行时常量池中。</br>

在近三个JDK版本（6、7、8）中， 运行时常量池的所处区域一直在不断的变化，

JDK6时是方法区的一部分

JDK7放到了堆内存中

JDK8之后出现了元空间，它又回到了方法区。
<blockquote style="line-height: inherit; display: block; padding: 15px 15px 15px 1rem; font-size: 0.9em; margin: 1em 0px; color: rgb(0, 0, 0); border-left: 5px solid rgb(239, 112, 96); background: rgb(239, 235, 233); overflow: auto; overflow-wrap: normal; word-break: normal;">思考：JDK7运行常量池在堆中，因为堆内存大小限制会OOM，JDK8挪到元空间，就不会了OOM了嘛? </blockquote>

##  <div style="border-bottom:1px solid #DEDEDE">OOM</div>
<div align=center><img height=500px src="https://user-gold-cdn.xitu.io/2020/1/21/16fc5fbcb9c5dab1?w=838&h=655&f=png&s=310455"></img></div>

##  <div style="border-bottom:1px solid #DEDEDE">垃圾回收机制与回收算法</div>
<strong style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; font-weight: bold; color: rgb(233, 105, 0);">概述</strong></br>
&emsp;&emsp;垃圾回收（Garbage Collection，GC），顾名思义就是释放垃圾占用的空间，防止内存泄露。有效的使用可以使用的内存，对内存堆中已经死亡的或者长时间没有使用的对象进行清除和回收

<strong style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; font-weight: bold; color: rgb(233, 105, 0);">触发回收</strong></br>
&emsp;&emsp;会在cpu空闲的时候自动进行回收 　</br>
&emsp;&emsp;在堆内存存储满了之后 　</br>
&emsp;&emsp;主动调用System.gc()后尝试进行回收 （可以通过显示配置使该方法失效）</br>

<strong style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; font-weight: bold; color: rgb(233, 105, 0);">回收区域</strong></br>
&emsp;&emsp;由于程序计数器、虚拟机栈、本地方法栈的生命周期都跟随线程的生命周期，当线程销毁了，内存也就回收了，所以这几个区域不用过多地考虑内存回收。</br>
&emsp;&emsp;由于堆和方法区的内存都是动态分配的，而且是线程共享的，所以内存回收主要关注这部分区域。

<div align=center><img height=360px src="https://user-gold-cdn.xitu.io/2020/1/21/16fc5f9118cca045?w=793&h=383&f=png&s=38101"></img></div>

##  <div style="border-bottom:1px solid #DEDEDE">可达性算法分析</div>
<strong style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; font-weight: bold; color: rgb(233, 105, 0);">核心思想</strong></br>
&emsp;&emsp;通过一系列称为“GC Roots”的对象作为起始点，从这些节点开始向下搜索，搜索走过的路径称为“引用链”，当一个对象到 GC Roots 没有任何的引用链相连时(从 GC Roots 到这个对象不可达)时，证明此对象不可用</br>

<strong style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; font-weight: bold; color: rgb(233, 105, 0);">GC ROOT对象</strong></br>
&emsp;&emsp;虚拟机栈中引用的对象。</br>
&emsp;&emsp;方法区中静态属性引用的对象</br>
&emsp;&emsp;方法区中常量引用的对象</br>
&emsp;&emsp;本地方法栈中(Native方法)引用的对象</br>

<strong style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; font-weight: bold; color: rgb(233, 105, 0);">死或生</strong></br>
&emsp;&emsp;判断对象是否真正死亡，需要进行两次标记。对象不可达时，进行第一次标记，之后会进行一次筛选，对象没有重写finalize()方法或者finalize()方法已经被JVM调用过（任何一个对象的ﬁnalize()方法都只会被系统自动调用一次 ），进行第二次标记。筛选后仍然有标记的才会被清理掉。

<div align=center><img height=360px src="https://user-gold-cdn.xitu.io/2020/1/21/16fc5ef56dba18b8?w=1092&h=577&f=png&s=260362"></img></div>

##  <div style="border-bottom:1px solid #DEDEDE">复制算法</div>
将可用内存按容量划分为大小相等的两块，每次只使用其中一块。当这块内存需要进行垃圾回收时，会将此区域还存活着的对象复制到另一块上面，然后再把已经使用过的内存区域一次清理掉。这样做的好处是每次都是对整个半区进行内存回收，内存分配时也就不需要考虑内存碎片等的复杂情况，只需要移动堆顶指针，按顺序分配即可。此算法实现简单，运行高效

<div align=center><img height=360px src="https://user-gold-cdn.xitu.io/2020/1/21/16fc5fb2e997d645?w=990&h=419&f=png&s=51760"></img></div>



##  <div style="border-bottom:1px solid #DEDEDE">标记-清除算法</div>
首先标记出需要回收的对象，在标记完成后进行统一的回收（标记即二次标记的过程）。此算法有两个不足：一是效率问题，标记和清除两个过程效率都不高；二是空间问题，标记清除后会产生大量不连续的内存碎片，内存空间碎片太多的话会导致以后程序在运行中想要分配较大对象的时候，无法找到一块连续的内存空间而导致不得不进行又一次的GC回收
<div align=center><img height=360px src="https://user-gold-cdn.xitu.io/2020/1/21/16fc5fd23938a08f?w=1008&h=427&f=png&s=45582"></img></div>


##  <div style="border-bottom:1px solid #DEDEDE">标记-整理算法</div>
<strong style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; font-weight: bold; color: rgb(233, 105, 0);">复制收集算法</strong>在对象存活率较高时会进行比较多的复制操作，效率会变低。因此在老年代一般不能使用复制算法。针对老年代的特点，提出了一种称之为“标记-整理算法”。标记过程仍与“标记-清除”过程一致，但后续步骤不是直接对可回收对象进行清理，而是让所有存活对象向一端移动，然后直接清理掉端边界以外的内存
<div align=center><img height=360px src="https://user-gold-cdn.xitu.io/2020/1/21/16fc5fddc1687e61?w=1061&h=387&f=png&s=105346"></img></div>

##  <div style="border-bottom:1px solid #DEDEDE">分代收集算法</div>
当前JVM垃圾收集都采用的是"分代收集(Generational Collection)"算法，这个算法并没有新思想，只是根据对象存活周期的不同将内存划分为几块。一般是把Java堆分为新生代和老年代。在新生代中，每次垃圾回收都有大批对象死去，只有少量存活，因此我们采用复制算法；而老年代中对象存活率高、没有额外空间对它进行分配担保，就必须采用"标记-清理"或者"标记-整理"算法。</br>

<strong style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; font-weight: bold; color: rgb(233, 105, 0);">Minor GC</strong>又称为新生代GC : 指的是发生在新生代的垃圾收集。因为Java对象大多都具备朝生夕灭的特性，因此Minor GC(采用复制算法)非常频繁，一般回收速度也比较快。</br>

<strong style="font-size: inherit; line-height: inherit; margin: 0px; padding: 0px; font-weight: bold; color: rgb(233, 105, 0);">Full GC</strong> 又称为老年代GC或者Major GC : 指发生在老年代的垃圾收集。出现了Major GC，经常会伴随至少一次的Minor GC(并非绝对，在Parallel Scavenge收集器中就有直接进行Full GC的策略选择过程)。Major GC的速度一般会比Minor GC慢10倍以上。




##  <div style="border-bottom:1px solid #DEDEDE">联系我/公众号</div>
 <div style="border-radius: 6px;;padding:5px; background-color:gray; width:100%;padding-bottom: 3px;"><font size=4 color=white >失败的原因千奇百怪，成功者的经历不尽相同。成功者之所以成功，归纳为三点:发现错误、定义错误、解决错误。</font></div>
 <div style="border-bottom:3px solid #808080;margin-top:-3px;">
</div>
<blockquote style="line-height: inherit; display: block; padding: 15px 15px 15px 1rem; font-size: 0.9em; margin: 1em 0px; color: rgb(0, 0, 0); border-left: 5px solid rgb(239, 112, 96); background: rgb(239, 235, 233); overflow: auto; overflow-wrap: normal; word-break: normal;"><r bgcolor=red>空号 | 文 【原创】【转载请联系本人】 如果本篇博客有任何错误，请批评指教，不胜感激 ！</br>
战胜心魔，挑战自我。一切不可能，终将化为寻常。</br>
 本文已收入至我的<font color="#FF8040">GitHub</font>仓库，欢迎Star：<a href="https://github.com/JavaKongHao/JavaTeam">github.com/JavaKongHao</a>,里面也有我个人联系方式有什么问题也可以直接找我。</blockquote>

<img src="https://user-gold-cdn.xitu.io/2019/12/2/16ec44a1033c0537?w=877&h=434&f=png&s=119828"></img>
