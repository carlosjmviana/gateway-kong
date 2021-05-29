## Configure a Service

To test our installation process, we can configure a service. We are using the visual studio code extension REST Client to perform the http requests.

### Add Service

*REST Client*
```
POST http://localhost:8001/services/ HTTP/1.1
content-type: application/json

{
  "name": "demo-service",
  "url": "http://mockbin.org"
}

```

*With CURL*
```bash
$ curl -i -X POST \
  --url http://localhost:8001/services/ \
  --data 'name=demo-service' \
  --data 'url=http://mockbin.org'
```

*Response*
```console
HTTP/1.1 201 Created
Date: Sat, 29 May 2021 18:12:07 GMT
Content-Type: application/json; charset=utf-8
Connection: close
Access-Control-Allow-Origin: *
Content-Length: 358
X-Kong-Admin-Latency: 22
Server: kong/2.4.1

{
  "path": null,
  "port": 80,
  "client_certificate": null,
  "tags": null,
  "ca_certificates": null,
  "id": "14f377f0-66b9-4e4a-a2c6-3560df985094",
  "protocol": "http",
  "read_timeout": 60000,
  "write_timeout": 60000,
  "created_at": 1622311927,
  "updated_at": 1622311927,
  "host": "mockbin.org",
  "tls_verify_depth": null,
  "connect_timeout": 60000,
  "tls_verify": null,
  "retries": 5,
  "name": "demo-service"
}
```

### Add Route

*REST Client*
```
POST http://localhost:8001/services/demo-service/routes HTTP/1.1
Content-Type: application/x-www-form-urlencoded

hosts[]=example.com
```

*With CURL*
```bash
$ curl -i -X POST \
  --url http://localhost:8001/services/demo-service/routes \
  --data 'hosts[]=example.com'
```

*Response*
```
HTTP/1.1 201 Created
Date: Sat, 29 May 2021 18:24:02 GMT
Content-Type: application/json; charset=utf-8
Connection: close
Access-Control-Allow-Origin: *
Content-Length: 480
X-Kong-Admin-Latency: 28
Server: kong/2.4.1

{
  "sources": null,
  "destinations": null,
  "preserve_host": false,
  "strip_path": true,
  "snis": null,
  "tags": null,
  "id": "5e7d6481-3c72-4c94-b18d-6c0266fd1bac",
  "hosts": [
    "example.com"
  ],
  "service": {
    "id": "14f377f0-66b9-4e4a-a2c6-3560df985094"
  },
  "response_buffering": true,
  "request_buffering": true,
  "headers": null,
  "path_handling": "v0",
  "created_at": 1622312642,
  "updated_at": 1622312642,
  "protocols": [
    "http",
    "https"
  ],
  "regex_priority": 0,
  "https_redirect_status_code": 426,
  "name": null,
  "paths": null,
  "methods": null
}
```

[Kong API Route details.](https://docs.konghq.com/gateway-oss/2.4.x/admin-api/#add-route).

### Test Request
Kong receive proxy requests on port **8000**.

*REST Client*
```
GET http://localhost:8000 HTTP/1.1
Host: example.com
```

*CURL*
```
curl -i http://localhost:8000/ --header 'Host: example.com'
```

*Response*
```
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Transfer-Encoding: chunked
Connection: close
Date: Sat, 29 May 2021 18:32:27 GMT
Vary: Accept-Encoding
Via: kong/2.4.1
CF-Cache-Status: DYNAMIC
cf-request-id: 0a5aff33620000f1ff6db78000000001
Report-To: {"endpoints":[{"url":"https:\/\/a.nel.cloudflare.com\/report\/v2?s=rHB2e1B0D9C9RyE7J6gm9f1hcG9URb4Vh%2BQQuFYg6d0pX0m65zv15Og3ri%2BC3bspZtg6gSZyYfUzV%2F8s%2BtFH4oCkHfIZfugN6VQ5h72E06TMo1z%2BKkTHW%2BQ%3D"}],"group":"cf-nel","max_age":604800}
NEL: {"report_to":"cf-nel","max_age":604800}
Server: cloudflare
CF-RAY: 6571ce323dd3f1ff-GRU
Content-Encoding: gzip
alt-svc: h3-27=":443"; ma=86400, h3-28=":443"; ma=86400, h3-29=":443"; ma=86400
X-Kong-Upstream-Latency: 308
X-Kong-Proxy-Latency: 2

<!DOCTYPE html><html><head>...
```

## References

 * [Kong Configure Service](https://docs.konghq.com/gateway-oss/2.4.x/getting-started/configuring-a-service/)