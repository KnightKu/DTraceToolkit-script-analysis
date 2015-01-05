## lockbydist.d
`lockbydist.d`位于[DTraceToolkit](http://www.brendangregg.com/dtracetoolkit.html)的`Locks`文件夹下，其功能是统计进程在获得“`adaptive mutex`”等待时间的分布。脚本代码就一行（略去版权信息）：

	lockstat:::adaptive-block { @time[execname] = quantize(arg1); }

脚本代码分析如下：  
定义一个以可执行程序名字（`execname`）为`key`的`aggregation`（`time`）。监听所有`lockstat Provider`的`adaptive-block` `probe`，其中`arg1`参数包含的是等待时间（单位是`ns`）。当`probe`触发时，调用`quantize`函数。最后脚本退出时，产生等待时间的分布图。

执行脚本，输出如下：

	bash-3.2# ./lockbydist.d
	dtrace: script './lockbydist.d' matched 1 probe
	^C
	  sched
	           value  ------------- Distribution ------------- count
	         2097152 |                                         0
	         4194304 |@@@                                      1
	         8388608 |                                         0
	        16777216 |@@@@@@@@                                 3
	        33554432 |                                         0
	        67108864 |@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@           12
	       134217728 |                                         0
可以看到`sched`程序等待`mutex`的时间介于[`67108864`，`134217728`)比较多。  
参考资料：
（1）[lockstat Provider](http://dtrace.org/guide/chapter18.html)。