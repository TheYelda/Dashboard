# REST API设计规范

### 什么是REST API

REST API 是一系列个体可描述 (individually-addressable) 的资源（API的名词）的模型。资源可以通过他们的资源名称来提及，并可以通过一个小集合内的方法（即API的动词）来操作。

REST Google API 的标准方法（也被称为REST方法）包括List, Get, Create Update 和Delete。当功能不能轻松地映射到标准方法时，如数据库事务，API设计者也可以使用自定义方法（也被称为自定义动词或自定义操作）。

### 为什么要使用REST API

网络应用程序，分为前端和后端两个部分。当前的发展趋势，就是前端设备层出不穷（手机、平板、桌面电脑、其他专用设备......）。

因此，必须有一种统一的机制，方便不同的前端设备与后端进行通信。这导致API构架的流行，甚至出现"API First"的设计思想。[RESTful API](http://en.wikipedia.org/wiki/Representational_state_transfer)是目前比较成熟的一套互联网应用程序的API设计理论。

### REST API设计细节

**协议**

API与用户的通信协议，总是使用HTTPs协议

**域名**

应该尽量将API部署在专用域名之下。

```
https://api.yelda.com
```

如果确定API很简单，不会有进一步扩展，可以考虑放在主域名下。

````
https://yelda.org/api/
````

**版本**

应该将API的版本号放入URL，例如

```
https://api.yelda.com/v1/
```

**EndPoint**

在RESTful架构中，每个网址代表一种资源（resource），所以网址中不能有动词，只能有名词，而且所用的名词往往与数据库的表格名对应。一般来说，数据库中的表都是同种记录的"集合"（collection），所以API中的名词也应该使用复数。例如：

```
https://api.yelda.com/v1/accounts
```

**动词**

HTTP动词包括

- GET：取出资源
- POST：新建一个资源
- PUT：更新资源（需提供整个资源的所有信息）
- DELETE：删除资源
- PATCH：更新资源（仅提供需要改变的属性）
- HEAD：获取资源元数据
- OPTIONS：获取信息，关于资源的哪些属性是客户端可以改变的

例如：

```
GET /accounts：列出所有账户
POST /accounts：新建一个账户
GET /accounts/account_id：获取某个指定账户的信息
PUT /accounts/account_id：更新某个指定账户的信息（提供该账户的全部信息）
PATCH /accounts/account_id：更新某个指定账户的信息（提供该账户的部分信息）
DELETE /accounts/account_id：删除某个账户
GET /accounts/account_id/settings：列出某个指定账户的所有个人设置
DELETE /accounts/account_id：删除某个指定账户
```

**过滤信息**

如果记录数量很多，服务器不可能都将它们返回给用户。API应该提供参数，过滤返回结果。

常见参数包括：

```
?limit=10：指定返回记录的数量
?offset=10：指定返回记录的开始位置。
?page=2&per_page=100：指定第几页，以及每页的记录数。
?sortby=username&order=asc：指定返回结果按照哪个属性排序，以及排序顺序。
?authority=1：指定筛选条件
```

参数的设计允许存在冗余，即允许API路径和URL参数偶尔有重复。比如，`GET /accounts/ID`和`GET /accounts?account_id=ID`的含义是相同的。

**状态码**

参见[HTTP Status Code Reference](https://github.com/TheYelda/Dashboard/blob/master/docs/development_standard/http_status_codes_reference.md)

**错误处理**

如果状态码是4xx，就应该向用户返回出错信息。例如：

```
{
    "message": "wrong password"
    ...
}
```

**返回结果**

针对不同操作，服务器向用户返回的结果应该符合以下规范

```
GET /collection：返回资源对象的列表（数组）
GET /collection/resource：返回单个资源对象
POST /collection：返回新生成的资源对象
PUT /collection/resource：返回完整的资源对象
PATCH /collection/resource：返回完整的资源对象
DELETE /collection/resource：返回一个空文档
```

**Hypermedia API**

RESTful API最好做到[Hypermedia](http://www.infoq.com/cn/articles/mark-baker-hypermedia)，即返回结果中提供链接，连向其他API方法，使得用户不查文档，也知道下一步应该做什么。

比如，当用户向api.yelad.com的根目录发出请求，会得到这样一个文档。

```
{
    ...
    "_links": {
        "self": { "href": "/" },
        "accounts": { "href": "/accounts/{?id}"},
        "authorization": { "href": "/authorization"}
    }
    ...
}
```

上面代码表示，文档中有一个link属性，用户读取这个属性就知道下一步该调用什么API了。rel表示这个API与当前网址的关系（collection关系，并给出该collection的网址），href表示API的路径，title表示API的标题，type表示返回类型。

Github的API就是这种设计，访问[api.github.com](https://api.github.com/)会得到一个所有可用API的网址列表。

**其它**

- API的身份认证应该使用[OAuth 2.0](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)框架。
- 服务器返回的数据格式，应该尽量使用JSON，避免使用XML。