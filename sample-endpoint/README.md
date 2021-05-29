# Hello World Service

We will create a node hello world service with express.

## Create Hello-World Project
```
$ mkdir hello-world
$ cd hello-world
$ yarn init 
```

We answered with all default values.

### Add Express Library
*Add express*
```
$ yarn add express
```

### Service Code

We create hello-world/index.js file with the following content:
```
const express = require('express')
const server = express()
const port = 3000
 
server.get('/', (req, res) => {
  console.log(req.headers)
  res.status(200).send('Hello world!')
})
 
server.listen(port, () => {
  console.log(`Server is listening on http://localhost:${port}`)
})
```

### Start Service
```
$ node index.js

Server is listening on http://localhost:3000
```

### Test Service

Open URL http://localhost:3000 on browser or

```
$ curl http://localhost:3000

Hello world!
```

## Kong Service And Route

We have to create a service and route in kong.

*Service*
```
POST http://localhost:8001/services/ HTTP/1.1
content-type: application/json

{
  "name": "hello-world-service",
  "url": "http://<hostname or hostip>:3000/"
}
```

*Route*
```
POST http://localhost:8001/services/hello-world-service/routes HTTP/1.1
Content-Type: application/x-www-form-urlencoded

paths[]=/api
```

*Test*
```
GET http://localhost:8000/api HTTP/1.1

HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 12
Connection: close
X-Powered-By: Express
ETag: W/"c-00hq6RNueFa8QiEjhep5cJRHWAI"
Date: Sat, 29 May 2021 20:05:46 GMT
X-Kong-Upstream-Latency: 14
X-Kong-Proxy-Latency: 147
Via: kong/2.4.1

Hello world!
```