# 来源
  ```
  https://www.cnblogs.com/k4nz/p/14350976.html
  ```
# 问题描述
当我们使用 Docker Compose 启动应用程序之后，如果主机发生重启，应用容器无法自动启动。

我们可以使用 Docker Compose 的重启策略，但是也存在一些问题。

该笔记将记录：1）自动启动服务的解决方案、2）及其常见问题和解决方法

## 方法一、通过 Docker Restart Policy 方法
在 Docker 中，支持 --restart 选项，来控制容器自动启动。在 Docker Compose 中，应该使用 restart 属性：
```
version: "3.6"
services:

  frontend:
    image: frontend:v1.0.0    
    depends_on:
      - backend
    restart: always

  backend:
    image: backend:v1.0.0
    restart: always
```

注意事项：
1）Docker 并不知道这些服务的依赖关系及启动顺序，需要我们精心编排 docker-compose.yaml 文件；
2）Docker Compose 不支持 deploy:restart_policy 属性，该属性只能用于 a swarm with docker stack deploy 环境；

## 方法二、通过进程管理服务（推荐）
该方法本质上还是在执行 docker-compose 命令。

使用 systemd 管理
如下示例，可以根据需要进行设置：
```
# cat /etc/systemd/system/docker-compose-app.service

[Unit]
Description=Docker Compose Application Service
Requires=docker.service
After=docker.service

[Service]
Type=oneshot
RemainAfterExit=yes
WorkingDirectory=/srv/docker/app/
ExecStart=/usr/local/bin/docker-compose up -d
ExecStop=/usr/local/bin/docker-compose down
TimeoutStartSec=0

[Install]
WantedBy=multi-user.target
```
