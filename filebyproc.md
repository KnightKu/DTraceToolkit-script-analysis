## filebyproc.d
`filebyproc.d`位于[DTraceToolkit](http://www.brendangregg.com/dtracetoolkit.html)的`Proc`文件夹下，其功能是打印进程打开的文件名。脚本代码就一行（略去版权信息）：

	syscall::open*:entry { printf("%s %s", execname, copyinstr(arg0)); }
脚本代码分析如下：
监听所有`open`相关函数的`entry` `probe`，如果触发，就打印进程名（`execname`）和文件名（`arg0`）。

执行脚本，输出如下：

	root# ./filebyproc.d 
	dtrace: script './filebyproc.d' matched 7 probes
	CPU     ID                    FUNCTION:NAME
	  2    151                       open:entry Dock /System/Library/CoreServices/Dock.app/Contents/Resources/pileArrow@2x.png
	  2    151                       open:entry Dock /System/Library/CoreServices/Dock.app/Contents/Resources/pileArrow@2x.png
	  2    151                       open:entry mds_stores .
	  2    151                       open:entry mds_stores .
	  0    937              open_nocancel:entry com.apple.WebKi /Users/nanxiao/Library/Caches/com.apple.Safari/cache_set.dmp