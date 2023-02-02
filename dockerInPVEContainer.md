# 在pve内创建lxc容器并安装docker,启动容器

> ref: https://www.youtube.com/shorts/uvecfr4na-8
> 参考这个油管短视频以及评论，lxc容器有两种选项：
> 1. 需要开启privileged container，但出于安全考虑不推荐；
> 
> 2. 推荐的更安全的方式： 使用unprivileged container，需要开启keyctl和nesting,后来发现网友说keyctl不需要，只需要FUSE和nestring;
> 
> 本笔记就记录了使用方式2：使用unprivileged container，开启FUSE和nesting选项,经过测试成功。

# 0. 在pve内创建lxc容器,并安装docker.
## 0.0 在pve中下载ct template,比如ubuntu22.04-standard_ubuntu22.04-1.amd64.tar.zst
## 0.1 在pve中创建ct容器
      比如 4 CPU, 8GB RAM，32GB harddisk, DHCP, unprivileged container,
      模板：ubuntu22.04-standard_ubuntu22.04-1.amd64.tar.zst
      设置root密码
## 0.2 修改容器的options
      勾选FUSE和nested两个选项
## 0.3 启动容器
## 0.4 用root登录进容器
## 0.5 更新系统:

```
apt update && apt upgrade -y

```
## 0.6 安装docker: `apt install docker.io`
## 0.7 新建用户xxx: `adduser xxx`
## 0.8 赋予xxx用户使用docker的权限：
```
usermod -aG docker xxx

```

# 1. 将pve外的docker容器转移到pve内的容器。
## 1.0 在pve外的linux上，将docker镜像打包
   ```
   docker save imagename:tag > imagename-tag.tar

   ```

## 1.1 从pve外的linux向pve内的容器内复制文件：
  ```
   rsync -avzP -e 'ssh -p 22'  imagename-tag.tar  xxx@192.168.yyy.zzz:/home/xxx

  ```

## 1.2 从pve外的linux登录进pve内的容器
   ```
    ssh xxx@192.168.yyy.zzz

   ```

## 1.3 在pve内的容器内执行
   ```
    docker load -i imagename-tag.tar
   
   ```

## 1.4 在pve内的容器内执行
   ```
    docker run -d -p 4500:4500 --name containername imagename:tag
   
   ```
