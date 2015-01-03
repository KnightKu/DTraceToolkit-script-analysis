## syscallbypid.d
`syscallbypid.d`位于[DTraceToolkit](http://www.brendangregg.com/dtracetoolkit.html)的`Proc`文件夹下，其功能是根据进程的`Process ID`，统计`syscall Provider`的 `probe`的信息。脚本代码如下（略去版权信息）：

    #pragma D option quiet

    dtrace:::BEGIN
    {
    	printf("Tracing... Hit Ctrl-C to end.\n");
    }
    
    syscall:::entry
    {
    	@num[pid, execname, probefunc] = count();
    }
    
    dtrace:::END
    {
    	printf("%6s %-24s %-24s %8s\n", "PID", "CMD", "SYSCALL", "COUNT");
    	printa("%6d %-24s %-24s %@8d\n", @num);
    }

脚本代码分析如下：  
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
 
    syscall:::entry
    {
    	@num[pid, execname, probefunc] = count();
    }
在每个`syscall Provider`的 `entry probe`里做统计：`count()`函数用来统计发生次数。统计结果输出在`num`这个`aggregation`里：进程号，执行程序的名字，函数名做`key`，统计值做`value`。  
（4）  

    dtrace:::END
    {
    	printf("%6s %-24s %-24s %8s\n", "PID", "CMD", "SYSCALL", "COUNT");
    	printa("%6d %-24s %-24s %@8d\n", @num);
    }
脚本退出时，打印`num`内容。需要注意的是，打印`aggregation`值时，要用`%@`这种格式。

执行脚本，输出如下：

    Nans-MacBook-Pro:Proc root# ./syscallbypid.d 
    Tracing... Hit Ctrl-C to end.
    ^C
       PID CMD                      SYSCALL                     COUNT
        17 syslogd                  madvise                         1
        20 fseventsd                gettimeofday                    1
        20 fseventsd                psynch_cvsignal                 1
        25 configd                  __disable_threadsignal          1
        25 configd                  bsdthread_terminate             1
        25 configd                  thread_selfid                   1
     