# vscode终端相关

- 不可以 npm run dev

```powershell
Set-ExecutionPolicy RemoteSigned -Force
```

- 不可以 npm install

打开nodejs源文件，属性-安全-编辑，给当前用户**完全控制**的权限

# cmd

在windows上打包go文件 管理员身份运行

```powershell
# 编译为 Linux 64 位可执行文件
set GOOS=linux
set GOARCH=amd64
go build -o main main.go
```

# Linux

## 终端

数据库导出

```powershell
mysqldump -u svb -p -h127.0.0.1  base >  /www/server/go_project/cspona/base.sql

```



## Nginx

```nginx
server {
    listen 80;
    listen [::]:80;
    listen 443 ssl ;
    listen [::]:443 ssl ;
    http2 on;
    server_name cspona.top www.cspona.top;
    
    # 强制HTTPS重定向（推荐）
    if ($scheme = http) {
        return 301 https://$server_name$request_uri;
    }

    # SSL配置
    ssl_certificate /www/server/panel/vhost/cert/cspona.top/fullchain.pem;
    ssl_certificate_key /www/server/panel/vhost/cert/cspona.top/privkey.pem;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;

    # 通用API代理
    location /api/ {
        proxy_pass http://127.0.0.1:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-Proto $scheme;
        add_header X-Config-Mode "generic" always;
    }

    # 前端路由
    location / {
        root /www/server/go_project/cspona/dist;
        try_files $uri $uri/ /index.html;
        index index.html;
    }
    
    # 后端图片代理
    location /Pictures {
        alias /www/server/go_project/cspona/dist/Pictures;
        # 其他配置...
    }

    # 日志配置
    access_log /var/log/nginx/cspona.access.log;
    error_log /var/log/nginx/cspona.error.log;
}
```



重新加载nginx

```powershell
# 测试配置
sudo nginx -t

# 重载配置
sudo systemctl reload nginx
service nginx restart

# 测试并重新加载
nginx -t && nginx -s reload

# 查看所有文件对应后缀
mime.types

# 查看所有生效配置
sudo nginx -T | grep -A 30 "server_name cspona.top"

# 启动main（go）程序
cd /www/xxxxx
sudo -u www ./main

```



**尽管文件存在且权限正确，Nginx 仍然返回 404**。就是典型的配置路径映射错误。



端口被占用

``` powershell
# 查找占用3000端口的进程
sudo lsof -i :3000

# 强制终止占用进程（假设PID是12345）
sudo kill -9 12345
```

