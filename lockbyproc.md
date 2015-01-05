## lockbyproc.d
`lockbyproc.d`位于[DTraceToolkit](http://www.brendangregg.com/dtracetoolkit.html)的`Locks`文件夹下，其功能是统计进程在获得“`adaptive mutex`”等待时间的总和。脚本代码就一行（略去版权信息）：

	lockstat:::adaptive-block { @time[execname] = sum(arg1); }

脚本代码分析如下：  
定义一个以可执行程序名字（`execname`）为`key`的`aggregation`（`time`）。监听所有`lockstat Provider`的`adaptive-block` `probe`，其中`arg1`参数包含的是等待时间（单位是`ns`）。当`probe`触发时，调用`sum`函数。最后脚本退出时，产生进程等待时间总和的统计。

执行脚本，输出如下：

	bash-3.2# ./lockbyproc.d
	dtrace: script './lockbyproc.d' matched 1 probe
	^C
	
	  sched                                                         19270
	  java                                                         156943
可以看到，`java`程序等待获得`mutex`时间比较长。  
参考资料：  
（1）[lockstat Provider](http://dtrace.org/guide/chapter18.html)。
