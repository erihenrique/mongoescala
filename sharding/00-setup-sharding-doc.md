## Configuirando um Sharding em MongoDB usando Docker Containers

### Config servers
Iniciar os config servers (3 member replica set)
```
docker-compose -f config-server/docker-compose.yaml up -d
```
Iniciando a replica set
```
mongo mongodb://10.61.17.43:27018
```
```
rs.initiate(
  {
    _id: "cfgrs",
    configsvr: true,
    members: [
      { _id : 0, host : "10.61.17.43:27018" },
      { _id : 1, host : "10.61.17.43:27019" },
      { _id : 2, host : "10.61.17.43:27020" }
    ]
  }
)

rs.status()
```

### Shard 1 servers
Start shard 1 servers (3 member replicas set)
```
docker-compose -f shard1/docker-compose.yaml up -d
```
Initiate replica set
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
Start mongos query router
```
docker-compose -f mongos/docker-compose.yaml up -d
```

### Add shard to the cluster
Connect to mongos
```
mongo mongodb://10.61.17.43:60000
```
Add shard
```
mongos> sh.addShard("shard1rs/10.61.17.44:27017,10.61.17.44:27018,10.61.17.44:27019")
mongos> sh.status()
```
## Adding another shard
### Shard 2 servers
Start shard 2 servers (3 member replicas set)
```
docker-compose -f shard2/docker-compose.yaml up -d
```
Initiate replica set
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
### Add shard to the cluster
Connect to mongos
```
mongo mongodb://10.61.17.43:60000
```
Add shard
```
mongos> sh.addShard("shard2rs/10.61.17.45:27017,10.61.17.45:27018,10.61.17.45:27019")
mongos> sh.status()
```

## Adding another shard
### Shard 3 servers
Start shard 3 servers (3 member replicas set)
```
docker-compose -f shard3/docker-compose.yaml up -d
```
Initiate replica set
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
### Add shard to the cluster
Connect to mongos
```
mongo mongodb://10.61.17.43:60000
```
Add shard
```
mongos> sh.addShard("shard3rs/10.61.17.47:27017,10.61.17.47:27018,10.61.17.47:27019")
mongos> sh.status()
```
