# Docker简介

Docker 是一个开源的应用容器引擎，基于 [Go 语言](https://www.runoob.com/go/go-tutorial.html) 并遵从 Apache2.0 协议开源。

Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。

容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）,更重要的是容器性能开销极低。

# Docker 架构

**镜像：（Image）**镜像就像是java语言中的类的概念，是一个个模板，是一个个安装程序。

**容器：（Container）**容器就是我们实例化过后的类，也就是安装好的程序。

**仓库：（Repository）**：仓库可看着一个代码控制中心，用来保存镜像。

# Docker 安装

百度

# Docker命令

## 获取并下载镜像

```
docker pull 镜像名称：版本号
```

当运行容器时，使用的镜像如果在本地中不存在，docker 就会自动从 docker 镜像仓库中下载，默认是从 Docker Hub 公共镜像源下载。

## 使用镜像运行容器(基本操作)

`run` 启动镜像，生成容器

```
docker run -it ubuntu /bin/bash
```

- **-i**: 交互式操作。
- **-t**: 终端。
- **ubuntu**: ubuntu 镜像。
- **/bin/bash**：放在镜像名后的是命令，这里我们希望有个交互式 Shell，因此用的是 /bin/bash。这是-it必要！

这时候我们会进入该系统的终端命令界面，那有时候我们让一个镜像以后台程序的方式运行，我们则需要参数

`-d` 以后台方式进入容器

```
docker run -d --name ubuntu-test ubuntu 
```

`--name`是给这个容器取一个指定的名字，而不是系统自动生成的！

`-v`的意思是将某个路径挂载到容器的某一个路径，形成替换！

停止容器：`docker stop <容器id/name>`

启动容器：`docker start <容器id/name>`

重启容器：`docker restart <容器id/name>`

进入容器：

1. **docker attach**  退出会导致容器停止

2. **docker exec** 退出不会导致容器停止

   ```
   docker exec -it redis bash/sh alphie版本为sh
   ```

`-p`设置映射端口，由于docker里的应用都是运行在一个个容器中，所以我们需要将一些web应用的端口映射到本机的端口，比如redis

```
docker exec -it p 6379(本机):6379(容器) redis bash
```

**查看命令：**

1. 查看所有镜像：`docker images`
2. 查看所有在运行容器：`docker ps /-a(所有，包括未运行)`
3. 查看本地和容器映射端口：`docker port <id/name>`
4. 删除容器：`docker rm <id/name>`
5. 删除镜像：`docker rmi <name>` **(删除镜像之前必须先删除该镜像下的所有容器)**
6. 查看容器内部输出日志：`docker logs <id/name>`
7. 查看容器内部运行进程：`docker top <id/name>`
8. 查看容器状态：`docker inspect <id/name>`

## 创建 修改 上传 镜像

命令：`docker build`

修改提交命令，我们想制造一个自己所更新的镜像，一般有两种方法，一种是提交自己所修改的容器，并提交修改成镜像文件。

```
docker commit -m "..." -a "..." 0b2616b0e5a8 ...
```

**其中，-m 来指定提交的说明信息，跟我们使用的版本控制工具一样；-a 可以指定更新的用户信息；之后是用来创建镜像的容器的 ID；最后指定目标镜像的仓库名和 tag 信息。创建成功后会返回这个镜像的 ID 信息。**

注意：必须在关闭这个容器的前提下，制作镜像

第二种就是利用Dockerfile制作镜像

[Dockerfile](https://www.runoob.com/docker/docker-dockerfile.html)