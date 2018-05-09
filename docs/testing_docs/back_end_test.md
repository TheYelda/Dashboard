# 后端本地调试方法

### 前期准备

安装`MySQL`。

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

如果不指定编码格式为UTF-8，交互插入数据时数据库可能会抛出异常。

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
```

然后，在虚拟环境中运行`python3 run.py`建表并运行服务器。

为了便于测试，在正式测试前，需要先在数据库中预设账号，但是由于数据库中`password`属性已被哈希，直接插入记录不现实，可以先通过注册功能插入用户，再使用`MySQL`修改权限。

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

### 本地测试

所有的`API视图函数`前都已经加上装饰器：

```python
@api.doc(parser=api.parser()
		.add_argument('body', type=str, required=True, help='json', location='json')
        )
```

利用该函数可以方便后端本地测试。

打开浏览器，输入`localhost:5000`。在`Flask`提供的GUI界面中根据REST API进行测试，例如，以`POST /authorization/`为例，在`Value`部分输入完整`JSON`：

```json
{"username": "admin", "password": "123"}
```

即可获得正确返回：

```json
{
  "message": "登录成功",
  "account_id": 1,
  "authority": 101
}
```

且浏览器会保持登录状态。

