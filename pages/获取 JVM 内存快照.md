## 自动输出 dump 信息
	- 在 JVM 启动时，添加 `-XX:+HeapDumpOnOutOfMemoryError` 参数，开启“**当堆内存空间溢出时输出堆的内存快照**”功能，而 `-XX:HeapDumpPath` 参数则指定生成的堆转储存放位置。
	- `java -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/usr/local/log/ -jar test-0.0.1-SNAPSHOT.jar `
- ## 手动导出 dump 文件
	- ## 导出堆转储文件 heap dump
		- 堆转储文件 `heap dump` 可以了解当时 JVM 中堆的使用情况，是某个时间点，Java 进程的内存快照。
		- 包含了当时内存中还没有被 full GC 回收的对象和类信息，比如各个类实例的数量及各个实例所占用的空间大小。**在内存溢出(OOM)时可以使用该文件进行分析和故障定位。**
		- 获取方式，先用 `jps` 工具获取到 `PID`，再用 `jmap` 获取 `head dump`：
			- ```shell
			  jps
			  jmap -dump:format=b,file=filename <pid>  (获取heap dump) 
			  jmap -dump:live,format=b,file=filename <pid>（只dump堆中存活的对象）
			  ```
	- ### 导出线程堆栈文件 thread dump
		- 线程堆栈文件 `thread dump` 可以了解当时 JVM 中所有线程的运行情况，**比如线程的状态和当前正在运行的代码行**。可以用于定位分析线程出现长时间停顿的原因。
		- 获取方式，先用 `jps` 工具获取到 `PID`，再用 `jstack` 获取 `thread dump`：
			- ```shell
			  jps
			  jstack [-l] <pid> > filename
			  ```
- ## heap dump 分析
	- jhat
	  logseq.order-list-type:: number
	- MAT
	  logseq.order-list-type:: number
	- VisualVM
	  logseq.order-list-type:: number