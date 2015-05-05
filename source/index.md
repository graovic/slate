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


Request Type: <b> POST </b> </br>
URL: /u/registration 

| Field    | Data type  | additional requirements | 
|----------|------------|-------------------------|
| username | string     | - |
| email    | string     | valid email address | 
| createAt

<pre>
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

URL: /auth/login/simple <br>
Method: POST

<pre>
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


<pre>
GET /people/<:userId>
</pre>

<pre>
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
    "aboutme": "testing editing"
  }
}

</pre>

# Update user information


PUT /users/<:userId>
Authorization: "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE0MzAzMjY2NzQsImdyb3VwcyI6bnVsbCwidXNlcklkIjoiNTUzNGZiN2MzZWEyNGNkNzQ5MDAwMDAxIiwidXNlcm5hbWUiOiJ0ZXN0MSJ9.NeP7hForLtZttrSMWccHjSBFwUCepltJIhoT8q16L91YdePralWhWyAkTcKvsEraK9DOtiw3B5CR4kxvYnWupVexqUH-SeRn6-p33fEi8l5v4C3G44a109TpA90qxO-CxLeHUbwIFJDLEcr_SkZokJAIiSTvqcdwFFeuVNycy30"

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

# Facebook integration

Facebook integration is performed using client side facebook api. 
On the website, user is logging into facebook using javascript jdk; after successfully login, callback function from javascript sends facebook access token and fbuserid to gamehub api.

POST /fbapi
{
  "fbuser": "facebookuserid",
  "access_token": "fb access token"
}

Response from server:

<pre>
{
"hmac": "J_s0VI3BAzLEHi8RCP00BcmWQ3OO5ynRFedG2fafQWU=",
"msg": "success",
"token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE0MzAzMjY2NzQsImdyb3VwcyI6bnVsbCwidXNlcklkIjoiNTUzNGZiN2MzZWEyNGNkNzQ5MDAwMDAxIiwidXNlcm5hbWUiOiJ0ZXN0MSJ9.NeP7hForLtZttrSMWccHjSBFwUCepltJIhoT8q16L91YdePralWhWyAkTcKvsEraK9DOtiw3B5CR4kxvYnWupVexqUH-SeRn6-p33fEi8l5v4C3G44a109TpA90qxO-CxLeHUbwIFJDLEcr_SkZokJAIiSTvqcdwFFeuVNycy30",
"userid": "5534fb7c3ea24cd749000001",
"username": "facebook username" 
}
</pre>



### Password management

####Password change:

POST /{userid}/pwdchange
{
  "oldpassword": "users old password",
  "password1": "first new password",
  "password2": "second new password"
}

If request is successful, user password will be changed.

####Password reset:

If users forget their password, they can send password change request to the url:

GET /pwdreset/<user_email_address>

After user submits the data, system will send them token for password resetting via email.

POST /pwdresetupdate
{
  "pwdtoken": "password token",
  "password1": "password1",
  "password2": "password2"  
}

In this request, password token is the token received by email, password1 and password2 are new passwords.

