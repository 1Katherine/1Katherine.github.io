---
title: nginx部署vue项目
date: 2022-08-16 23:26:27
tags:
- 部署
- 项目
- nginx
categories:
- 项目
---













Nginx 部署前端 vue 项目 + 实现反向代理

/etc/nginx/nginx.conf

```
server {
      listen 9061;
      server_name 59.110.70.138;

      # 前端目录
      location / {
              root /root/Gaokao_Data_Analysis_System/dist;
              #index  index.html index.htm;
              index index.html;
              try_files  $uri $uri/ /index.html;
      }

		# 反向代理到后端工程
        location /api {  
            proxy_set_header Host $http_host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header REMOTE-HOST $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_pass http://127.0.0.1:8080;  # 代理到同服务器下的 8080 端口
            rewrite "^/api/(.*)$" /$1 break;  # /api/* 重写为 /*
        }
}

```

修改完后，重新加载nginx配置文件  nginx -s reload





重新加载报错时，删除所有 ngnix 服务，重新启动

```

root@iZ2zecl0gtu0ylfl829uudZ:~# ps aux | grep nginx
root       17120  0.0  0.0  55964  1660 ?        Ss   Jul28   0:00 nginx: master process nginx -c /etc/nginx/nginx.conf
root       17122  0.0  0.0  56204  5292 ?        S    Jul28   0:00 nginx: worker process
root       17123  0.0  0.0  56204  6160 ?        S    Jul28   0:00 nginx: worker process
root      104079  0.0  0.0   9032   660 pts/6    S+   23:11   0:00 grep --color=auto nginx


1. 删除
kill -9 17120 17122 17123

2. 重新启动
nginx -c /etc/nginx/nginx.conf

3. 检查是否启动
ps aux | grep nginx
```





查看 nginx 报错信息

 cat /var/log/nginx/error.log



