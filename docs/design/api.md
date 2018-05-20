FORMAT: 1A
HOST: http://localhost

# TheYelda

TheYelda is a simple API for a medical images viewing system.

## Authentication
*The Yelda API* uses OAuth Authorization. First you create a new (or acquire existing) OAuth token using Basic Authentication(Username and Password). After you have acquired your token you can use it to access other resources within token' scope.

## Media Types
Where applicable this API uses the [HAL+JSON](https://github.com/mikekelly/hal_specification/blob/master/hal_specification.md) media-type to represent resources states and affordances.

Requests with a message-body are using plain JSON to set or update resource states.

## Error States
The common [HTTP Response Status Codes](https://github.com/TheYelda/Dashboard/blob/master/http_status_codes_reference.md) are used.

# TheYelda API Root [/]
TheYelda API entry point.

This resource does not have any attributes. Instead it offers the initial API affordances in the form of the HTTP Link header and
HAL links.

## Retrieve the Entry Point [GET]

+ Response 200 (application/hal+json)

    + Headers

            Link: <http://api.theyelda.org/>;rel="self",<http:/api.theyelda.org/authorization>;rel="authorization",<http:/api.theyelda.org/accounts>;rel="accounts"

    + Body

            {
                "message": "",
                "_links": {
                    "self": { "href": "/" },
                    "accounts": { "href": "/accounts/{?id}"},
                    "authorization": { "href": "/authorization"}
                }
            }

+ Response 400

    + Body

            {
                "message": "请求非法"
            }

# Group Access Authorization and Control
Access and Control of *The Yelda API* OAuth token.

## Authorization [/authorization/]
Authorization Resource represents an authorization granted to the user. You can **only** access your own authorization, and only through **Username and Password**.

### Create Authorization [POST]
+ Request (application/json)

    + Headers

            Accept: application/json

    + Body

            {
                "username": "yelda",
                "password": "yeldapass"
            }

+ Response 201

    + Body

            {
                "message": "登录成功",
                "account_id": 123,
                "authority": 101
            }

+ Response 401

    + Body

            {
                "message": "用户不存在"
            }

+ Response 401

    + Body

            {
                "message": "密码错误"
            }

+ Response 401

    + Body

            {
                "message": "用户审核未通过"
            }

### Remove an Authorization [DELETE]
+ Request

    + Headers

            Accept: application/json
            
    + Body

            {
                "message": "登出成功"
            }

+ Response 204

            {
            }

+ Response 401

            {
                "message": "用户未登录"
            }

# Group Self
Myself-related resources of *The Yelda API*.

## a Single Self [/Self]
A single account object. The account resource has the following attributes:

+ id

### Retrieve a Single Self [GET]

+ Request (application/json)

    + Headers

            Accept: application/json
            
    + Body

            {
            }

+ Response 200

    + Headers

            Last-Modified: 2018-04-17

    + Body

            {
                "message": "ID获取成功",
                "account_id": 123
            }

+ Response 401

            {
                "message": "用户未登录"
            }

# Group Account
Account-related resources of *The Yelda API*.

## a Single Account [/accounts/{account_id}]
A single account object. The account resource has the following attributes:

+ id
+ username
+ nickname
+ password
+ email
+ photo
+ authority

The *id* is assigned by the server at the moment of creation.

### Retrieve a Single Account [GET]

+ Request (application/json)

    + Headers

            Accept: application/json
            
    + Body

            {
            }

+ Response 200

    + Headers

            Last-Modified: 2018-04-17

    + Body

            {
                "message": "用户获取成功",
                "account_id": 364,
                "username": "yelda",
                "nickname": "Nick",
                "email": "yelda@mail.com",
                "photo": "photo1",
                "authority": 101
            }

+ Response 401

            {
                "message": "用户无法访问他人账号"
            }
            
+ Response 404

            {
                "message": "用户不存在"
            }

### Edit an Account [PUT]
To update an account, send a JSON with updated value for All of the account resource attributes EXCEPT id.

+ Request (application/json)

    + Headers

            Accept: application/json
            
    + Body

            {
                "nickname": "Nick",
                "password": "mypass",
                "email": "yelda@mail.com",
                "photo": "photo1",
                "authority": 102
            }

+ Response 200

    + Headers

            Last-Modified: 2018-04-17
            
    + Body

            {
                "message": "用户修改成功",
                "username": "yelda",
                "nickname": "Nick",
                "email": "yelda@mail.com",
                "photo": "photo1",
                "authority": 102
            }
            
+ Response 401

            {
                "message": "用户无法修改他人信息"
            }

+ Response 401

            {
                "message": "用户无法修改权限"
            }

+ Response 401

            {
                "message": "管理员无法修改本账号权限"
            }
            
+ Response 401

            {
                "message": "管理员无法修改他人权限为管理员"
            }

+ Response 404

            {
                "message": "用户不存在"
            }

### Delete an Account [DELETE]

+ Response 204

    + Body

            {
                "message": "用户删除成功"
            }

+ Response 401

            {
                "message": "用户无法删除他人账号"
            }
            
+ Response 401

            {
                "message": "管理员不可删除"
            }

+ Response 404

            {
                "message": "用户不存在"
            }


## Accounts Collection [/accounts/{?username}]
Collection of all accounts.

+ Parameters
    + username: yelda (optional, string) - Name of the Account in form of a string

### List All Accounts [GET]

+ Request (application/json)

    + Headers

            Accept: application/json
            
    + Body

            {
            }

+ Response 200

    + Headers
    
            Last-Modified: 2018-04-17
        
    + Body

            {
                "message": "用户集合获取成功",
                "data":
                        [
                            {
                                "id": 12,
                                "username": "yelda",
                                "nickname": "Nick",
                                "email": "yelda@mail.com",
                                "photo": "photo1",
                                "authority": 101
                            }, {
                                "id": 23,
                                "username": "yelda2",
                                "nickname": "Nick2",
                                "email": "yelda2@mail.com",
                                "photo": "photo2",
                                "authority": 102
                            }, {
                                "id": 34,
                                "username": "yelda3",
                                "nickname": "Nick2",
                                "email": "yelda3@mail.com",
                                "photo": "photo3",
                                "authority": 103
                            }
                        ]
            }

+ Response 401

            {
                "message": "用户无法查看他人账号"
            }

### Create an Account [POST]
To create a new account simply provide a JSON. This action requires an `authority` of `admin` .

+ Request (application/json)

    + Headers

            Accept: application/json
            
    + Body

            {
                "username": "yelda100",
                "nickname": "Nick",
                "password": "yeldapass100",
                "email": "yelda100@mail.com",
                "photo": "photo100"
            }

+ Response 201

    + Headers

            Location: /accounts/100
            
    + Body

            {
                "message": "用户创建成功"
            }

+ Response 409

            {
                "message": "用户名已存在"
            }

# Group Job
Job-related resources of *The Yelda API*.

## a Single Job [/jobs/{id}]
A single job object. The job resource has the following attributes:

+ id
+ image_id
+ doctor_id
+ label_id
+ state
+ finished_date

The *id* is assigned by the server at the moment of creation.

### Retrieve a Single Job [GET]

+ Request (application/json)

    + Headers

            Accept: application/json
            
    + Body

            {
            }

+ Response 200

    + Headers

            Last-Modified: 2018-04-17

    + Body

            {
                "message": "成功查看任务",
                "job_id": "1",    
                "image_id": "123",
                "label_id": "12345",
                "account_id": "1234",
                "finished_date": "2018-04-17",
                "job_state": "200"
            }
+ Response 404

    + Headers

            Last-Modified: 2018-04-17

    + Body

            {
                "message": "任务不存在"
            }

+ Response 401

    + Headers

            Last-Modified: 2018-04-17

    + Body

            {
                "message": "用户未登录"
            }

+ Response 403

    + Headers

            Last-Modified: 2018-04-17

    + Body

            {
                "message": "用户无法访问他人任务"
            }

### Edit a Job [PUT]
To update a job, send a JSON with updated value for All of the job resource attributes EXCEPT id.

+ Request (application/json)

    + Headers

            Accept: application/json

    + Body

            {
                "image_id": "123",
                "account_id": "1234",
                "label_id": "12345",
                "job_state": "200",
                "finished_date": "2018-04-17"
            }

+ Response 200

    + Headers

            Location: /jobs/1
            
    + Body

            {
                "message": "成功编辑任务",
                "job_id": "1",    
                "image_id": "123",
                "label_id": "12345",
                "account_id": "1234",
                "finished_date": "2018-04-17",
                "job_state": "200"
            }

+ Response 401

    + Headers

            Location: /jobs/1
            
    + Body

            {
                "message": "用户未登录"
            }

+ Response 403

    + Headers

            Location: /jobs/1
            
    + Body

            {
                "message": "用户无法修改他人任务"
            }

+ Response 403

    + Headers

            Location: /jobs/1
            
    + Body

            {
                "message": "用户无法修改已完成的任务"
            }

+ Response 404

    + Headers

            Last-Modified: 2018-04-17

    + Body

            {
                "message": "任务不存在"
            }

### Delete a Job [DELETE]

+ Response 200

    + Headers

            Location: /jobs/1
            
    + Body

            {
                "message": "已删除该任务"
            }
+ Response 401

    + Headers

            Location: /jobs/1
            
    + Body

            {
                "message": "用户未登录"
            }

+ Response 403

    + Headers

            Location: /jobs/1
            
    + Body

            {
                "message": "删除任务需要管理员权限"
            }

+ Response 404

    + Headers

            Last-Modified: 2018-04-17

    + Body

            {
                "message": "任务不存在"
            }

## Jobs Collection [/jobs/{?account_id}]
Collection of all jobs.

+ Parameters
    + account_id: 1 (optional, int) - ID of the doctor who the job is assigned to in form of an integer

### List All Jobs [GET]

+ Request (application/json)

    + Headers

            Accept: application/json
            
    + Body

            {
            }

+ Response 200

    + Headers
    
            Last-Modified: 2018-04-17
        
    + Body

            {
                "message":"成功获取所有任务",
                "data":
                    [
                        {
                            "job_id": "1",    
                            "image_id": "123",
                            "label_id": "12345",
                            "account_id": "1234",
                            "finished_date": "2018-04-17",
                            "job_state": "200"
                        }, {
                            "job_id": "2",    
                            "image_id": "456",
                            "label_id": "6798",
                            "account_id": "1234",
                            "finished_date": "2018-04-17",
                            "job_state": "200"
                        }, {
                            "job_id": "3",    
                            "image_id": "756",
                            "label_id": "9798",
                            "account_id": "1834",
                            "finished_date": "2018-04-17",
                            "job_state": "200"
                        }
                ]
            }

+ Response 401

    + Headers
    
            Last-Modified: 2018-04-17
        
    + Body

            {
                "message":"用户未登录"
            }

+ Response 403

    + Headers
    
            Last-Modified: 2018-04-17
        
    + Body

            {
                "message":"用户无法访问其他用户的任务列表"
            }

### Create a Job [POST]
To create a new job simply with a JSON. This action requires an `authority` of `admin` .

+ Request (application/json)

    + Headers

            Accept: application/json
            
    + Body

            {
                "image_id": "123",
                "account_id": "1234"
            }

+ Response 201

    + Headers

            Location: /jobs/1
            
    + Body

            {
                "message": "任务创建成功",
                "job_id": "3",    
                "image_id": "756",
                "label_id": "9798",
                "account_id": "1834",
                "finished_date": "2018-04-17",
                "job_state": "200"
            }

+ Response 401

    + Headers

            Location: /jobs/1
            
    + Body

            {
                "message": "用户未登录"
            }

+ Response 403

    + Headers

            Location: /jobs/1
            
    + Body

            {
                "message": "创建任务需要管理员权限"
            }

# Group Image
Image-related resources of *The Yelda API*.

## a Single Image [/images/{id}]
A single image object. The image resource has the following attributes:

+ id
+ groundtruth_id
+ state
+ info_id

The *id* is assigned by the server at the moment of creation.

### Retrieve a Single Image [GET]

+ Request (application/json)

    + Headers

            Accept: application/json
            
    + Body

            {
            }

+ Response 200

    + Headers

            Last-Modified: 2018-04-17

    + Body

            {
                "message": "",
                "id": "1",    
                "groundtruth_id": "123",
                "state": "unassigned",
                "info_id": "123"
            }

+ Response 401

    + Headers

            Last-Modified: 2018-04-17

    + Body

            {
                "message": "用户未登录"
            }

### Edit an Image [PUT]
To update an image, send a JSON with updated value for All of the image resource attributes EXCEPT id.

+ Request (application/json)

    + Headers

            Accept: application/json

    + Body

            {
                "id": "1",
                "groundtruth_id": "123",
                "state": "assigned",
                "info_id": "123"
            }

+ Response 200

    + Headers

            Location: /images/1
            
    + Body

            {
                "message": "",
                "id": "1",
                "groundtruth_id": "123",
                "state": "assigned",
                "info_id": "123"
            }

+ Response 401

    + Headers

            Location: /images/1
            
    + Body

            {
                "message": "用户未登录"
            }

+ Response 401

    + Headers

            Location: /images/1
            
    + Body

            {
                "message": "修改图片信息需要管理员权限"
            }

### Delete an Image [DELETE]

+ Response 204

    + Headers

            Location: /images/1
            
    + Body

            {
                "message": "已删除图片"
            }
+ Response 401

    + Headers

            Location: /images/1
            
    + Body

            {
                "message": "用户未登录"
            }

+ Response 401

    + Headers

            Location: /images/1
            
    + Body

            {
                "message": "删除图片需要管理员权限"
            }

## Images Collection [/jobs/{?state}]
Collection of all images.


+ Parameters
    + state: "unassigned" (optional, string) - ID of the image in form of a string

### List All Images [GET]

+ Request (application/json)

    + Headers

            Accept: application/json
            
    + Body

            {
            }

+ Response 200

    + Headers
    
            Last-Modified: 2018-04-17
        
    + Body

            {
                "message":"",
                "data":
                    [
                        {
                            "id": "1",    
                            "groundtruth_id": "123",
                            "state": "unassigned",
                            "info_id": "123"
                        }, {
                            "id": "2",    
                            "groundtruth_id": "234",
                            "state": "unassigned",
                            "info_id": "234"
                        }, {
                            "id": "3",    
                            "groundtruth_id": "345",
                            "state": "unassigned",
                            "info_id": "345"
                        }
                    ]
            }

+ Response 401

    + Headers

            Location: /images/1
            
    + Body

            {
                "message": "用户未登录"
            }

### Create an Image [POST]
To create a new image simply with a JSON. This action requires an `authority` of `admin` .

+ Request (application/json)

    + Headers

            Accept: application/json
            
    + Body

            {
                "file_path": "/images/abc.png"
            }

+ Response 201

    + Headers

            Location: /images/1
            
    + Body

            {
                "message": "",
                "id": "1",
                "state": "unassigned",
                "info_id": "123"
            }

+ Response 401

    + Headers

            Location: /images/1
            
    + Body

            {
                "message": "用户未登录"
            }

+ Response 401

    + Headers

            Location: /images/1
            
    + Body

            {
                "message": "创建图片需要管理员权限"
            }

# Group Label
Label-related resources of *The Yelda API*.

## a Single Label [/label/{id}]
A single label object. The label resource has the following attributes:

+ id
+ quality
+ dr
+ stage
+ dme
+ hr
+ agedme
+ rvo
+ crao
+ myopia
+ od
+ glaucoma
+ comment

The *id* is assigned by the server at the moment of creation.

### Retrieve a Single Label [GET]

+ Request (application/json)

    + Headers

            Accept: application/json
            
    + Body

            {
            }

+ Response 200

    + Headers

            Last-Modified: 2018-04-17

    + Body

            {
                "label_id": 1,
                "quality": true,
                "dr": false,
                "stage": "2",
                "dme": true,
                "hr": "1",
                "age_dme": "1",
                "rvo": true,
                "crao": true,
                "myopia": true,
                "od": true,
                "glaucoma": true,
                "others": true,
                "comment": "1",
                "message": "label获取成功"
            }

+ Response 401

    + Headers

            Last-Modified: 2018-04-17

    + Body

            {
                "message": "用户未登录"
            }

+ Response 401

    + Headers

            Last-Modified: 2018-04-17

    + Body

            {
                "message": "用户无法访问其他用户的标注信息"
            }

### Edit a Label [PUT]
To update a label, send a JSON with updated value for All of the label resource attributes EXCEPT id.

+ Request (application/json)

    + Headers

            Accept: application/json

    + Body

            {
                "quality":true,
                "dr":false,
                "stage":"3",
                "dme":true,
                "hr":"1",
                "age_dme":"1",
                "rvo":true,
                "crao":true,
                "myopia":true,
                "od":true,
                "glaucoma":true,
                "others":true,
                "comment":"1"
            }

+ Response 200

    + Headers

            Last-Modified: 2018-04-17
            
    + Body

            {
              "quality": true,
              "dr": false,
              "stage": "3",
              "dme": true,
              "hr": "1",
              "age_dme": "1",
              "rvo": true,
              "crao": true,
              "myopia": true,
              "od": true,
              "glaucoma": true,
              "others": true,
              "comment": "1",
              "message": "label修改成功"
            }

+ Response 401

    + Headers

            Location: /images/1
            
    + Body

            {
                "message": "用户未登录"
            }

+ Response 401

    + Headers

            Location: /images/1
            
    + Body

            {
                "message": "用户无法修改其他用户的标注信息"
            }

+ Response 401

    + Headers

            Location: /images/1
            
    + Body

            {
                "message": "用户无法修改该标注信息"
            }

### Delete an Label [DELETE]

+ Response 204

    + Headers

            Location: /images/1
            
    + Body

            {
                "message": "已删除标注信息"
            }

+ Response 401

    + Headers

            Location: /images/1
            
    + Body

            {
                "message": "用户未登录"
            }

+ Response 401

    + Headers

            Location: /images/1
            
    + Body

            {
                "message": "用户无法删除其他用户的标注"
            }

## Labels Collection [/labels]
Collection of all labels.

### Create a Label [POST]
To create a new image simply with a JSON. This action requires an `authority` of `admin` .

+ Request (application/json)

    + Headers

            Accept: application/json
            
    + Body

            {
                "quality":true,
                "dr":false,
                "stage":"2",
                "dme":true,
                "hr":"1",
                "age_dme":"1",
                "rvo":true,
                "crao":true,
                "myopia":true,
                "od":true,
                "glaucoma":true,
                "others":true,
                "comment":"1"
            }

+ Response 201

    + Headers

            Last-Modified: 2018-04-17
            
    + Body

            {
              "label_id": 4,
              "quality": true,
              "dr": false,
              "stage": "2",
              "dme": true,
              "hr": "1",
              "age_dme": "1",
              "rvo": true,
              "crao": true,
              "myopia": true,
              "od": true,
              "glaucoma": true,
              "others": true,
              "comment": "1",
              "message": "创建成功"

            }

+ Response 401

    + Headers

            Location: /images/1
            
    + Body

            {
                "message": "用户未登录"
            }
         
## Codes

### Authority 1XX

| code |  name  | description |
| :--: | :----: | :---------: |
| 100  |  None  |             |
| 101  | Admin  |             |
| 102  | Doctor |             |
| 103  | Guest  |             |

### Job State 2XX

| code |   name    | description |
| :--: | :-------: | :---------: |
| 200  | Unlabeled |             |
| 201  | Labeling  |             |
| 202  | Finished  |             |


### Image State 3XX

| code |    name    | description |
| :--: | :--------: | :---------: |
| 300  | Unassigned |             |
| 301  |  Running   |             |
| 302  | Different  |             |
| 303  |    Done    |             |
