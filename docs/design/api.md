***Notice:***

**For MUCH better reading experience, please read the document at [theyelda.docs.apiary.io](https://theyelda.docs.apiary.io/#reference).**

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

    + Headers

            Location: /accounts/1

    + Body

            {
                "message": "登录成功",
                "account_id": 123,
                "authority_id": 1
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

# Group Self
Myself-related resources of *The Yelda API*.

## a Single Self [/self]
A single account object. The account resource has the following attributes:

+ account_id

### Retrieve a Single Oneself [GET]

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
                "message": "账户获取成功",
                "account_id": 364,
                "username": "yelda",
                "nickname": "Nick",
                "email": "yelda@mail.com",
                "photo": "photo1",
                "authority": "admin"
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
                "authority": 1
            }

+ Response 200

    + Headers

            Last-Modified: 2018-04-17
            
    + Body

            {
                "message": "账户编辑成功",
                "username": "yelda",
                "nickname": "Nick",
                "email": "yelda@mail.com",
                "photo": "photo1",
                "authority": 1
            }


### Delete an Account [DELETE]

+ Response 204

    + Body

            {
                "message": "账户删除成功"
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
                "message": "账户集合获取成功",
                "data":
                        [
                            {
                                "id": 12,
                                "username": "yelda",
                                "nickname": "Nick",
                                "email": "yelda@mail.com",
                                "photo": "photo1",
                                "authority": "admin"
                            }, {
                                "id": 23,
                                "username": "yelda2",
                                "nickname": "Nick2",
                                "email": "yelda2@mail.com",
                                "photo": "photo2",
                                "authority": "admin"
                            }, {
                                "id": 34,
                                "username": "yelda3",
                                "nickname": "Nick2",
                                "email": "yelda3@mail.com",
                                "photo": "photo3",
                                "authority": "admin"
                            }
                        ]
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
                "message": "注册成功"
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
                "message": "",
                "id": "1",    
                "image_id": "123",
                "doctor_id": "1234",
                "label_id": "12345",
                "state": "unlabeled",
                "finished_date": "2018-04-17"
            }

### Edit a Job [PUT]
To update a job, send a JSON with updated value for All of the job resource attributes EXCEPT id.

+ Request (application/json)

    + Headers

            Accept: application/json

    + Body

            {
                "image_id": "123",
                "doctor_id": "1234",
                "label_id": "12345",
                "state": "unlabeled",
                "finished_date": "2018-04-17"
            }

+ Response 200

    + Headers

            Location: /jobs/1
            
    + Body

            {
                "message": "",
                "id": "1",
                "image_id": "123",
                "doctor_id": "1234",
                "label_id": "12345",
                "state": "unlabeled",
                "finished_date": "2018-04-17"
            }


### Delete a Job [DELETE]

+ Response 204

## Jobs Collection [/jobs/{?image_id}{?doctor_id}{?state}]
Collection of all jobs.

+ Parameters
    + doctor_id: 1 (optional, int) - ID of the doctor who the job is assigned to in form of an integer

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
                "message":"",
                "data":
                    [
                        {
                            "id": "1",
                            "image_id": "123",
                            "doctor_id": "1234",
                            "label_id": "12345",
                            "state": "unlabeled",
                            "finished_date": "2018-04-17"
                        }, {
                            "id": "2",
                            "image_id": "234",
                            "doctor_id": "2345",
                            "label_id": "23456",
                            "state": "unlabeled",
                            "finished_date": "2018-04-17"
                        }, {
                            "id": "3",
                            "image_id": "345",
                            "doctor_id": "3456",
                            "label_id": "34567",
                            "state": "unlabeled",
                            "finished_date": "2018-04-17"
                        }
                ]
            }

### Create a Job [POST]
To create a new job simply with a JSON. This action requires an `authority` of `admin` .

+ Request (application/json)

    + Headers

            Accept: application/json
            
    + Body

            {
                "image_id": "123",
                "doctor_id": "1234"
            }

+ Response 201

    + Headers

            Location: /jobs/1
            
    + Body

            {
                "message": "",
                "id": "1",
                "image_id": "123",
                "doctor_id": "1234",
                "state": "unlabeled",
                "finished_date": "2018-04-17"
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


### Delete an Image [DELETE]

+ Response 204

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
                "message": "",
                "id": "1",    
                "quality": "",
                "dr": "",
                "stage": "",
                "dme": "",
                "hr": "",
                "agedme": "",
                "rvo": "",
                "crao": "",
                "myopia": "",
                "od": "",
                "glaucoma": "",
                "comment": ""
            }

### Edit a Label [PUT]
To update a label, send a JSON with updated value for All of the label resource attributes EXCEPT id.

+ Request (application/json)

    + Headers

            Accept: application/json

    + Body

            {
                "quality": "",
                "dr": "",
                "stage": "",
                "dme": "",
                "hr": "",
                "agedme": "",
                "rvo": "",
                "crao": "",
                "myopia": "",
                "od": "",
                "glaucoma": "",
                "comment": ""
            }

+ Response 200

    + Headers

            Last-Modified: 2018-04-17
            
    + Body

            {
                "message": "",
                "id": "1",    
                "quality": "",
                "dr": "",
                "stage": "",
                "dme": "",
                "hr": "",
                "agedme": "",
                "rvo": "",
                "crao": "",
                "myopia": "",
                "od": "",
                "glaucoma": "",
                "comment": ""
            }


### Delete an Label [DELETE]

+ Response 204

## Labels Collection [/labels]
Collection of all labels.

### Create a Label [POST]
To create a new image simply with a JSON. This action requires an `authority` of `admin` .

+ Request (application/json)

    + Headers

            Accept: application/json
            
    + Body

            {
                "id": "1",    
                "quality": "",
                "dr": "",
                "stage": "",
                "dme": "",
                "hr": "",
                "agedme": "",
                "rvo": "",
                "crao": "",
                "myopia": "",
                "od": "",
                "glaucoma": "",
                "comment": ""
            }

+ Response 201

    + Headers

            Last-Modified: 2018-04-17
            
    + Body

            {
                "message": "",
                "id": "1",    
                "quality": "",
                "dr": "",
                "stage": "",
                "dme": "",
                "hr": "",
                "agedme": "",
                "rvo": "",
                "crao": "",
                "myopia": "",
                "od": "",
                "glaucoma": "",
                "comment": ""
            }

## Codes

### Authority 1XX

| code |  name  | description |
| :--: | :----: | :---------: |
| 100  |  Empty |             |
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

