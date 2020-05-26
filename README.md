# Redis Sentinel

## Introducción

El cluster está formado por un maestro, una réplica y escalaremos a 3 sentinels

* master: Master Redis Server
* replica:  Replica Redis Server
* sentinel: Sentinel Server

Los sentinels están configurados en un cluster "undavmaster", con las siguientes configuraciones:

```
sentinel monitor undavmaster redis-master 6379 2
sentinel down-after-milliseconds undavmaster 5000
sentinel parallel-syncs undavmaster 1
sentinel failover-timeout undavmaster 5000
```

El archivo de config está sentinel/sentinel.conf

Algunas variables de entorno están en el Dockerfile de sentinel

* SENTINEL_QUORUM: 2
* SENTINEL_DOWN_AFTER: 5000
* SENTINEL_FAILOVER: 5000

## Manos a la obra

```bash
docker-compose up --build
```

Chequeamos el estado del cluster

```bash
docker-compose ps
```

Escalamos el servicio de sentinel a tres instancias

```bash
docker-compose scale sentinel=3
```

Escalamos las replicas

```bash
docker-compose scale replica=4
```

Chequeamos el cluster

```bash
docker-compose ps
```

El resultado debiese ser algo como: 

```bash
               Name                              Command               State    Ports
---------------------------------------------------------------------------------------
redisclusterwithsentinel_master_1     docker-entrypoint.sh redis ...   Up      6379/tcp
redisclusterwithsentinel_sentinel_1   entrypoint.sh                    Up      6379/tcp
redisclusterwithsentinel_sentinel_2   entrypoint.sh                    Up      6379/tcp
redisclusterwithsentinel_sentinel_3   entrypoint.sh                    Up      6379/tcp
redisclusterwithsentinel_replica_1      docker-entrypoint.sh redis ...   Up      6379/tcp
redisclusterwithsentinel_replica_2      docker-entrypoint.sh redis ...   Up      6379/tcp
redisclusterwithsentinel_replica_3      docker-entrypoint.sh redis ...   Up      6379/tcp
redisclusterwithsentinel_replica_4      docker-entrypoint.sh redis ...   Up      6379/tcp
```

For stop master redis server.

```bash
 docker-compose unpause master
```

Para obterner la información de los sentinels

```bash
docker-compose exec sentinel redis-cli -p 26379 SENTINEL get-master-addr-by-name undavmaster
```

## Referencias

* https://github.com/AliyunContainerService/redis-cluster
* https://registry.hub.docker.com/u/joshula/redis-sentinel/
* https://docs.docker.com/compose/

## Contribuidores, traducción y adaptación

El código original surgió de https://github.com/mustafaileri/redis-cluster-with-sentinel

* Mustafa Ileri (<mi@mustafaileri.com>)

Fue traducido y adaptado para la materia Programación Distribuida II de la Universidad Nacional de Avellaneda
