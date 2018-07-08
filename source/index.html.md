---
title: Plannery API v1

language_tabs:
  - shell

toc_footers:
 - <a href='http://github.com/tripit/slate'>Documentation Powered by Slate</a>
---

# Introduction

This is the API for the Plannery app. It provides methods to create and update user accounts, add linked plaid accounts and return financial plan data.

JSON is the only supported request and response format.

# Authentication

```shell
# With curl, supply the credentials with the -u option
curl "https://prod.planneryapp.com/api/items"
  -u "todd@packer.com:auth-token"
```

Some API methods require an authentication token, which is returned from the login and register methods. Tokens should be considered valid indefinitely, but can expire at any time.

The API expects credentials via HTTP Basic Authentication, with the user's email address as the username and the authentication token as the password.

# Versioning

```shell
Accept: application/vnd.plannery.v1+json
```

Use the Accept HTTP header to indicate the requested version of the API. At this time, the api will default to the most recent version. You should always specify the requested version.

# Accounts

## Create a new user account

```shell
# EXAMPLE REQUEST

{
  "first_name": "Todd",
  "last_name": "Packer",
  "email": "todd@packer.com",
  "password": "letmein",
  "phone_number": "555-555-1234",
}
```

```shell
# EXAMPLE RESPONSE

{
  "id": 1,
  "authentication_token": "abcd1234",
  "first_name": "Todd",
  "last_name": "Packer",
  "email": "todd@packer.com",
  "phone_number": "555-555-1234"
}
```

This endpoint creates a new user account.

### HTTP Request

`POST https://prod.planneryapp.com/api/user`

### Request Parameters

Parameter | Required | Description
--------- | -------- | -----------
email     | true | The user's email address.
password | true | The user's password.
first_name | true | The user's first name.
last_name | true | The user's last name.
phone_number | true | The user's phone number. Should be nine digits. Formatting is stripped when saved.

## Login to existing user account

```shell
# EXAMPLE REQUEST

{
  "email": "todd@packer.com",
  "password": "letmein"
}
```

```shell
# EXAMPLE RESPONSE

{
  "id": 1,
  "authentication_token": "abcd1234",
  "first_name": "Todd",
  "last_name": "Packer",
  "email": "todd@packer.com",
  "phone_number": "555-555-1234",
}
```

This endpoint logs in to an existing user account. Each login will return a new authentication token, and the old authentication token will be invalidated.

### HTTP Request

`POST https://prod.planneryapp.com/api/user/login`

### Request Parameters

Parameter | Required | Description
--------- | -------- | -----------
email     | true | The user's email address.
password | true | The user's password.

