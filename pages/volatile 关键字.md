# 作用
- 对变量进行修饰，是对变量的操作：
	- 防止重排序（即有序性）
	  logseq.order-list-type:: number
	- 实现可见性
	  logseq.order-list-type:: number
- ((64de305f-b42b-4b6b-bb84-d9af9cc790ca))
- ## 可见性实现
- 修改 volatile 变量时会强制将修改后的值刷新的主内存中。
  logseq.order-list-type:: number
- 修改volatile变量后会导致其他线程工作内存中对应的变量值失效。因此，再读取该变量值的时候就需要重新从读取主内存中的值。
  logseq.order-list-type:: number
- > ((64d20472-0d5b-4205-81f9-241362fad076))
- ## 有序性实现
- JVM底层是通过一个叫做“内存屏障”的东西来完成。内存屏障，也叫做内存栅栏，是一组处理器指令，用于实现对内存操作的顺序限制。
- > ((64d20485-230f-4153-bba9-2a3b5f28ccda))