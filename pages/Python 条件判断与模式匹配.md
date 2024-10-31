## 条件判断
	- 形如：
		- ```
		  if <条件判断1>:
		      <执行1>
		  elif <条件判断2>:
		      <执行2>
		  elif <条件判断3>:
		      <执行3>
		  else:
		      <执行4>
		  ```
	- 简写：
		- ```
		  if x:
		      print('True')
		  ```
		- 只要`x`是非零数值、非空字符串、非空list等，就判断为`True`，否则为`False`。
- ## 模式匹配
	- 代替条件判断，不需要 `break` 的 `switch` 语句。例如：
		- ```
		  score = 'B'
		  match score:
		      case 'A':
		          print('score is A.')
		      case 'B':
		          print('score is B.')
		      case 'C':
		          print('score is C.')
		      case _: # _表示匹配到其他任何情况
		          print('score is ???.')
		  ```
	- ### 复杂匹配
		- `match`语句除了可以匹配简单的单个值外，还可以**匹配多个值**、**匹配一定范围**，并且**把匹配后的值绑定到变量**：
			- ```
			  age = 15
			  match age:
			    case x if x < 10:
			        print(f'< 10 years old: {x}')
			    case 10:
			        print('10 years old.')
			    case 11 | 12 | 13 | 14 | 15 | 16 | 17 | 18:
			        print('11~18 years old.')
			    case 19:
			        print('19 years old.')
			    case _:
			        print('not sure.')
			  ```
			- 在上面这个示例中，第一个`case x if x < 10`表示当`age < 10`成立时匹配，且赋值给变量`x`，第二个`case 10`仅匹配单个值，第三个`case 11|12|...|18`能匹配多个值，用`|`分隔。
	- ### 匹配列表
		- `match`语句还可以匹配列表，功能非常强大。
		- 我们假设用户输入了一个命令，用`args = ['gcc', 'hello.c']`存储，下面的代码演示了如何用`match`匹配来解析这个列表：
		- ```
		  args = ['gcc', 'hello.c', 'world.c']
		  # args = ['clean']
		  # args = ['gcc']
		  
		  match args:
		      # 如果仅出现gcc，报错:
		      case ['gcc']:
		          print('gcc: missing source file(s).')
		      # 出现gcc，且至少指定了一个文件:
		      case ['gcc', file1, *files]:
		          print('gcc compile: ' + file1 + ', ' + ', '.join(files))
		      # 仅出现clean:
		      case ['clean']:
		          print('clean')
		      case _:
		          print('invalid command.')
		  ```
		- 第一个`case ['gcc']`表示列表仅有`'gcc'`一个字符串，没有指定文件名，报错；
		- 第二个`case ['gcc', file1, *files]`表示列表第一个字符串是`'gcc'`，第二个字符串绑定到变量`file1`，后面的任意个字符串绑定到`*files`（符号`*`的作用将在[函数的参数](https://liaoxuefeng.com/books/python/function/parameter/index.html)中讲解），它实际上表示至少指定一个文件；
		- 第三个`case ['clean']`表示列表仅有`'clean'`一个字符串；
		- 最后一个`case _`表示其他所有情况。