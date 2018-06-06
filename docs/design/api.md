FORMAT: 1A
HOST: http://localhost

# TheYelda

## Notice:

For MUCH better reading experience, please read the document at [theyelda.docs.apiary.io](https://theyelda.docs.apiary.io/#).

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
            }

+ Response 204

            {
                "message": "登出成功"
            }

+ Response 401

            {
                "message": "用户未登录"
            }

# Group Self
Myself-related resources of *The Yelda API*.

## a Single Self [/self/]
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

+ account_id
+ username
+ nickname
+ password
+ email
+ photo
+ authority

The *id* is assigned by the server at the moment of creation.

+ Parameters
    + account_id: 123 (required, int) - ID of the Account in form of an integer

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
                "photo": "photo1.png",
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
                "photo": "photo1.png",
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
                "photo": "photo2.png",
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


## Accounts Collection [/accounts/{?username}{?authority}]
Collection of all accounts.

+ Parameters
    + username: yelda (optional, string) - Name of the Account in form of a string
    + authority: 100 (optional, int) - Authority of the Account in form of an integer

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
                                "account_id": 12,
                                "username": "yelda",
                                "nickname": "Nick",
                                "email": "yelda@mail.com",
                                "photo": "photo1.png",
                                "authority": 102
                            }, {
                                "account_id": 23,
                                "username": "yelda2",
                                "nickname": "Nick2",
                                "email": "yelda2@mail.com",
                                "photo": "photo2.png",
                                "authority": 102
                            }, {
                                "account_id": 34,
                                "username": "yelda3",
                                "nickname": "Nick2",
                                "email": "yelda3@mail.com",
                                "photo": "photo3.png",
                                "authority": 102
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
                "email": "yelda100@mail.com"
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

## a Single Job [/jobs/{job_id}]
A single job object. The job resource has the following attributes:

+ job_id
+ image_id
+ account_id
+ label_id
+ job_state
+ finished_date

The *id* is assigned by the server at the moment of creation.

+ Parameters
    + job_id: 253 (required, int) - ID of the Job in form of an integer

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
                "message": "",
                "job_id": 132,    
                "image_id": 427,
                "account_id": 42,
                "label_id": 88,
                "job_state": 200,
                "finished_date": "2018-04-17"
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

+ Response 401

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
                "label_id": 88,
                "job_state": 204
            }

+ Response 200

    + Headers

            Location: /jobs/1
            
    + Body

            {
                "message": "任务修改成功",
                "job_id": 132,    
                "image_id": 427,
                "account_id": 42,
                "label_id": 88,
                "job_state": 200,
                "finished_date": "2018-04-17"
            }

+ Response 401

    + Headers

            Location: /jobs/1
            
    + Body

            {
                "message": "用户未登录"
            }

+ Response 401

    + Headers

            Location: /jobs/1
            
    + Body

            {
                "message": "用户无法修改他人任务"
            }

+ Response 401

    + Headers

            Location: /jobs/1
            
    + Body

            {
                "message": "用户无法修改已完成的任务"
            }
            
+ Response 400

    + Headers

            Location: /jobs/1
            
    + Body

            {
                "message": "指定的用户或图像不存在"
            }


### Delete a Job [DELETE]

+ Response 204

    + Headers

            Location: /jobs/1
            
    + Body

            {
                
            }
+ Response 401

    + Headers

            Location: /jobs/1
            
    + Body

            {
                "message": "用户未登录"
            }

+ Response 401

    + Headers

            Location: /jobs/1
            
    + Body

            {
                "message": "删除任务需要管理员权限"
            }

+ Response 404

    + Headers

            Location: /jobs/1
            
    + Body

            {
                "message": "任务不存在"
            }

