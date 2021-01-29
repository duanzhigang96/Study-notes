# Nginx

- **反向代理：代理服务器**

![image.png](https://cdn.nlark.com/yuque/0/2021/png/12549023/1611905497355-c74475a7-a63b-4f6e-8834-1e12f1e50a12.png#align=left&display=inline&height=270&margin=%5Bobject%20Object%5D&name=image.png&originHeight=270&originWidth=438&size=89510&status=done&style=none&width=438)

- **负载均衡**

nginx 提供负载均衡策略有2种：内置策略和拓展策略<br />内置策略：

   1. 轮询
   1. 加权轮询(根据权重的比例动态分配请求的服务器)
   1. ip hash（可以解决session不共享的问题，通过iphash 的方式固定的ip 只能访问同一台服务器这样就不会出现session不共享的问题。（建议使用）通过redies实现session 的共享）

拓展策略：.......

- **正向代理：代理客户端![image.png](https://cdn.nlark.com/yuque/0/2021/png/12549023/1611904526198-8e621820-2614-4d81-86dc-5e922b90e139.png#align=left&display=inline&height=308&margin=%5Bobject%20Object%5D&name=image.png&originHeight=308&originWidth=481&size=91396&status=done&style=none&width=481)**
- **动静分离**

前端静态页面通过nginx返回。<br />

<a name="F17b3"></a>
### 安装：
Windows 安装<br />Linux 安装<br />

<a name="NBgZ4"></a>
### 介绍nginx.conf 配置文件 加负载均衡配置
```basic
#全局配置
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


# 最大连接数 监听的事件
events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

		#负载均衡配置
		upstream test{
				#服务器资源配置
				server 127.0.0.1:8080 weight = 1; 设置权重
				server 127.0.0.1:8081 weight = 2; 设置权重
		}

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}

```
```basic
server {
    listen       80;
    listen  [::]:80;
    server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
				proxy_pass  http://test; #负载均衡代理
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

		#代理
    location /blog/ {
       proxy_pass  http://172.17.59.99:8000;
    }
    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}
```
<a name="JjWBK"></a>
### nginx常用命令
帮助命令：nginx -h<br />启动Nginx服务器 ：sudo nginx<br />查看进程： ps aux | grep nginx<br />配置文件路径：/usr/local/nginx/conf/nginx.conf<br />检查配置文件：sudo nginx -t<br />指定启动配置文件：sudo nginx -c /usr/local/nginx/conf/nginx.conf<br />暴力停止服务：sudo nginx -s stop<br />优雅停止服务：sudo nginx -s quit<br />重新加载配置文件：sudo nginx -s reload
