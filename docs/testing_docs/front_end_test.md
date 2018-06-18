# 前端本地测试方法

---

此文档暂时作废。

---

### 前期准备

下载后端代码：

```shell
$ git clone https://github.com/TheYelda/
```

如果已有直接更新

```shell
$ cd AppServer
$ git pull
```

安装`MySQL'

[Ubuntu 16.04 上安装 MySQL 5.7 教程](https://www.linuxidc.com/Linux/2017-05/143864.htm)

登录`MySQL`：

``` shell
$ mysql -u{username} -p
```

接下来会提示输入密码。

登录进入`MySQL`后，创建数据库并进入。注意，创建数据库时指定了编码格式为UTF-8：

```mysql
mysql> CREATE DATABASE yelda DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;
mysql> USE yelda;
```

数据表无需创建，后端代码会完成。

进入后端代码库：

```shell
$ cd AppServer/server
```

创建你的本地配置文件：

```shell
$ mkdir instance
$ vim instance/config.py
```

在`server/instance/config.py`中输入你的配置内容：

```python
DB_USERNAME = MySQL用户名    // 例如 DB_USERNAME = 'root'
DB_PASSWORD = MySQL密码      // 
DB_NAME = 数据库名称          // 
SECRET_KEY = 用户会话管理秘钥  // 例如 SECRET_KEY = 'hello world'
PHOTOS_FOLDER = 头像路径      // 例如 PHOTOS_FOLDER = 'yelda/photos'
MEDICAL_IMAGES_FOLDER = 医学图像路径  // 例如 MEDICAL_IMAGES_FOLDER = 'yelda/medical-images'
MAX_CONTENT_LENGTH = 16 * 1024 * 1024  // 上传文件大小限制
```

修改代码解决**跨域问题**：

```shell
$ vim app/__init__.py
```

在import语句中加入：

```python
from flask_cors import *
```

在`app = Flask(__name__, instance_relative_config=True)`后加入语句，变为：

```python
app = Flask(__name__, instance_relative_config=True)
CORS(app, supports_credentials=True)
```

之后可能需要在虚拟环境中重新安装`flask-cors`模块

创建虚拟环境并启动（需要先安装virtualenv）：

```shell
$ virtualenv venv  // 如果已有，无须再创建
$ source venv/bin/activate
```

运行服务器：

```shell
(venv) $ python3 run.py
```

### 本地测试

本地测试时URL使用`http://localhost:10086/`

例如：

```shell
this.$http.post('http://localhost:10086/authorization/', this.loginForm).then(res => {
```

为了便于测试，需要先在数据库中预设账号，但是由于数据库中`password`属性已被哈希，直接插入记录不现实，可以先通过注册功能插入用户，再使用`MySQL`修改权限。

打开`index.html`完成注册（最好至少注册三个账号，分别代表`Admin, Doctor, Guest`），在`MySQL`中：

```mysql
mysql> SELECT * FROM Accounts;
```

可以看到数据库中的账号，当然权限都是`100(Empty)`

分别修改它们的权限，例如把`account_id`为1的账号权限修改为`Admin`：

```mysql
mysql> UPDATE Accounts SET authority = 101 WHERE account_id = 1;
```

之后就可以利用这些账号正常登录进行操作和调试。

每一个请求和响应的具体内容可以在浏览器的开发者工具中查看，后端收到请求并响应的内容可以再后端运行的终端中查看，例如：

```shell
2018-05-09 12:52:47,744 INFO sqlalchemy.engine.base.Engine {'username_1': 'admin'}
127.0.0.1 - - [09/May/2018 12:52:47] "POST /authorization/ HTTP/1.1" 200 -
```