## Jobs Collection [/jobs/]
Collection of all jobs.

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
                "message":"任务集合获取成功",
                "data":
                    [
                        {
                            "job_id": 132,    
                            "image_id": 427,
                            "account_id": 42,
                            "label_id": 88,
                            "job_state": 200,
                            "finished_date": "2018-04-17"
                        }, {
                            "job_id": 1234,    
                            "image_id": 427,
                            "account_id": 42,
                            "label_id": 88,
                            "job_state": 200,
                            "finished_date": "2018-04-17"
                        }, {
                            "job_id": 352,    
                            "image_id": 427,
                            "account_id": 42,
                            "label_id": 88,
                            "job_state": 200,
                            "finished_date": "2018-04-17"
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

+ Response 401

    + Headers
    
            Last-Modified: 2018-04-17
        
    + Body

            {
                "message":"用户没有权限访问其他用户的任务列表"
            }

### Create a Job [POST]
To create a new job simply with a JSON. This action requires an `authority` of `admin` .

+ Request (application/json)

    + Headers

            Accept: application/json
            
    + Body

            {
                "image_id": 42,
                "account_id": 253
            }

+ Response 201

    + Headers

            Location: /jobs/1
            
    + Body

            {
                "message": "任务创建成功",
                "job_id": 132,    
                "image_id": 427,
                "account_id": 42,
                "label_id": 88,
                "job_state": 200,
                "finished_date": "2018-04-17"
            }

+ Response 401

    + Headers

            Location: /jobs/1
            
    + Body

            {
                "message": "用户未登录"
            }

+ Response 401

    + Headers

            Location: /jobs/1
            
    + Body

            {
                "message": "创建任务需要管理员权限"
            }
            
+ Response 400

    + Headers

            Location: /jobs/1
            
    + Body

            {
                "message": "指定的用户或图像不存在"
            }

# Group Image
Image-related resources of *The Yelda API*.

## a Single Image [/images/{image_id}]
A single image object. The image resource has the following attributes:

+ image_id
+ label_id
+ image_state
+ filename
+ source

The *id* is assigned by the server at the moment of creation.

+ Parameters
    + image_id: 123 (required, int) - ID of the Image in form of an integer

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
                "message": "图像获取成功",
                "label_id": 67,
                "image_state": 300,
                "filename": "image1.png",
                "source": "someone"
            }

+ Response 401

    + Headers

            Last-Modified: 2018-04-17

    + Body

            {
                "message": "用户未登录"
            }

+ Response 404

    + Headers

            Location: /images/1
    
    + Body

            {
                "message": "图像不存在"
            }

### Edit an Image [PUT]
To update an image, send a JSON with updated value for All of the image resource attributes EXCEPT id.

+ Request (application/json)

    + Headers

            Accept: application/json

    + Body

            {
                "label_id": 67,
                 "image_state": 300,
                "source": "somexxx"
            }

+ Response 200

    + Headers

            Location: /images/1
            
    + Body

            {
                "message": "图像信息修改成功",
                "label_id": 67,
                "image_state": 300,
                "filename": "image1.png",
                "source": "somexxx"
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
                "message": "修改图像信息需要管理员权限"
            }

+ Response 404

    + Headers

            Location: /images/1
    
    + Body

            {
                "message": "图像不存在"
            }

### Delete an Image [DELETE]

+ Response 204

    + Headers

            Location: /images/1
            
    + Body

            {
                "message": "已删除图像"
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
                "message": "删除图像需要管理员权限"
            }

+ Response 404

    + Headers

            Location: /images/1
    
    + Body

            {
                "message": "图像不存在"
            }

## Images Collection [/images/{?state}]
Collection of all images.


+ Parameters
    + image_state: 301 (optional, int) - state of the image in form of a constant integer code

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
                "message":"图像集合获取成功",
                "data":
                    [
                        {
                            "image_id": 432,    
                            "label_id": 67,
                            "image_state": 300,
                            "filename": "image1.png",
                            "source": "someone"
                        }, {
                            "image_id": 332,    
                            "label_id": 67,
                            "image_state": 300,
                            "filename": "image1.png",
                            "source": "someone"
                        }, {
                            "image_id": 542,    
                            "label_id": 67,
                            "image_state": 300,
                            "filename": "image1.png",
                            "source": "someone"
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
                "filename": "image1.png",
                "source": "someone"
            }

+ Response 201

    + Headers

            Location: /images/1
            
    + Body

            {
                "message": "图像创建成功",
                "image_id": 432,    
                "label_id": 67,
                "image_state": 300,
                "filename": "image1.png",
                "source": "someone"
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

## a Single Label [/label/{label_id}]
A single label object. The label resource has the following attributes:

+ label_id
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
+ others
+ comment

The *id* is assigned by the server at the moment of creation.

+ Parameters
    + label_id: 123 (required, int) - ID of the Label in form of an integer

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
                "message": "标注获取成功",
                "quality": true,
                "dr": false,
                "stage": 0,
                "dme": 400,
                "hr": 500,
                "age_dme": 600,
                "rvo": false,
                "crao": true,
                "myopia": false,
                "od": true,
                "glaucoma": false,
                "others": true,
                "comment": "COMMENT"
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

+ Response 404

    + Headers

            Location: /images/1
    
    + Body

            {
                "message": "标注不存在"
            }

### Edit a Label [PUT]
To update a label, send a JSON with updated value for All of the label resource attributes EXCEPT id.

+ Request (application/json)

    + Headers

            Accept: application/json

    + Body

            {
                "quality": true,
                "dr": false,
                "stage": 0,
                "dme": 400,
                "hr": 500,
                "age_dme": 600,
                "rvo": false,
                "crao": true,
                "myopia": false,
                "od": true,
                "glaucoma": false,
                "others": true,
                "comment": "COMMENT"
            }

+ Response 200

    + Headers

            Last-Modified: 2018-04-17
            
    + Body

            {
                "message": "标注修改成功",
                "quality": true,
                "dr": false,
                "stage": 0,
                "dme": 400,
                "hr": 500,
                "age_dme": 600,
                "rvo": false,
                "crao": true,
                "myopia": false,
                "od": true,
                "glaucoma": false,
                "others": true,
                "comment": "COMMENT"
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

+ Response 404

    + Headers

            Location: /images/1
    
    + Body

            {
                "message": "标注不存在"
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
            
+ Response 404

    + Headers

            Location: /images/1
    
    + Body

            {
                "message": "标注不存在"
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
                "quality": true,
                "dr": false,
                "stage": 0,
                "dme": 400,
                "hr": 500,
                "age_dme": 600,
                "rvo": false,
                "crao": true,
                "myopia": false,
                "od": true,
                "glaucoma": false,
                "others": true,
                "comment": "COMMENT"
            }

+ Response 201

    + Headers

            Last-Modified: 2018-04-17
            
    + Body

            {
                "message": "标注创建成功",
                "label_id": 634,    
                "quality": true,
                "dr": false,
                "stage": 0,
                "dme": 400,
                "hr": 500,
                "age_dme": 600,
                "rvo": false,
                "crao": true,
                "myopia": false,
                "od": true,
                "glaucoma": false,
                "others": true,
                "comment": "COMMENT"
            }

+ Response 401

    + Headers

            Location: /images/1
            
    + Body

            {
                "message": "用户未登录"
            }


# Group Uploaded photo
Upload photo related resources of *The Yelda API*.

## A single uploaded photo object [/uploads/photos/{filename}]
A single photo object. A photo is a binary files

The *filename* is a full name with filename extension like "yelda.jpg".

For example, maybe you can use the label like
`<img src="http://ourhost:8080/api/uploads/photos/admin.jpg" />`

to get the photo file

+ Parameters
    + filename: abc.png (required, string) - Name of the file in form of a string

### Retrieve a Single uploaded photo [GET]

+ Request (application/json)

    + Headers

            Accept: application/json
            
    + Body

            {
            }

+ Response 200

    + Body

            raw data

+ Response 401

    + Headers

            Last-Modified: 2018-04-17

    + Body

            {
                "message": "用户未登录"
            }

## Uploaded photo Collection [/uploads/photos/]

### Upload a photo [POST]
To upload a new photo. This action is `login-required`.

+ Request (application/json)

    + Headers
    
            
    + Body

            {
                raw_data
            }

+ Response 201

    + Headers


    + Body

            {
                "message": "头像上传成功"
            }
            
+ Response 400

    + Headers


    + Body

            {
                "message": "头像上传失败"
            }


# Group Uploaded medical image
Upload medical image related resources of *The Yelda API*.

## A single uploaded medical image object [/uploads/medical-images/{filename}]
A single medical image object.

The *filename* is a full name with filename extension like "yelda.jpg".

For example, maybe you can use the label like
`<img src="http://ourhost:8080/api/uploads/medical-images/xxx.jpg" />`

to get the medical image file

+ Parameters
    + filename: abc.png (required, string) - Name of the file in form of a string

### Retrieve a Single uploaded medical image [GET]

+ Request (application/json)

    + Headers

            Accept: application/json
            
    + Body

            {
            }

+ Response 200

    + Body

            raw data

+ Response 401

    + Headers

            Last-Modified: 2018-04-17

    + Body

            {
                "message": "用户未登录"
            }


## Uploaded medical images Collection [/uploads/medical-images/]

### Upload a medical image [POST]
To upload a new medicalimage. This action is `login-required`.

+ Request (application/json)

    + Headers
    
            
    + Body

            {
                raw_data
            }

+ Response 201

    + Headers


    + Body

            {
                "message": "医学影像上传成功"
            }
            
+ Response 400

    + Headers


    + Body

            {
                "message": "医学影像上传失败"
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

### 黄斑水肿 4XX

| code |    name    | description |
| :--: | :--------: | :---------: |
| 400  | 没有黄斑水肿 |             |
| 401  |  糖尿病黄斑水肿   |             |
| 402  | 非糖尿病黄斑水肿  |             |

### 高血压视网膜病变 5XX

| code |    name    | description |
| :--: | :--------: | :---------: |
| 500  | 不患有高血压视网膜病变 |             |
| 501  |  轻度高血压视网膜病变   |             |
| 502  | 中度高血压视网膜病变  |             |
| 503  | 重度高血压视网膜病变  |             |

### 年龄相关性黄斑变性 6XX

| code |    name    | description |
| :--: | :--------: | :---------: |
| 600  | 不患有年龄相关性黄斑变性 |             |
| 601  |  早期年龄相关性黄斑变性   |             |
| 602  | 中期年龄相关性黄斑变性  |             |
| 603  | 晚期年龄相关性黄斑变性  |             |

