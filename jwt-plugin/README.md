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

# References
* [Kong JWT Plugin](https://docs.konghq.com/hub/kong-inc/jwt/?_ga=2.231115290.448208261.1622297983-785702943.1613576554#parameters)