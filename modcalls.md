## modcalls.d
`modcalls.d`位于[DTraceToolkit](http://www.brendangregg.com/dtracetoolkit.html)的`Kernel`文件夹下，其功能是统计不同模块调用`kernel`函数的次数。脚本代码就一行（略去版权信息）：

    fbt:::entry { @calls[probemod] = count(); }

脚本代码分析如下：  
监听`fbt Provider`的 `entry probe`（`fbt Provider`几乎提供了对所有`kernel`函数的`entry`和`exit`的`trace`）。定义`calls`这个`aggregation`，用`probemod`做`key`（`probemod`记录了当前`probe`的模块名）。当`probe`触发后，把`calls`里相应模块的统计次数加`1`。

执行脚本，输出如下：

    Nans-MacBook-Pro:Kernel root# ./modcalls.d 
    dtrace: script './modcalls.d' matched 47940 probes
    ^C
    
      com.apple.driver.AppleAPI                                 5
      com.apple.driver.AppleOSXWatchdog                         7
      com.apple.driver.AppleUSBComposite                        9
      com.apple.driver.AppleUSBTCButtons                        17
    ......