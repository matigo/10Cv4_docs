# `/invite` - Invitations

### Summary

As of this writing, 10Centuries is an invitation-only platform. For this reason, people need a way to create invitation codes that they can share with others. This document will outline how to interact with the `/invite` API endpoint.

> **Note:** Any API request that can create or modify data must have a valid authorization token in the HTTP header.

### Sections

1. Creating Invitation Codes
2. Retrieving Invitation Codes
3. Retrieving Invitation Limits
4. Verifying an Invitation Code

### Creating Invitation Codes

People can have a maximum of 10 unredeemed invitations available at any time, and people can create as many as 10 invitation codes per day. To create new invitation codes, send a request to the following endpoint:

```
POST https://api.10centuries.org/invite/create
```

So long as the logged-in account has invitation codes remaining, new codes will be created and the account's invitation code summary will be returned.

##### Example

```
curl -X POST -H "Authorization: {Authorization Token}" \
-H "Content-Type: application/x-www-form-urlencoded" \
"https://api.10centuries.org/invite/create"
```

If the post was successful, the API will respond with a JSON package similar to this:

```
{
    "meta": {
        "code": 200,
        "server": "56.101"
    },
    "data": [
        {
            "guid": "9e7233b75488b7c50075a792cc11c53950a8341bc4cd8d0d7a9744a3ed9aa000",
            "created_by": {
                "id": 1
            },
            "created_at": "2017-03-14T12:52:06Z",
            "created_unix": 1489495926,
            "redeemed_by": {
                "id": 173
            },
            "redeemed_at": "2017-03-14T14:37:42Z",
            "redeemed_unix": 1489502262
        },
        {
            "guid": "6fd10251e5359c86aef577b17abf03a81dbed3a467ef49ce135f60c76a43f01b",
            "created_by": {
                "id": 1
            },
            "created_at": "2017-03-13T08:14:40Z",
            "created_unix": 1489392880,
            "redeemed_by": false,
            "redeemed_at": false,
            "redeemed_unix": false
        }
    ]
}
```

There is no limit to the number of invitation codes that can be returned in this object. Invitation codes that have already been redeemed will include the id of the account that was created as well as the redemption time.

If no more invitation codes can be created for the account, the following message will be returned:

```
{
    "meta": {
        "code": 400,
        "text": "You Have No More Invitation Codes For Today"
    },
    "data": false
}
```

### Retrieving Invitation Codes

Retrieving a list of invitation codes can be done with a simple, authenticated `GET` request to the following endpoint:

```
GET https://api.10centuries.org/invite
```

So long as the logged-in account has invitation codes, they will be returned.

##### Example

```
curl -X GET -H "Authorization: {Authorization Token}" \
-H "Content-Type: application/x-www-form-urlencoded" \
"https://api.10centuries.org/invite"
```

If the post was successful, the API will respond with a JSON package similar to this:

```
{
    "meta": {
        "code": 200,
        "server": "56.101"
    },
    "data": [
        {
            "guid": "9e7233b75488b7c50075a792cc11c53950a8341bc4cd8d0d7a9744a3ed9aa000",
            "created_by": {
                "id": 1
            },
            "created_at": "2017-03-14T12:52:06Z",
            "created_unix": 1489495926,
            "redeemed_by": {
                "id": 173
            },
            "redeemed_at": "2017-03-14T14:37:42Z",
            "redeemed_unix": 1489502262
        },
        {
            "guid": "6fd10251e5359c86aef577b17abf03a81dbed3a467ef49ce135f60c76a43f01b",
            "created_by": {
                "id": 1
            },
            "created_at": "2017-03-13T08:14:40Z",
            "created_unix": 1489392880,
            "redeemed_by": false,
            "redeemed_at": false,
            "redeemed_unix": false
        }
    ]
}
```

If the account has not yet created any invitation codes, the `data` object will be an empty array.

### Retrieving Invitation Limits

Retrieving the number of invitation codes an account can create is done with an authenticated `GET` request to the following endpoint:

```
GET https://api.10centuries.org/invite/remain
```

##### Example

```
curl -X GET -H "Authorization: {Authorization Token}" \
-H "Content-Type: application/x-www-form-urlencoded" \
"https://api.10centuries.org/invite/remain"
```

If the post was successful, the API will respond with a JSON package similar to this:

```
{
    "meta": {
        "code": 200,
        "server": "56.101"
    },
    "data": {
        "remain": 2
    }
}
```

Regardless of whether an account has created invitation codes or not, a JSON response body will be returned.

### Verifying an Invitation Code

Verification of whether a single invitation code is valid or not can be done with an authenticated or unauthenticated `GET` request to the following endpoint:

```
GET https://api.10centuries.org/invite/verify
```

##### Required Variables

```
invite_code: {invitation code}
```

##### Example

```
curl -X GET -H "Content-Type: application/x-www-form-urlencoded" \
-d "invite_code=9e7233b75488b7c50075a792cc11c53950a8341bc4cd8d0d7a9744a3ed9aa000" \
"https://api.10centuries.org/invite/verify"
```

The API will then respoind with a JSON package similar to this:

```
{
    "meta": {
        "code": 200,
        "server": "56.101"
    },
    "data": {
        "is_valid": true
    }
}
```

If the invitation code has not yet been redeemed, the `is_valid` value will be true. If the invitation code has been redeemed the value will be false.

Should the invitation code be unrecognised, the following JSON response will be returned:

```
{
    "meta": {
        "code": 400,
        "text": "Invalid Invitation Code"
    },
    "data": false
}
```