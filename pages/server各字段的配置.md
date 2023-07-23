# server各字段的配置
- # listen
- 监听可以配置成`IP`或`端口`或`IP+端口`
- ```bash
  listen 127.0.0.1:8000;
  listen 127.0.0.1;（ 端口不写,默认80 ）
  listen 8000;listen *:8000;
  listen localhost:8000;
  ```
- # server_name
- server_name主要用于区分，可以随便起。
- 也可以使用变量 `$hostname` 配置成主机名。
- 或者配置成域名： `example.org` `www.example.org` `*.example.org`
- 如果多个server的端口重复，那么根据`域名`或者`主机名`去匹配 server_name 进行选择。
- > 虚拟主机server通过listen和server_name进行区分，如果有多个server配置，listen + server_name 不能重复。
- >当有请求到达时也是 `listen + server_name` 确定对应哪个server。
- # localtion
- 确定一个server下不同的请求的URI对应配置查找规则
- 示例：
- ```bash
  location / {
    root   /usr/share/nginx/html;
    index  index.html index.htm;
  }
  ```
- **location可以使用修饰符或者正则表达式**，优先级依次从高到低为：
	- `=` 等于，严格匹配 ，匹配优先级最高。
	  `^~` 表示普通字符匹配。使用前缀匹配。如果匹配成功，则不再匹配其它 location。优先级第二高。
	- `~` 正则匹配，区分大小写
	- `~*` 正则匹配，不区分大小写
	- 没有修饰符，前缀匹配
	- `@`，命名location，可以用作内部重定向
- 借助此项功能可实现代理目标的分离：
- ```bash
  server{
  location / {
    proxy_pass http://localhost:8080/;
  }
  location = /html/ie.html {
    root  /home/www/static;
  }
  location ^~ /fonts/ {
    root  /home/www/static;
  }
  location ~ \.(css|js|png|jpg|gif|ico) {
    root /home/www/static;
  }
  ```
- `@name`  **的用法：**
- `@`前缀可以用来定义一个命名的location,该location不处理正常的外部请求,一般用来供内部重定向使用。它们不能嵌套,也不能包含嵌套的location。
- 例如:
  ```other
  location /try {  
  try_files $uri $uri/ @name;
  }
  
  location /error {  
  error_page 404 = @name;  
  return 404;
  }
  
  location @name {  
  return 200 "@name";
  }
  ```
  
  这时访问`/try`或者`/error`都会返回"@name"
  
  **root与路径**
  
  localtion匹配后，请求实际转发到的路径为root+路径名称，示例：
  
  ```other
  # /1.jpg 会被转发到/nginx/html/1.jpg
  location / {
    root /nginx/html;
  }
  # /css/1.jpg -> /nginx/html/css/1.jpg
  location /css {  
    root /nginx/html;  
  }
  ```