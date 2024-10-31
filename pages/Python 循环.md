- Python 有两种循环方式，一种是for...in循环，依次把list或tuple中的每个元素迭代出来：
	- ```
	  names = ['Michael', 'Bob', 'Tracy']
	  for name in names:
	      print(name)
	  ```
- 另一种是while循环，只要条件满足，就不断循环，条件不满足时退出循环：
	- ```
	  sum = 0
	  n = 99
	  while n > 0:
	      sum = sum + n
	      n = n - 2
	  print(sum)
	  ```
- 另外还可以在循环中使用`break`提前退出循环；使用 `continue` 跳过当前循环，进入下次循环。