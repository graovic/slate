---
title: Users API

language_tabs:
  - shell


toc_footers:
  - <a href='#'>GameHub API documentation</a>

includes:
  - errors

search: true
---

# Users management and authentication:

##End user registration:

Server: 54.69.78.153:8080
Request Type: <b> POST </b> </br>
URL: /u/registration 

| Field    | Data type  | additional requirements | 
|----------|------------|-------------------------|
| username | string     | - |
| email    | string     | valid email address | 
| createAt

<pre>
Server: 54.69.78.153:8080
URL : /u/registration 
Application-Type: "application/json"
POST: 
{
  "username":"uniqueusername",
  "firstname": "Ashley",
  "lastname": "Cline",
  "password1": "simpletestpassword",
  "password2": "simpletestpassword",
  "gender":"female",
  "year": 1972,
  "day": 11,
  "month": 2,
  "address": "7805 Southcrest Parkway"
  "country": "USA",
  "email": "Ashley.Cline@gmail.com"
  "aboutme": "info about user",
  "headline" : "more information"
}
</pre>

| Field     | Data type  |  Additional requirements | 
|-----------|------------|--------------------------|
| username  | string     |  unique  | 
| firstname  |  string | -   |  
| lastname  |  string  | -   |
| password1 | string integer special character | same as password2 |
| password2 |   string integer special character | same as password1|
| gender |  string | "male"|"female" |
| year  | integer | 1900 > y < 2005, valid day, month, year |
| day  | integer | 1 >=  d <= 31| 
| month | integer | 1 >= m <= 12 |
| address | string | - |
| country | string | One of the countries from country list http://www.textfixer.com/resources/dropdowns/country-dropdown-iso-html.txt |
| email | string | valid email address, this mail address will be used for sending password reset mail and registration confirmation token |
| aboutme | string | info about user used in profile page | 
| headline | string |  - |


> Response:

201 - if request is successful, mail is sent to the user with token id,
 
400 - if some of the requirements are not satisfied

User will receive email with token id
<pre>
Registration token /registration/confirm/<:tokeid>
</pre>


In final version, the content of this mail will be changed to point to the real website, that will, in background, send tokenid to the api. In case of mobile client, clients can send GET request directly to the API server.

Token is valid in next 24 hours. If user doesn't provide token confirmation in that period, registration request expires; user data will be removed from database, and token become invalid. After that period user needs to register again.


When server receives tokenId in valid time frame, server will enable that user. After that user can login on api.


If user doesn't receive email address from the system, user can request resending token, by sending request on 

<pre>
/registration/resend/<:email>
</pre>

If email address belongs to user which isn't enabled, mail service will send them mail with registration token.

If user requests multiple time url 

<pre>
/registration/confirm/<:tokenid>
</pre>

user will receive notification that this token is already verified.


# User authentication
## User password authentication


Endpoint

Server: 54.69.78.153:8080
URL: /auth/login/simple <br>
Method: POST

<pre>
Server: 54.69.78.153:8080
URL : /auth/login/simple
Application-Type: "application/json"
POST: 
{
  "username": "test1",
  "password": "testpassword"
}
</pre>

Server Response if user/password combination is correct

<pre>

{
"hmac": "J_s0VI3BAzLEHi8RCP00BcmWQ3OO5ynRFedF4fIfQWU=",
"msg": "success",
"token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE0MzAzMjY2NzQsImdyb3VwcyI6bnVsbCwidXNlcklkIjoiNTUzNGZiN2MzZWEyNGNkNzQ5MDAwMDAxIiwidXNlcm5hbWUiOiJ0ZXN0MSJ9.NeP7hForLtZttrSMWccHjSBFwUCepltJIhoT8q16L91YdePralWhWyAkTcKvsEraK9DOtiw3B5CR4kxvYnWupVexqUH-SeRn6-p33fEi8l5v4C3G44a109TpA90qxO-CxLeHUbwIFJDLEcr_SkZokJAIiSTvqcdwFFeuVNycy30",
"userid": "5534fb7c3ea24cd749000001",
"username": "test1" 
}
</pre>


