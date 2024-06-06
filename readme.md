MongoDB (6.0.1) Sharded Cluster with Docker Compose
=========================================

## Credit: https://github.com/minhhungit/mongodb-cluster-docker-compose/tree/master

---
## ❓ Mongo Components

* Config Server (3 member replica set): `configsvr01`,`configsvr02`,`configsvr03`
* 3 Shards (each a 3 member `PSS` replica set):
	* `shard01-a`,`shard01-b`, `shard01-c`
	* `shard02-a`,`shard02-b`, `shard02-c`
	* `shard03-a`,`shard03-b`, `shard03-c`
* 2 Routers (mongos): `router01`, `router02`

<img src="https://raw.githubusercontent.com/Tfcosendey/mongodb-cluster/main/images/sharding-and-replica-sets.png" style="width: 100%;" />

## ✨ Steps 

### 👉 Step 1: Start all of the containers 

Clone this repository, open powershell or cmd on the repo folder and run:

```bash
docker-compose up -d
```

### 👉 Step 2: Initialize the replica sets (config servers and shards)

Run these command one by one:

```bash
docker-compose exec configsvr01 sh -c "mongosh < /scripts/init-configserver.js"

docker-compose exec shard01-a sh -c "mongosh < /scripts/init-shard01.js"
docker-compose exec shard02-a sh -c "mongosh < /scripts/init-shard02.js"
docker-compose exec shard03-a sh -c "mongosh < /scripts/init-shard03.js"
```

### 👉 Step 3: Initializing the router

>Note: Wait a bit for the config server and shards to elect their primaries before initializing the router

```bash
docker-compose exec router01 sh -c "mongosh < /scripts/init-router.js"
```

### 👉 Step 4: Enable sharding and setup sharding-key
```bash
docker-compose exec router01 mongosh --port 27017

// Enable sharding for database `MyDatabase`
sh.enableSharding("MyDatabase")

// Setup shardingKey for collection `MyCollection`**
db.adminCommand( { shardCollection: "MyDatabase.MyCollection", key: { warehouseId: 1, productId: 1 } } )

```

---
### ✔️ Done !!!
