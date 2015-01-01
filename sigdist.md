## sigdist.d
`sigdist.d`位于[DTraceToolkit](http://www.brendangregg.com/dtracetoolkit.html)的`Proc`文件夹下，其功能是统计一段时间内进程之间发送信号的数量和类型。脚本代码如下（略去版权信息）：

	#pragma D option quiet
	
	dtrace:::BEGIN
	{
	    printf("Tracing... Hit Ctrl-C to end.\n");
	}
	
	proc:::signal-send
	{
	    @Count[execname, stringof(args[1]->pr_fname), args[2]] = count();
	}
	
	dtrace:::END
	{
	    printf("%16s %16s %6s %6s\n", "SENDER", "RECIPIENT", "SIG", "COUNT");
	    printa("%16s %16s %6d %6@d\n", @Count);
	}
下面就对脚本做一个分析：  
（1）

	#pragma D option quiet
这一行是让`DTrace`仅仅输出脚本程序里追踪的数据（像用`printf`、`trace`等函数的输出），而不会输出像列名字、“`CPU ID`”、“`Probe ID`”等等信息。

（2）

	dtrace:::BEGIN
	{
	    printf("Tracing... Hit Ctrl-C to end.\n");
	}
脚本开始运行时，提示输入“`Ctrl-C`”终止程序运行。

（3）

	proc:::signal-send
	{
	    @Count[execname, stringof(args[1]->pr_fname), args[2]] = count();
	}
`args[1]`参数类型是`psinfo_t`，描述了接收信号的进程信息，其中`pr_fname`成员记录的是接收信号的进程名。`args[2]`参数类型是`int`，记录的是信号值。

`count()`函数用来统计发生次数。统计结果输出在`Count`这个`aggregation`里：发送进程名，接收进程名，信号值做为`key`，统计值做`value`。

（4）

	dtrace:::END
	{
	    printf("%16s %16s %6s %6s\n", "SENDER", "RECIPIENT", "SIG", "COUNT");
	    printa("%16s %16s %6d %6@d\n", @Count);
	}
脚本退出时，打印`Count`内容。需要注意的是，打印`aggregation`值时，要用`%@`这种格式。

最后执行脚本，输入“`Ctrl-C`”后，脚本程序退出，输出如下：

	bash-3.2# ./sigdist.d 
	Tracing... Hit Ctrl-C to end.
	^C
	      SENDER        RECIPIENT    SIG  COUNT
	         fmd              fmd     14      1
	        grep             init     18      1
	        init             init     18      1
	       sched           dtrace      2      1
	       sched           iostat     16      1
	        tail             init     18      1
	          wc             init     18      1
	       sched             init     18      3
参考资料：  
（1）[proc Provider](http://dtrace.org/guide/chapter25.html)。