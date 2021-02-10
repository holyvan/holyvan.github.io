---
layout: post
title:  "docker entrypoint"
categories: docker
tags: docker entrypoint run cmd
author: joshua
description: docker
---



## docker RUN CMD ENTRYPOINT 的区别



### Shell 和 Exec 格式

我们可用两种方式指定 RUN、CMD 和 ENTRYPOINT 要运行的命令：Shell 格式和 Exec 格式，二者在使用上有细微的区别

#### Shell 格式

```
<instruction> <command>
```

```dockerfile
RUN apt-get install python3
CMD echo "Hello world"
ENTRYPOINT echo "Hello world"
```


当指令执行时，shell 格式底层会调用 ==/bin/sh -c <command>==

Dockerfile

```dockerfile
ENV name Cloud Man
ENTRYPOINT echo "Hello, $name"
```


docker run <image>

```
Hello, Cloud Man
```



#### Exec 格式

```
<instruction> ["executable", "param1", "param2", ...]
```

```dockerfile
RUN ["apt-get", "install", "python3"]
CMD ["/bin/echo", "Hello world"]
ENTRYPOINT ["/bin/echo", "Hello world"]
```

当指令执行时，==直接调用 <command>，不会被 shell 解析==

Dockerfile

```dockerfile
ENV name Cloud Man
ENTRYPOINT ["/bin/echo", "Hello, $name"]
```

运行容器将输出

```
Hello, $name
```

环境变量“name”没有被替换

如果希望**使用环境变量**，修改

```dockerfile
ENV name Cloud Man
ENTRYPOINT ["/bin/sh", "-c", "echo Hello, $name"]
```

运行容器将输出

```
Hello, Cloud Man
```



### RUN

执行命令并创建新的镜像层，通常用于安装应用和软件包。

RUN 在当前镜像的顶部执行命令，并通过创建新的镜像层。Dockerfile 中常常包含多个 RUN 指令。

RUN 有两种格式：

1. Shell 格式：RUN
2. Exec 格式：RUN ["executable", "param1", "param2"]

下面是使用 RUN 安装多个包的例子：

```dockerfile
RUN apt-get update && apt-get install -y \
 bzr \
 cvs \
 git \
 mercurial \
 subversion
```

> **apt-get update 和 apt-get install 被放在一个 RUN 指令中执行**，这样能够保证每次安装的是最新的包。
>
> 如果 apt-get install 在单独的 RUN 中执行，则会使用 apt-get update 创建的镜像层，而这一层可能是很久以前缓存的。



### CMD

设置容器启动后**默认执行的命令及其参数**，此命令会在容器启动且 **==docker run 没有指定其他命令时运行==**

如果在使用`docker run` 启动容器时使用了**命令行参数**，那么dockerfile 中的**cmd 指令将无效**，CMD 被 `docker run` 后面跟的命令行参数**替换**

1. 如果 docker run 指定了其他命令，CMD 指定的默认命令将**被忽略**
2. 如果 Dockerfile 中有**多个 CMD 指令**，只有==**最后一个 CMD 有效**==

CMD 有三种格式：

1. Exec 格式：CMD ["executable","param1","param2"]
   这是 CMD 的推荐格式。
2. CMD ["param1","param2"] **为 ENTRYPOINT 提供额外的参数**，
   此时 ENTRYPOINT 必须使用 **Exec** 格式，其用途是为 ENTRYPOINT 设置**默认的参数**。
3. Shell 格式：CMD command param1 param2

CMD 是如何工作的

Dockerfile 片段

```dockerfile
CMD echo "Hello world"
```

直接运行容器输出：

```sh
docker run -it [image]

Hello world
```

但当后面加上一个命令，CMD 会被忽略掉，命令 bash 将被执行：

```sh
docker run -it [image] /bin/bash

root@10a32dc7d3d3:/#
```



### ENTRYPOINT

配置容器启动时运行的命令，可让容器以应用程序或者**服务**的形式运行。

通过ENTRYPOINT 指定的命令需要与`docker run` 启动容器进行**搭配**，将`docker run` 指令后面跟的内容当做**参数**作为ENTRYPOINT指令指定的运行命令的参数，ENTRYPOINT 指定的linux命令一般是不会被覆盖的。



ENTRYPOINT 看上去与 CMD 很像，它们都可以指定要执行的命令及其参数。不同的地方在于 ENTRYPOINT **不会被忽略，一定会被执行**，即使运行 docker run 时指定了其他命令。

ENTRYPOINT 有两种格式：

1. Exec 格式：ENTRYPOINT ["executable", "param1", "param2"] 这是 ENTRYPOINT 的推荐格式
2. Shell 格式：ENTRYPOINT command param1 param2

在为 ENTRYPOINT 选择格式时必须小心，因为这两种格式的**效果差别很大**

#### Exec 格式

ENTRYPOINT 的 Exec 格式用于设置要执行的命令及其参数，同时**可通过 CMD 提供额外的参数**

**ENTRYPOINT 中的参数**始终会被使用，而 **CMD 的额外参数**可以在容器**启动时动态替换掉**

Dockerfile 片段

```dockerfile
ENTRYPOINT ["/bin/echo", "Hello"]
CMD ["world"]
```

容器直接启动时，输出为：

```sh
docker run -it [image]

Hello world
```

这样启动，输出为：

```sh
docker run -it [image] CloudMan

Hello CloudMan
```

例如

```
FROM ubuntu
ENTRYPOINT ["/bin/echo", "Hello"]
CMD ["world"]
```

![img](https://www.liangzl.com/editorImages/cawler/20180805113916_182.jpg)

#### Shell 格式

ENTRYPOINT 的 Shell 格式会**忽略任何** CMD 或 docker run 提供的参数



#### 最佳实践

1. 使用 RUN 指令安装应用和软件包，构建镜像，两种格式都可以。
2. **CMD 和 ENTRYPOINT 推荐使用 Exec 格式**，因为指令可读性更强，更容易理解
3. 如果 Docker 镜像的用途是**运行应用程序或服务**，比如运行一个 MySQL，应该优先使用 **Exec 格式的 ENTRYPOINT 指令**。CMD 可为 ENTRYPOINT 提供**额外的默认参数**，同时可利用 docker run 命令行**替换默认参数**。
4. 如果想为容器设置**默认的启动命令**，可使用 CMD 指令。用户可在 docker run 命令行中替换此默认命令。