| Field     | Description                                               |
| ----------| ----------------------------------------------------------|
| hmac      | Used for shoutz to send hmac token through url parameters |
| msg       | sucesss                                                   |
| token     | jwt token, (http://jwt.io) you can check token content    |
| userid    | unique user id                                            |
| username  | username                                                  |


For every other request, user needs to send token data in http Authorization header

<pre>
Authorization: eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE0MzAzMjY2NzQsImdyb3VwcyI6bnVsbCwidXNlcklkIjoiNTUzNGZiN2MzZWEyNGNkNzQ5MDAwMDAxIiwidXNlcm5hbWUiOiJ0ZXN0MSJ9.NeP7hForLtZttrSMWccHjSBFwUCepltJIhoT8q16L91YdePralWhWyAkTcKvsEraK9DOtiw3B5CR4kxvYnWupVexqUH-SeRn6-p33fEi8l5v4C3G44a109TpA90qxO-CxLeHUbwIFJDLEcr_SkZokJAIiSTvqcdwFFeuVNycy30
</pre>

# Get user profile information

Server: 54.69.78.153:8080
GET /v1/users/<:userId>


```shell
curl -X GET http://apiserver.local:8080/v1/users/55341b418716380451000003
```

```json
{
  "ID": "55341b418716380451000003",
  "username": "test1",
  "email": "test2@fincore.com",
  "_created": "0001-01-01T00:00:00Z",
  "_updated": "0001-01-01T00:00:00Z",
  "acl": 0,
  "fbuser": false,
  "fbuserid": "",
  "usertype": 0,
  "tickets": 100,
  "coins": 0,
  "oldslingo": false,
  "enabled": true,
  "resettoken": "",
  "profile": {
    "firstname": "",
    "lastname": "",
    "fbavatar": "",
    "lavatar": "",
    "gender": "male",
    "year": 2000,
    "month": 1,
    "day": 1,
    "country": "",
    "address": "test address",
    "headline": "",
    "aboutme": "test data"
  }
}
```


# Update user information

Server: 54.69.78.153:8080
PUT /users/<:userId>


```shell

PUT /v1/users/55341b418716380451000003 HTTP/1.1
Host: 54.69.78.153:8080
Authorization: eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE0MzEwMTI5MDYsInVzZXJJZCI6IjU1MmVlN2FiM2VhMjRjZmNlYzAwMDAwMSIsInVzZXJuYW1lIjoiZ3Jhb3ZpYzEzMzQ1IiwidXNlcnR5cGUiOjB9.BhcBY1PwyF2bZoxHE3YqfXEb_GTvrHeToSLRz9zDrnKaWv4azWNiK1f28JKq0uXLStqZp53qdZrxV2gSBWgxeZICW94XYbYcyTR73gmkyIvEXzogAryXxZpQzuOWnLUAdGHDcQDN2Ui-h5_irfCWmBaAwNS1maALoimU3US5WKQ
Content-Type: application/json
Cache-Control: no-cache

{
"email": "test2@fincore.com",
"profile": {
    "firstname": "",
    "lastname": "",
    "gender": "male",
    "year": 2000,
    "month": 1,
    "day": 1,
    "country": "",
    "address": "test address",
    "headline": "",
    "aboutme": "testing editing"
  }
}
```

# Facebook integration

Facebook integration is performed using client side facebook api. 
On the website, user is logging into facebook using javascript jdk; after successfully login, callback function from javascript sends facebook access token and fbuserid to gamehub api.

Server: 54.69.78.153:8080
POST /fbapi

```shell
POST /fbapi HTTP/1.1
Host: 54.69.78.153:8080
{
  "fbuser": "facebookuserid",
  "access_token": "fb access token"
}

```

>Response from server:

```json
{
"hmac": "J_s0VI3BAzLEHi8RCP00BcmWQ3OO5ynRFedG2fafQWU=",
"msg": "success",
"token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE0MzAzMjY2NzQsImdyb3VwcyI6bnVsbCwidXNlcklkIjoiNTUzNGZiN2MzZWEyNGNkNzQ5MDAwMDAxIiwidXNlcm5hbWUiOiJ0ZXN0MSJ9.NeP7hForLtZttrSMWccHjSBFwUCepltJIhoT8q16L91YdePralWhWyAkTcKvsEraK9DOtiw3B5CR4kxvYnWupVexqUH-SeRn6-p33fEi8l5v4C3G44a109TpA90qxO-CxLeHUbwIFJDLEcr_SkZokJAIiSTvqcdwFFeuVNycy30",
"userid": "5534fb7c3ea24cd749000001",
"username": "facebook username" 
}
```



# Password management

## Password change:

User can change your own password

Server: 54.69.78.153:8080
POST /v1/users/{userid}/pwdchange

```shell


POST /v1/users/55341b418716380451000003/pwdchange HTTP/1.1
Host: 54.69.78.153:8080
Authorization: eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE0MzEwMTI5MDYsInVzZXJJZCI6IjU1MmVlN2FiM2VhMjRjZmNlYzAwMDAwMSIsInVzZXJuYW1lIjoiZ3Jhb3ZpYzEzMzQ1IiwidXNlcnR5cGUiOjB9.BhcBY1PwyF2bZoxHE3YqfXEb_GTvrHeToSLRz9zDrnKaWv4azWNiK1f28JKq0uXLStqZp53qdZrxV2gSBWgxeZICW94XYbYcyTR73gmkyIvEXzogAryXxZpQzuOWnLUAdGHDcQDN2Ui-h5_irfCWmBaAwNS1maALoimU3US5WKQ
{
  "oldpassword": "users old password",
  "password1": "first new password",
  "password2": "second new password"
}
```

If request is successful, user password will be changed.


## Password reset:


If users forget their password, they can send password change request to the url:

Server: 54.69.78.153:8080
GET /pwdreset/<user_email_address>

```shell
GET /pwdreset/testmail@testmaildomain.com
```

>response from server

```json
{"msg":"ok"}
```

After user submit request, system will send them token for password resetting via email. User needs to submit password reset token + new passwords.

In this request, password token is the token received by email, password1 and password2 are new passwords.

Server: 54.69.78.153:8080

```shell

POST /pwdresetupdate
{
  "pwdtoken": "password token",
  "password1": "password1",
  "password2": "password2"  
}
```
slate


## Friend requests:

```shell

POST /friendrequest/sent/{userid}
{}

```

## Get all friend request:

```shell
GET /friendrequest/
```


## Delete friend requests:

```shell
DELETE /friendrequest/{requestid}
```

## Confirm friend requests:

```shell
GET friendsrequest/confirm/{requestid}
```


