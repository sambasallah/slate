---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

search: true
---

# Register

This endpoint registers a new user and returns an API key.

> Request

```shell
curl -X POST "https://seifchain.seif.io/users"
  -H "Content-Type: application/json"
  -d {"username": "testUser", "orgname": "mainnet"}
```

Only "username" field can be changed. "orgname" field can also be changed if the API is being called from and to own blockchain node.

It registers all users in msp and save the keys in server. When the user uses the token, in the backend the key is loaded and transactions are made

> Response

```json
{
    "success": true,
    "apiKey": "blahCvHLblahrB",
    "message": "testUser enrolled Successfully"
}
```


### HTTP Request

`POST https://seifchain.seif.io/users`

### Body Parameters

Parameter |  Description
--------- | -----------
username | The username.
orgname | The organization name.

<aside class="success">
  Save the APIKEY - It cannot be requested again through API - unless contact us
</aside>

# Authentication

This endpoint gets the access token.

> Request

```shell
curl -X POST "https://seifchain.seif.io/getToken"
  -H "Content-Type: application/json"
  -d {"username": "testUser", "apiKey": "HlWKyuLDQcRu"}
```

> Response

```json
{
    "success": true,
    "message": "Authorization successful",
    "token": "eyJhbGciOiJIUzI1Ni..."
}
```

### HTTP Request

`POST https://seifchain.seif.io/getToken`

### Body Parameters

Parameter |  Description
--------- | -----------
username | The username.
apiKey | The API key geneated during registration.

# Insert User Data

This endpoint inserts user data.

> Request

```shell
curl -X "POST https://seifchain.seif.io/channels/common/chaincodes/reference"
  -H "Content-Type: application/json"
  -H "Authorization: Bearer TOKEN"
  -d {"peers": ["mainnet/peer0"], "fcn": "addAPIUser", "args": ["John","Doe","Male","Doctor","0178787878","user@gmail.com","CURP-123123123123","Ine-23423423423","UniqueID0001"]}
```


> Response

```json
{
    "transaction": "520e09ee53e08e4da4cf400e35797faf3c6970984b6ae1fafe046181e34fdc9e"
}
```

> Error

```json
{
    "ok": false,
    "message": "[ERROR] user already exists, cannot create two accounts with same ID <UniqueID0001>"
}
```

### HTTP Request

`POST https://seifchain.seif.io/channels/common/chaincodes/reference`

### Body Parameters

Parameter |  Description
--------- | -----------
peers | The organization name/peer id .
fcn | The function to call. This "addAPIUser" is being called. The fucntion name is self explainatory.
args | The actual data as an array containing 9 values of type - string.

The values of `args` should be inserted in the following order:

1. First Name
2. Last Name
3. Gender
4. Occupation
5. Phone/Mobile number
6. Email
7. CURP number
8. Ine number
9. Unique ID of the user to save, update data and lets query the history of user data

<aside class="info">
  All fields are required
</aside>

Transaction ID is returned and User's Global Blockchain ID and Organization Name are automatically populated


<aside class="warning">
  Retruns error if user is created with the Unique ID already
</aside>


# Query User History

This endpoint returns the full history of the user data manipulation

> Request

```shell
curl -X GET "https://seifchain.seif.io/channels/common/chaincodes/reference/?peer=mainnet/peer0&fcn=getPreviousValue&args=['UniqueID0001']"
  -H "Content-Type: application/json"
  -H "Authorization: Bearer TOKEN"
```


> Response

```json
{
    "result": [
        {
            "TxId": "520e09ee53e08e4da4cf400e35797faf3c6970984b6ae1fafe046181e34fdc9e",
            "Value": {
                "id": "testerID0001",
                "firstname": "Tester",
                "lastname": "User",
                "gender": "Male",
                "occupation": "Doctor",
                "phone": "0178787878",
                "email": "user@gmail.com",
                "curp": "CURP-123123123123",
                "ine": "Ine-23423423423",
                "blockchainid": "eDUwOTo6Q049a2VuZGksT1U9Y2xpZW50K09VPW1haW5uZXQrT1U9bWFpbm5ldDo6Q049Y2EubWFpbm5ldC5zZWlmLmlvLE89bWFpbm5ldC5zZWlmLmlvLEw9U2FuIEZyYW5jaXNjbyxTVD1DYWxpZm9ybmlhLEM9VVM=",
                "mspid": "mainnetMSP"
            },
            "Timestamp": "2019-10-15 20:00:01.14 +0000 UTC",
            "IsDelete": "false"
        }
    ]
}
```

> Error

```json
{
    "ok": false,
    "message": "[ERROR] Unauthorized Attempt."
}
```


### HTTP Request

`https://seifchain.seif.io/channels/common/chaincodes/reference/?peer=mainnet/peer0&fcn=getPreviousValue&args=['UniqueID0001']"`

### Query Parameters

Parameter | Description
--------- | -----------
peer | The organization name/peer id.
fcn | The function to call. This time "getPreviousValue" is being called. The fucntion returns the full history of the user data manipulation.
args | The actual data as an array containing 1 value of type - string. The value is the Unique ID that was used to save or update data.


<aside class="warning">Retruns error if the unique ID is not same as the creator user. Different token for the same user works fine.</aside>
