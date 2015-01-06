## creatbyproc.d
`creatbyproc.d`位于[DTraceToolkit](http://www.brendangregg.com/dtracetoolkit.html)的`Proc`文件夹下，其功能是打印调用`creat`系统调用的进程名和创建的文件名。脚本代码就一行（略去版权信息）：

    syscall::creat*:entry { printf("%s %s", execname, copyinstr(arg0)); }


脚本代码分析如下：  
监听`creat`（或者`creat64`）系统调用的 `entry probe`，其中`arg0`参数记录了要创建的文件名。当`probe`触发后，打印进程名和创建的文件名。

执行脚本，输出如下：

    bash-3.2# ./creatbyproc.d
	dtrace: script './creatbyproc.d' matched 2 probes
	CPU     ID                    FUNCTION:NAME
	  0  57233                    creat64:entry sh /tmp/mpsYaiuL
	  4  57233                    creat64:entry sh /dev/null
	  0  57233                    creat64:entry sh /dev/null
	  5  57233                    creat64:entry sh /dev/null

