# JWT Kong Plugin

## Enable Plugin on Service

*REST Client vscode plugin*
```
POST http://localhost:8001/services/hello-world-service/plugins
Content-Type: application/x-www-form-urlencoded

name=jwt&config.secret_is_base64=false&config.key_claim_name=kid&config.claims_to_verify=exp&config.run_on_preflight=true
```

*CURL*
```
$ curl -X POST http://localhost:8001/services/hello-world-service/plugins \
    --data "name=jwt"  \
    --data "config.secret_is_base64=false" \
    --data "config.key_claim_name=kid" \
    --data "config.claims_to_verify=exp" \
    --data "config.run_on_preflight=true"
```

*Response*
```
HTTP/1.1 201 Created
Date: Sat, 29 May 2021 20:26:17 GMT
Content-Type: application/json; charset=utf-8
Connection: close
Access-Control-Allow-Origin: *
Content-Length: 465
X-Kong-Admin-Latency: 28
Server: kong/2.4.1

{
  "service": {
    "id": "492e4da7-729b-4b8a-9bd5-ce38fc260bdb"
  },
  "config": {
    "run_on_preflight": true,
    "anonymous": null,
    "secret_is_base64": false,
    "uri_param_names": [
      "jwt"
    ],
    "cookie_names": [],
    "key_claim_name": "kid",
    "header_names": [
      "authorization"
    ],
    "claims_to_verify": [
      "exp"
    ],
    "maximum_expiration": 0
  },
  "created_at": 1622319977,
  "tags": null,
  "route": null,
  "enabled": true,
  "id": "98804b91-7211-4b16-9dbb-017a9e5c9ed5",
  "consumer": null,
  "protocols": [
    "grpc",
    "grpcs",
    "http",
    "https"
  ],
  "name": "jwt"
}
```

## Create a Consumer

A consumer is associated with each user.

```
$ curl http://localhost:8001/consumers/ \
    --data "username=admin" \
    --data "custom_id=abcdefghij" \
    --data "tags=user-level&tags=low-priority" 
```

*Response*
```
HTTP/1.1 201 Created
Date: Sun, 30 May 2021 21:27:52 GMT
Content-Type: application/json; charset=utf-8
Connection: close
Access-Control-Allow-Origin: *
Content-Length: 150
X-Kong-Admin-Latency: 140
Server: kong/2.4.1

{
  "username": "admin",
  "custom_id": "abcdefghij",
  "id": "92b6b238-5f08-43ef-9a83-cabed2ebf75d",
  "tags": [
    "user-level",
    "low-priority"
  ],
  "created_at": 1622410072
}
```

## Create JWT Credential

```bash
$ curl -X POST http://localhost:8001/consumers/admin/jwt -H "Content-Type: application/x-www-form-urlencoded"
```

*Response*
```console
HTTP/1.1 201 Created
Date: Sun, 30 May 2021 21:34:11 GMT
Content-Type: application/json; charset=utf-8
Connection: close
Access-Control-Allow-Origin: *
Content-Length: 265
X-Kong-Admin-Latency: 28
Server: kong/2.4.1

{
  "algorithm": "HS256",
  "created_at": 1622410451,
  "tags": null,
  "id": "fc09cf08-f1ac-4745-8451-a9589ac06be4",
  "key": "vjnDyKWSocxovZbaBZquxSUvPikuUZ6F",
  "secret": "JF1DjgWtSCtn9Xr9pjM9Yg61CsNPohtv",
  "rsa_public_key": null,
  "consumer": {
    "id": "92b6b238-5f08-43ef-9a83-cabed2ebf75d"
  }
}
```

### Encode the JWT

Access the [jwt.io](http://jwt.io) and fill the *Header* and *Payload* sections as follow:

![jwt.io](./images/jwt-encoded.png)

*JWT token*
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6InZqbkR5S1dTb2N4b3ZaYmFCWnF1eFNVdlBpa3VVWjZGIn0.eyJuYW1lIjoiYWRtaW4iLCJlbWFpbCI6ImFkbWluQGVtYWlsLmNvbSIsImV4cCI6MTkwMDAwMDAwMH0.F_OEWked91eGd5Yxy_SyjVJCh2MDfrWHuHQYiiA4ZsE
```

### Test Credentials

#### Request Without Credential

```console
$ curl http://localhost:8000/api
```

*Response*
```
HTTP/1.1 401 Unauthorized
Date: Sun, 30 May 2021 22:09:20 GMT
Content-Type: application/json; charset=utf-8
Connection: close
Content-Length: 26
X-Kong-Response-Latency: 5
Server: kong/2.4.1

{
  "message": "Unauthorized"
}
```

#### Requests with Credential

```
$ curl http://localhost:8000/api \
 -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6InZqbkR5S1dTb2N4b3ZaYmFCWnF1eFNVdlBpa3VVWjZGIn0.eyJuYW1lIjoiYWRtaW4iLCJlbWFpbCI6ImFkbWluQGVtYWlsLmNvbSIsImV4cCI6MTkwMDAwMDAwMH0.F_OEWked91eGd5Yxy_SyjVJCh2MDfrWHuHQYiiA4ZsE"
```

*Response*
```
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 12
Connection: close
X-Powered-By: Express
ETag: W/"c-00hq6RNueFa8QiEjhep5cJRHWAI"
Date: Sun, 30 May 2021 22:01:54 GMT
X-Kong-Upstream-Latency: 146
X-Kong-Proxy-Latency: 21
Via: kong/2.4.1

Hello world!
```

# References
* [How to Use the Kong Gateway JWT Plugin for Service Authentication](https://konghq.com/blog/jwt-kong-gateway)
* [Kong JWT Plugin](https://docs.konghq.com/hub/kong-inc/jwt/?_ga=2.231115290.448208261.1622297983-785702943.1613576554#parameters)