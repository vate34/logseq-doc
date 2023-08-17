- #计算机网络
# 常用命令

```other
#立即停止
nginx -s stop
#执行完当前请求再停止
nginx -s quit
#重新加载配置文件，相当于restart
nginx -s reload
#将日志写入一个新的文件
nginx -s reopen
#测试配置文件
nginx -t
```
# 配置文件示例

```other
http {
server { #虚拟主机
    listen       80; #监听端口
    server_name  localhost; #

    location / {
        root   /usr/share/nginx/html; #根目录
        index  index.html index.htm; #首页
    }
}
}
```
- ## [[server各字段的配置]]
- # 反向代理
- 配置文件示例：
- 以下配置可将8001端口转发到本地的8088端口
  
  ```other
  server {
  listen 8001;
  server_name localhost;
  location / {
    proxy_pass http://localhost:8088;
  }
  location /path1/ {
      proxy_pass http://localhost:8080;
  }
  location /path2/ {
      proxy_pass http://localhost:8080/zh-cn/;
  }
  }
  ```
- 如果`proxy_pass` 的地址只配置到端口，不包含`/`或其他路径，那么location将被追加到转发地址中，如上所示，访问 `http://localhost/path1/page.html` 将被代理到 `http://localhost:8080/path1/page.html`
- 如果`proxy-pass`的地址包括`/`或其他路径，那么`/path` 将会被替换，如上所示，访问 `http://localhost/some/path2/page.html` 将被代理到 `http://localhost:8080/zh-cn/page.html`。‎
- 由于使用反向代理之后，后端服务无法获取用户的真实IP，所以，一般反向代理都会设置以下header信息。
  
  ```other
  location /path/ {
    #nginx的主机地址
    proxy_set_header Host $http_host;
    #用户端真实的IP，即客户端IP
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_pass http://localhost:8088;
  }
  ```
- `$host`：nginx主机IP，例如192.168.56.105
- `$http_host`：nginx主机IP和端口，192.168.56.105:8001
- `$proxy_host`：localhost:8088，proxy_pass里配置的主机名和端口
- `$remote_addr`:用户的真实IP，即客户端IP。