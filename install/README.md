# Kong Instalation

We'll use the OpenSource version Gateway Kong OSS with Docker. The latest version is 2.4.1.

## Kong Network

We will create a specific network to kong and kong-database containers. 


```
$ docker network create kong-net

69a2699b4ffaf80a10e50d741537be993750082eb81d436d959e9008c83e5199
```

Inspect the kong-net

```
$ docker inspect kong-net

[
    {
        "Name": "kong-net",
        "Id": "69a2699b4ffaf80a10e50d741537be993750082eb81d436d959e9008c83e5199",
        "Created": "2021-05-29T16:31:39.514733314Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "XXX.YY.0.0/16",
                    "Gateway": "XXX.YY.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }

```

The default kong-net type is a *bridge* network.

## Kong With Database

Kong will be installed with PostgresSQL Database.

### Install Postgres

```
$ docker run -d --name kong-database \
  --rm \
  --network=kong-net
  -p 5432:5432 \
  -e "POSTGRES_USER=kong" \
  -e "POSTGRES_DB=kong" \
  -e "POSTGRES_PASSWORD=kong123" \
  postgres:9.6 
```

### Prepare Kong Databse

We need to prepare the kong database before use it.
 ```bash
 $ docker run --rm \
     --network=kong-net \
     -e "KONG_DATABASE=postgres" \
     -e "KONG_PG_HOST=kong-database" \
     -e "KONG_PG_USER=kong" \
     -e "KONG_PG_PASSWORD=kong" \
     -e "KONG_CASSANDRA_CONTACT_POINTS=kong-database" \
     kong:2.4.1-alpine kong migrations bootstrap 
 ```

The command output log can be seen [here](./kong-migrations.log).

 ### Start Kong

 ```bash
$ docker run -d --name kong \
     --rm
     --network=kong-net \
     -e "KONG_DATABASE=postgres" \
     -e "KONG_PG_HOST=kong-database" \
     -e "KONG_PG_USER=kong" \
     -e "KONG_PG_PASSWORD=kong123" \
     -e "KONG_CASSANDRA_CONTACT_POINTS=kong-database" \
     -e "KONG_PROXY_ACCESS_LOG=/dev/stdout" \
     -e "KONG_ADMIN_ACCESS_LOG=/dev/stdout" \
     -e "KONG_PROXY_ERROR_LOG=/dev/stderr" \
     -e "KONG_ADMIN_ERROR_LOG=/dev/stderr" \
     -e "KONG_ADMIN_LISTEN=0.0.0.0:8001, 0.0.0.0:8444 ssl" \
     -p 8000:8000 \
     -p 8443:8443 \
     -p 127.0.0.1:8001:8001 \
     -p 127.0.0.1:8444:8444 \
     kong:2.4.1-alpine
 ```

We can also start Kong wihout a Database. See [here](https://docs.konghq.com/install/docker/?_ga=2.167182651.448208261.1622297983-785702943.1613576554#db-less-mode) more details.

### Kong access

We can see that kong was configured performing REST requests to Kong admin API:

*Listing Kong configurations*
```
$ curl http://localhost:8001
```

or listing kong services

```
$ curl http://localhost:8001/services
```

 ## References
 * [Kong docs install docker](https://docs.konghq.com/install/docker/?_ga=2.167182651.448208261.1622297983-785702943.1613576554)
 * [Kong Docker Hub](https://hub.docker.com/_/kong/)
