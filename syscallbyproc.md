## syscallbyproc.d
`syscallbyproc.d`位于[DTraceToolkit](http://www.brendangregg.com/dtracetoolkit.html)的`Proc`文件夹下，其功能是统计进程使用系统调用的次数。脚本代码就一行（略去版权信息）：

    syscall:::entry { @num[execname] = count(); }

脚本代码分析如下：  
监听`syscall Provider`的 `entry probe`，`exec name`内置变量记录了进程名，作为`num`这个`aggregation`的`key`。当`probe`触发后，把`num`里相应进程的统计次数加`1`。

执行脚本，输出如下：

    Nans-MacBook-Pro:Proc root# ./syscallbyproc.d 
    dtrace: script './syscallbyproc.d' matched 490 probes
    ^C
    
      loginwindow                                     1
      CVMServer                                       2
      coreaudiod                                      2
      CalendarAgent                                   3
      SocialPushAgent                                 4
      
    ......