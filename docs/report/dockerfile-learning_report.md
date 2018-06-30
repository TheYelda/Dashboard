# Dockerfile 学习

> author: 15331304 [@Ace-0](https://github.com/Ace-0)

## Dockerfile 简介

### Dockerfile 是什么

> 镜像的定制实际上就是定制每一层所添加的配置、文件。如果我们可以把每一层修改、安装、构建、操作的命令都写入一个脚本，用这个脚本来构建、定制镜像，那么之前提及的无法重复的问题、镜像构建透明性的问题、体积的问题就都会解决。这个脚本就是 Dockerfile。
> 

### 为什么要用 Dockerfile
Dockerfile的好处实在是太多了，除非你真的能很容易的找到一个**满足你一切需求**，**没有你不想要的层**，**非常好用**的镜像。当你想要追求以上的条件时，就可以开始考虑用Dockerfile构建属于自己的镜像了。

### Dockerfile 基本指令

Dockerfile功能很强大，它提供了十多个指令，详细可参考[Dockerfile 指令详解](https://yeasy.gitbooks.io/docker_practice/content/image/dockerfile/)



## Dockerfile 实战

下面将以构建一个后端web应用为例，学习Dockerfile的使用。这个基本的web应用包括：

- 利用`Flask`框架编写的后端应用
- MySQL作为数据库


因此，分别需要两个个容器：

- 运行后端程序的容器
- 数据库容器

<!-- more -->

### 启动 MySQL容器

对于数据库容器，可以直接使用官方的`mysql`镜像。例如：

```shell
$ sudo docker pull mysql:5.7
```

如果配置了国内官方镜像仍然无法拉取，可以使用别的镜像库，例如`DaoCloud`，`网易`等：

```shell
$ sudo docker pull daocloud.io/library/mysql:5.7.4
```

然后运行这个容器，配置端口映射。由于我本地3306已经被使用，于是配置3307端口来映射：

```shell
$ sudo docker run -d \
  -p 3307:3306 \
  --name db \
  -e MYSQL_ROOT_PASSWORD=123　\
  daocloud.io/library/mysql:5.7.4
```

这里，`-e`（`--env`）设置表示设置环境变量，将MySQL的默认`root`密码设置为`123`。

可以通过`$ sudo docker ps`看到该容器已经在运行了。同时，由于暴露了3307端口，同样也可以使用mysql客户端对这个容器进行连接：

```shell
$ mysql -h127.0.0.1 -uroot -p123 -P3307
```

接下来，可以创建一个数据库供后端程序连接。

### 编写Dockerfile

这里示例用的后端应用使用python+Flask开发，因此，整个镜像可以构建在python基础上。我使用的是`python:3.5-alpine`镜像作为基础。

在`./server/`目录下，放置了整个后端应用的代码，大致结构如下：

```shell
.
├── Dockerfile
└── server
    ├── app
    ├── config.py
    ├── instance
    ├── log
    ├── requirements.txt
    └── run.py
```

编写的Dockerfile如下：

```dockerfile
FROM python:3.5-alpine
LABEL maintainer="Jarvis-Wong"

COPY server/ /server/

WORKDIR /server

RUN pip3 install -r requirements.txt

CMD ["python3", "run.py"]
```

逐行的解释一下这个简单的Dockerfile：

FROM：指定**基础镜像**，因此一个 `Dockerfile` 中 `FROM` 是必备的指令，并且必须是第一条指令。当然，以 `scratch` 为基础镜像的话，意味着你不以任何镜像为基础，接下来所写的指令将作为镜像第一层开始存在。

LABEL：这个指令只是用于指定一些信息，这里不做解释。

COPY：格式为`COPY <源路径>... <目标路径>`或者`COPY ["<源路径1>",... "<目标路径>"]`指令将从构建上下文目录中 `<源路径>` 的文件/目录复制到新的一层的镜像内的 `<目标路径>` 位置。

WORKDIR：切换工作目录（容器中）

RUN：指令是用来执行命令行命令的。由于命令行的强大能力，`RUN` 指令在定制镜像时是最常用的指令之一。

CMD：在启动容器的时候，需要指定所运行的程序及参数。`CMD` 指令就是用于指定默认的容器主进程的启动命令的。与RUN的格式类似

总的来说，这个Dockerfile在构建镜像时，做了四件事情：

1. 找到`python:3.5-alpine`镜像，并将它作为基础。如果本地没有这个惊喜，dock同样会进行`pull`操作。
2. 将本目录下的`server/`文件夹下的内容，复制到容器的`/server/`下。
3. 在容器中，进入目录`/server`（默认会在根目录下）。
4. 在当前工作目录下，执行指令`pip3 install -r requirements.txt`，安装依赖模块。
5. 指定启动容器的命令为`python3 run.py`。

### 构建镜像

写好了Dockerfile，就可以利用它来构建镜像了：

```shell
$ sudo docker build -t app:v1 .
```

`-t`用于指定构建的容器的名字为`app`， 版本号为`v1`，没有指定版本时默认为`latest`。最后的`.`是用于指定上下文，因为在这个目录下放置了`Dockerfile`文件，因此只需要用`.`表示。当你的Dockerfile文件不在当前目录，或者用了别的文件名时，需要用其他路径代替。

运行后，可以看到镜像构建的过程。之后，使用`$ sudo docker image ls`就可以看到这个镜像了。

关于后端代码连接数据库容器的配置，需要在此步骤**之前**写好配置文件，因为镜像构建的过程中，配置文件就已经被拷贝到容器中了。

### 运行容器

此时，就可以用镜像来运行容器了：

```shell
$ sudo docker run -d \
    -p 9090:10086 \
    --name app /
    app:v1
```

这时候，后端程序已经开始运行了，并且连接上了另一个容器`db`作为它的数据库。




*本文为博主原创文章，转载请注明出处。*