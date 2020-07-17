## Configuirando um Sharding em MongoDB usando Docker Containers

### Config servers
Iniciar os config servers (3 member replica set)
```
docker-compose -f config-server/docker-compose.yaml up -d
```
Iniciando a replica set
```
mongo mongodb://10.61.17.43:27019
```
```
rs.initiate(
  {
    _id: "cfgrs",
    configsvr: true,
    members: [
      { _id : 0, host : "10.61.17.43:27019" },
      { _id : 1, host : "10.61.17.43:27020" },
      { _id : 2, host : "10.61.17.43:27021" }
    ]
  }
)

rs.status()
```

### Shard 1 servers
Iniciar shard 1 servers (3 member replicas set)
```
docker-compose -f shard1/docker-compose.yaml up -d
```
Iniciando replica set
```
mongo mongodb://10.61.17.44:27017
```
```
rs.initiate(
  {
    _id: "shard1rs",
    members: [
      { _id : 0, host : "10.61.17.44:27017" },
      { _id : 1, host : "10.61.17.44:27018" },
      { _id : 2, host : "10.61.17.44:27019" }
    ]
  }
)

rs.status()
```

### Mongos Router
Iniciar mongos query router
```
docker-compose -f mongos/docker-compose.yaml up -d
```

### Adicionar shard ao cluster
Conectar ao mongos
```
mongo mongodb://10.61.17.43:27017
```
Adicionar shard
```
mongos> sh.addShard("shard1rs/10.61.17.44:27017,10.61.17.44:27018,10.61.17.44:27019")
mongos> sh.status()
```
## Adicionar outro shard
### Shard 2 servers
Iniciar shard 2 servers (3 member replicas set)
```
docker-compose -f shard2/docker-compose.yaml up -d
```
Iniciando replica set
```
mongo mongodb://10.61.17.45:27017
```
```
rs.initiate(
  {
    _id: "shard2rs",
    members: [
      { _id : 0, host : "10.61.17.45:27017" },
      { _id : 1, host : "10.61.17.45:27018" },
      { _id : 2, host : "10.61.17.45:27019" }
    ]
  }
)

rs.status()
```
### Adicionar shard ao cluster
Conectar ao mongos
```
mongo mongodb://10.61.17.43:27017
```
Adicionar shard
```
mongos> sh.addShard("shard2rs/10.61.17.45:27017,10.61.17.45:27018,10.61.17.45:27019")
mongos> sh.status()
```

## Adicionar outro shard
### Shard 3 servers
Iniciar shard 3 servers (3 member replicas set)
```
docker-compose -f shard3/docker-compose.yaml up -d
```
Iniciando replica set
```
mongo mongodb://10.61.17.47:27017
```
```
rs.initiate(
  {
    _id: "shard3rs",
    members: [
      { _id : 0, host : "10.61.17.47:27017" },
      { _id : 1, host : "10.61.17.47:27018" },
      { _id : 2, host : "10.61.17.47:27019" }
    ]
  }
)

rs.status()
```
### Adcionar shard ao cluster
Conectar ao mongos
```
mongo mongodb://10.61.17.43:27017
```
Adicionar shard
```
mongos> sh.addShard("shard3rs/10.61.17.47:27017,10.61.17.47:27018,10.61.17.47:27019")
mongos> sh.status()
```
