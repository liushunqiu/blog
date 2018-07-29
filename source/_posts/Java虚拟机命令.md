---
title: Java虚拟机命令
date: 2018-07-29 13:25:23
tags: Java虚拟机
---

1. <code>jps：虚拟机进程状况工具</code>
```java
a). -l 输出主类的全名,如果进程执行的是Jar包,输出Jar路径
b). -m 输出虚拟机进程启动时传递给主类main()函数的参数
c). -v 输出虚拟机进程启动时传入JVM参数
eg: jps -lvm 
--------------------------
24131 sun.tools.jps.Jps -lmv -Dapplication.home=/usr/java/jdk1.8.0_101 -Xms8m
1541 /data/webdata/apps//search.jar -Xdebug -Xrunjdwp:server=y,transport=dt_socket,address=9960,suspend=n -Xms128M -Xmx256M -Dspring.profiles.active=dev
```
2. <code>jstat：虚拟机统计信息监视工具</code>
```java
a). -class 监视类装载、卸载数量、总空间以及类装在所消耗的时间
eg: jstat -class 19830
Loaded(加载class的数量)       Bytes(所占用空间大小)      Unloaded(未加载数量)  Bytes(未加载占用空间)   Time(时间) 
          8594                      15920.9                   0                      0.0             11.40
------------------------------------------------------------------------------------------------------------------------
b). -gc 监视Java堆状态,包括Eden区、两个survivor区、老年代、永久代等的容量、已用空间、GC时间合计等信息
eg: jstat -gc 19830
S0C(第一个幸存区的大小)    S1C(第二个幸存区的大小)    S0U(第一个幸存区的使用大小)    S1U(第二个幸存区的使用大小)      EC(伊甸园区的大小)       EU(伊甸园区的使用大小)        OC(老年代大小)         OU(老年代使用大小)       MC(方法区大小)     MU(方法区使用大小)    CCSC(压缩类空间大小)   CCSU(压缩类空间使用大小)   YGC(年轻代垃圾回收次数)     YGCT(年轻代垃圾回收消耗时间)    FGC(老年代垃圾回收次数)    FGCT(老年代垃圾回收消耗时间)     GCT(垃圾回收消耗总时间)   
        8192.0             	1536.0      		  0.0  			 1184.2 		  122880.0		   48794.8   		    175104.0  		 28093.7   	       47656.0 		  46746.4 		5928.0 		   5718.4     		   27    			0.383  			 2     		 	0.299    			0.682
------------------------------------------------------------------------------------------------------------------------
c). -gccapacity 监视内容与-gc基本相同,但输出主要关注Java堆各个区域使用到的最大、最小空间
eg: jstat -gccapacity 19830
 NGCMN(新生代最小容量)    NGCMX(新生代最大容量)     NGC(当前新生代容量)     S0C(第一个幸存区的大小)   S1C(第二个幸存区的大小)       EC(伊甸园区的大小)       OGCMN(老年代最小容量)      OGCMX(老年代最大容量)       OGC(老年代容量当前新生成的容量)         OC(老年代大小)       MCMN(方法区最小容量)     MCMX(方法区最大容量)      MC(方法区大小)     CCSMN(最小压缩类空间大小)    CCSMX(最大压缩类空间大小)     CCSC(压缩类空间大小)    YGC(年轻代垃圾回收次数)    FGC(老年代垃圾回收次数) 
 	87040.0 		174592.0 		139264.0 		8192.0 		1536.0 			122880.0   		175104.0 		  349696.0  			 175104.0  		 	175104.0     		 0.0 		1091584.0 		 47656.0     		 0.0 		1048576.0  			 5928.0    		 27   		  2
------------------------------------------------------------------------------------------------------------------------
d). -gcutil 监视内容与-gc基本相同,但输出主要关注已使用空间占总空间的百分比
eg: jstat -gcutil 19830
  S0(第一个幸存区当前使用比例)     S1(第二个幸存区当前使用比例)     E(伊甸园区使用比例)      O(老年代区使用比例)      M(方法区使用比例)     CCS(压缩使用比例)    YGC(年轻代垃圾回收次数)     YGCT(年轻代垃圾回收消耗时间)    FGC(老年代垃圾回收次数)    FGCT(老年代垃圾回收消耗时间)     GCT(垃圾回收消耗总时间)   
  	0.00 		 	   77.09  			51.17 		 	16.04  			98.09 		 96.46  		   27   		 0.383    		 2  		  		0.299 		  	 0.682
------------------------------------------------------------------------------------------------------------------------
e). -gccause 与 -gcutil 功能一样,但是会额外输出导致上一次GC产生的原因
eg: jstat -gccause 19830
------------------------------------------------------------------------------------------------------------------------
f). -gcnew 监视新生代GC状况
eg: jstat -gcnew 19830
 S0C(第一个幸存区的大小)    S1C(第二个幸存区的大小)    S0U(第一个幸存区的使用大小)    S1U(第二个幸存区的使用大小)   TT(对象在新生代存活的次数) MTT(对象在新生代存活的最大次数)  DSS(期望的幸存区大小)      EC(伊甸园区的大小)       EU(伊甸园区的使用大小)     YGC(年轻代垃圾回收次数)     YGCT(年轻代垃圾回收消耗时间)  
	8192.0 			1536.0    			0.0 			1184.2 			13  			15 			8192.0 				122880.0 		 67585.5 		    27   		 0.383
------------------------------------------------------------------------------------------------------------------------
g). -gcnewcapacity 监视内容与 -gcnew 基本相同,输出主要关注使用到的最大、最小空间
eg: jstat -gcnewcapacity 19830
------------------------------------------------------------------------------------------------------------------------
h). -gcold 监视老年代GC情况
eg: jstat -gcold 19830
   MC(方法区大小)      MU(方法区使用大小)      CCSC(压缩类空间大小)     CCSU(压缩类空间使用大小)       OC(老年代大小)          OU(老年代使用大小)       YGC(年轻代垃圾回收次数)    FGC(老年代垃圾回收次数)    FGCT(老年代垃圾回收消耗时间)     GCT(垃圾回收消耗总时间)   
    47656.0		  46746.4  		 5928.0   		5718.4   		 175104.0   		  28093.7  		   27    		 2   		 	0.299 		  	 0.682
------------------------------------------------------------------------------------------------------------------------
i). -compiler 输出JIT编译器编译过的方法、耗时等信息
eg： jstat -compiler 19830
Compiled(编译数量) Failed(失败数量) Invalid(不可用数量)   Time(时间)   FailedType(失败类型) FailedMethod(失败的方法)
    6788     	 	1       	0  		 53.83         	 1 	org/springframework/core/annotation/AnnotationUtils 		findAnnotation

```
3. <code>jinfo：Java配置信息工具</code>
```java
实时的查看和调整虚拟机各项参数
eg: jinfo [options] [pid]
Usage:
    jinfo [option] <pid>
        (to connect to running process)
    jinfo [option] <executable <core>
        (to connect to a core file)
    jinfo [option] [server_id@]<remote server IP or hostname>
        (to connect to remote debug server)

where <option> is one of:
    -flag <name>         to print the value of the named VM flag
    -flag [+|-]<name>    to enable or disable the named VM flag
    -flag <name>=<value> to set the named VM flag to the given value
    -flags               to print VM flags
    -sysprops            to print Java system properties
    <no option>          to print both of the above
    -h | -help           to print this help message

```
4. <code>jmap:Java内存映像工具</code>
```java
Usage:
    jmap [option] <pid>
        (to connect to running process)
    jmap [option] <executable <core>
        (to connect to a core file)
    jmap [option] [server_id@]<remote server IP or hostname>
        (to connect to remote debug server)

where <option> is one of:
    <none>               to print same info as Solaris pmap
    -heap                to print java heap summary
    -histo[:live]        to print histogram of java object heap; if the "live"
                         suboption is specified, only count live objects
    -clstats             to print class loader statistics
    -finalizerinfo       to print information on objects awaiting finalization
    -dump:<dump-options> to dump java heap in hprof binary format
                         dump-options:
                           live         dump only live objects; if not specified,
                                        all objects in the heap are dumped.
                           format=b     binary format
                           file=<file>  dump heap to <file>
                         Example: jmap -dump:live,format=b,file=heap.bin <pid>
    -F                   force. Use with -dump:<dump-options> <pid> or -histo
                         to force a heap dump or histogram when <pid> does not
                         respond. The "live" suboption is not supported
                         in this mode.
    -h | -help           to print this help message
    -J<flag>             to pass <flag> directly to the runtime system
```
```java
eg: jmap -dump:format=b,file=/root/dump.bin 19830
Dumping heap to /root/dump.bin ...
Heap dump file created
```
5. <code>jhat:虚拟机堆转储快照分析工具</code>
```java
jhat dump.bin
Reading from dump.bin...
Dump file created Sun Jul 29 19:56:51 CST 2018
Snapshot read, resolving...
Resolving 592589 objects...
Chasing references, expect 118 dots......................................................................................................................
Eliminating duplicate references......................................................................................................................
Snapshot resolved.
Started HTTP server on port 7000
Server is ready.
通常使用VisualVM去分析jmap dump下来的bin文件
```
6. <code>jstack:Java堆栈跟踪工具</code>
```
jstack 用于生成虚拟机当前时刻的线程快照
Usage:
    jstack [-l] <pid>
        (to connect to running process)
    jstack -F [-m] [-l] <pid>
        (to connect to a hung process)
    jstack [-m] [-l] <executable> <core>
        (to connect to a core file)
    jstack [-m] [-l] [server_id@]<remote server IP or hostname>
        (to connect to a remote debug server)

Options:
    -F  to force a thread dump. Use when jstack <pid> does not respond (process is hung)
    -m  to print both java and native frames (mixed mode)
    -l  long listing. Prints additional information about locks
    -h or -help to print this help message

```