## kill.d
`kill.d`位于[DTraceToolkit](http://www.brendangregg.com/dtracetoolkit.html)的`Proc`文件夹下，其功能是检查进程之间发送的信号值，以及信号发送是否成功。脚本代码如下（略去版权信息）：

	dtrace:::BEGIN
	{
	    /* Print header */
	    printf("%5s %12s %5s %-6s %s\n",
	        "FROM", "COMMAND", "SIG", "TO", "RESULT");
	}
	
	syscall::kill:entry
	{
	    /* Record target PID and signal */
	    self->target = arg0;
	    self->signal = arg1;
	}
	
	syscall::kill:return
	{
	    /* Print source, target, and result */
	    printf("%5d %12s %5d %-6d %d\n",
	        pid, execname, self->signal, self->target, (int)arg0);
	
	    /* Cleanup memory */
	    self->target = 0;
	    self->signal = 0;
	}
下面就对脚本做一个分析：  
（1）

	dtrace:::BEGIN
	{
	    /* Print header */
	    printf("%5s %12s %5s %-6s %s\n",
	        "FROM", "COMMAND", "SIG", "TO", "RESULT");
	}
脚本开始运行时，打印每一列的名字：`FROM`是发送信号的进程`ID`，`COMMAND`是发送信号的进程名字，`SIG`是发送的信号值，`TO`是接收信号的进程`ID`，`RESULT`是发送信号的结果（`－1`表示失败）。
（2）

	syscall::kill:entry
	{
	    /* Record target PID and signal */
	    self->target = arg0;
	    self->signal = arg1;
	}
当发生`kill`系统调用时，用`thread local`变量记录接收信号的进程`ID`（`arg0`）和信号值（`arg1`）。
（3）

	syscall::kill:return
	{
	    /* Print source, target, and result */
	    printf("%5d %12s %5d %-6d %d\n",
	        pid, execname, self->signal, self->target, (int)arg0);
	
	    /* Cleanup memory */
	    self->target = 0;
	    self->signal = 0;
	}
`kill`系统调用返回时，打印相应的值，同时释放`thread local`变量，防止内存泄漏。

最后执行脚本，输入“`Ctrl-C`”后，脚本程序退出，输出如下：

	FROM      COMMAND   SIG TO     RESULT
	  478         bash     9 567    -1
	    1      launchd    30 16     0
	    1      launchd    30 16     0
	    1      launchd    30 16     0
	    1      launchd    30 16     0