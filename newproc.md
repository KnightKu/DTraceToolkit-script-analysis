## newproc.d
`newproc.d`位于[DTraceToolkit](http://www.brendangregg.com/dtracetoolkit.html)的`Proc`文件夹下，其功能是打印新执行的进程名字。脚本代码就一行（略去版权信息）：

    proc:::exec-success { trace(curpsinfo->pr_psargs); }

脚本代码分析如下：
监听`exec-success` `probe`（只有`exec`系统调用执行成功才会触发这个`probe`），其中`curpsinfo`是`psinfo_t`类型变量，`pr_psargs`成员记录了命令行参数。当`probe`触发后，打印整个新进程的命令行参数。

执行脚本，输出如下：

    bash-3.2# ./newproc.d
    dtrace: script './newproc.d' matched 1 probe
    CPU     ID                    FUNCTION:NAME
      0   3177         exec_common:exec-success   sh -c { iostat -x 5 2|grep -v '^$' |grep -vi 'dev'; } 2>&1
      0   3177         exec_common:exec-success   iostat -x 5 2
      4   3177         exec_common:exec-success   grep -vi dev
      5   3177         exec_common:exec-success   grep -v ^$
参考资料：[proc Provider](http://dtrace.org/guide/chapter25.html)。