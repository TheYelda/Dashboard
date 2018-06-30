# 使用 Docker-Compose 部署项目

> author: 15331304 [@Ace-0](https://github.com/Ace-0)

## Docker-Compose简介

### Docker-Compose是什么

>`Docker Compose` 是 Docker 官方编排（Orchestration）项目之一，负责快速的部署分布式应用。

其代码目前在 <https://github.com/docker/compose> 上开源。它的定位是**定义和运行多个 Docker 容器的应用**（Defining and running multi-container Docker applications）。

### 为什么要使用Docker-Compose

在[之前的文章](https://ace-0.github.io/2018/06/20/dockerfile-learning/)中，我们了解到使用一个`Dockerfile`可以很方便的自定义一个容器。然而，在实际的生产环境中，一个单独的容器往往是不足以完成任务的。例如，对于一个简单的网站，就需要一个web应用本身的容器，一个数据库容器 ，甚至是一个负载均衡服务器的容器。`Docker-Compose`正是为了解决这样的问题而诞生的，我们可以通过一个`docker-compose.yml`文件，定义一组容器的关联关系和依赖关系，将它们视作一个整体的项目(project)。`Compose`的官方图标就很好的说明了它的作用：

![](/images/compose-logo.png)



### 关于Compose的基本概念

> `Compose` 中有两个重要的概念：
>
> - 服务 (`service`)：一个应用的容器，实际上可以包括若干运行相同镜像的容器实例。
>
> - 项目 (`project`)：由一组关联的应用容器组成的一个完整业务单元，在 `docker-compose.yml` 文件中定义。
>
> `Compose` 的默认管理对象是项目，通过子命令对项目中的一组容器进行便捷地生命周期管理。



## Docker-Compose实战

### 安装Docker-Compose

验证安装：

```shell
$ sudo docker-compose --version
```

如果还未安装的话，有以下两种方式：

- 使用`github`二进制文件安装（建议）

  ```shell
  $ sudo curl -L https://github.com/docker/compose/releases/download/1.17.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
  $ sudo chmod +x /usr/local/bin/docker-compose
  ```

- 使用`PIP`安装：

  ```shell
  $ sudo pip install -U docker-compose
  ```



### 使用Docker-Compose

接下来，我们使用`Docker-Compose`，搭建一个`Nginx + Flask + MySQL`的名为`yelda`的web应用。

<!-- more -->

项目的目录如下：

```shell
.
├── docker-compose.yml
├── my-app
│   ├── Dockerfile
│   └── server
│       ├── app
│       ├── config.py
│       ├── instance
│       ├── log
│       ├── requirements.txt
│       └── run.py
└── my-nginx
    ├── conf.d
    │   └── yelda.conf
    ├── dist
    │   ├── caman.full.js
    │   ├── css
    │   ├── favicon.ico
    │   ├── index.html
    │   ├── js
    │   ├── viewer.css
    │   └── viewer.js
    └── Dockerfile

```

其中，

- `server/`目录下是后端代码，也是后端启动目录。该应用监听的是`10086`端口。
- `conf.d`目录下的`yelda.conf`，是`Nginx`的配置文件。在这个文件中，我配置了`Nginx`监听`8081`端口，并且将`/api/`请求转发到`10086`端口，即后端应用运行的端口。（我使用的是`daocloud.io/library/nginx:1.13.0-alpine `版本镜像，旧版本下配置文件应该放置在`sites-enabled`目录下）。
- `dist/`目录下是前端文件。

### 分别编写 Dockerfile

后端容器和负载均衡容器分别使用了它们各自的`Dockerfile`来构建，而数据库我直接使用了官方镜像。

后端容器的`Dockerfile`如下：

```dockerfile
FROM python:3.5-alpine
LABEL maintainer="Jarvis-Wong"

COPY server/ /www/

WORKDIR /www

RUN pip3 install -r requirements.txt

CMD ["python3", "run.py"]
```

完成了**拷贝代码，切换工作目录，安装依赖，设置启动命令**的任务。

负载均衡容器的`Dockerfile`如下：

```dockerfile
FROM daocloud.io/library/nginx:1.13.0-alpine
LABEL maintainer="Jarvis-Wong"

COPY dist/ /static/
COPY conf.d/ /etc/nginx/conf.d
```

完成了**拷贝前端代码，拷贝配置文件**的任务。

这里的配置文件很简单，只是设置了根目录，并配置了转发。

```yaml
server {
    listen 8081;
    
    # SSL configuration     
    # listen 443 ssl;
    server_name localhost;

    root /static/;

    #access_log /log/access_log;
    #error_log /log/error_log;

    location ~ ^\/api\/.*$ {
        rewrite ^.+api/?(.*)$ /$1 break;
        proxy_pass       http://myhost:10086;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }  
}
```



到了这里，你当然可以分别运行三个容器，暴露端口，这样整个web应用就能运行起来了。但是，相比起每次要输入一长串的命令，以及更新后的多次构建，甚至是要运行多个相同镜像的容器实例时，`Docker-Compose`就会起作用了。

### 使用 docker-compose.yml 关联容器

`docker-compose.yml`放置在根目录下：

```yaml
version: '3'
services:
  
  server:
    build: my-nginx/.
    ports:
      - "8081:8081"

  webapp:
    build: my-app/.

    ports:
      - "10086:10086"
 
    depends_on:
      - "db"

  db:
    image: daocloud.io/library/mysql:5.7.4

    ports:
      - "3307:3306"
      
    environment:
      - MYSQL_ROOT_PASSWORD=123
      - MYSQL_DATABASE=yelda
```

这个模板文件中，定义了三个`service`：server，webapp和db，分别对应者负载均衡器，后端应用和数据库。而这三个`service`就组成了一个`project`。

#### server service

这一部分，实际上就是指定以`mg-nginx/`作为上下文构建镜像，类似于进入到`my-nginx/`目录下，然后`docker build .`。`ports`指定了暴露的端口映射，类似于直接运行`docker run`时的`-p`参数。

#### webapp service

后端容器的构建也很简单，同样指定上下文构建，并暴露端口。`depends_on`指明了依赖关系，解决容器的依赖、启动先后的问题。这里指明了`webapp`必须在`db`之后启动。原因很简单，我的后端应用在启动时就需要连接数据库，那么数据库自然就要先于它启动了。

#### db service

数据库容器，我并没有自己构建镜像，而是直接使用了官方镜像，也就是`image`字段指明的镜像。`image`用于指明该容器所用镜像，如果本地没有该镜像，同样会执行`pull`操作。`environment`字段用于设置环境变量，例如，在这里，我设置了`MySQL`的root密码，并新建了一个名为`yelda`的数据库。

### 使用Docker-Compose启动

接下来就是启动容器了：

```shell
$ sudo docker-compose up
```

只需要这一句命令，`Docker-Compose`和`Docker`会为你完成所有指定好的事情。现在，在浏览器输入`localhost:8081`就可以访问到这个web应用了。以上的运行放，你可以在控制台看到所有输出，一般来说会在后台启动：

```shell
$ sudo docker-compose up -d
Starting yelda_server_1 ... done
Starting yelda_db_1     ... done
Starting yelda_webapp_1 ... done
```

可以看到，这三个容器都已经在后台运行了：

```shell
CONTAINER ID        IMAGE                             COMMAND                  CREATED             STATUS              PORTS                            NAMES
36e623937d9a        yelda_webapp                      "python3 run.py"         About an hour ago   Up 56 seconds       0.0.0.0:10086->10086/tcp         yelda_webapp_1
f5a199db124b        daocloud.io/library/mysql:5.7.4   "/entrypoint.sh mysq…"   About an hour ago   Up 57 seconds       0.0.0.0:3307->3306/tcp           yelda_db_1
1470e4a86eec        yelda_server                      "nginx -g 'daemon of…"   About an hour ago   Up 56 seconds       80/tcp, 0.0.0.0:8081->8081/tcp   yelda_server_1
```

要关闭它们，也只需要一个命令：

```shell
$ sudo docker-compose down
Stopping yelda_webapp_1 ... done
Stopping yelda_db_1     ... done
Stopping yelda_server_1 ... done
Removing yelda_webapp_1 ... done
Removing yelda_db_1     ... done
Removing yelda_server_1 ... done
Removing network yelda_default
```

就能关掉所有用`up`启动的容器并移除网络。并且，这些终止运行的容器并不会继续存在，即使用`docker container ls -a`也看不到它们，因为它们已经自动被删除了。

当然，`docker-compose`提供了更多自由的命令来完成这个过程：[compose命令详解](https://yeasy.gitbooks.io/docker_practice/content/compose/commands.html)

例如，你可以先进行容器构建，而不启动：

```shell
$ sudo docker-compose build
```

再使用：

```shell
$ sudo docker-compose run db
```

来只启动数据库容器等。



### FAQ

1. 关于`depends_on`，该参数只是保证了**启动顺序**，例如，在上文中，可以保证先启动`db`再启动`webapp。`然而，并不能保证`webapp`会在`db`**完全启动**之后再启动。如果遭遇到启动顺序上的问题，可以尝试手动先启动数据库。
2. 由于`Docker`会使用缓存机制，因此你有时候会发现容器内部的内容可能没有更新。例如，当更改了`docker-compose.yml`等文件时，直接使用`docker-compose up`是不正确的，需要重新构建，可以在后面加上`--build`参数。
3. 同样由于缓存机制，容器内部拷贝内容会没有更新，可以指定`--no-cache`命令，以及在运行时使用`--force-recreate`参数。如果这还没有解决问题，直接删除掉容器重新构建就好了。


*本文为博主原创文章，转载请注明出处。*