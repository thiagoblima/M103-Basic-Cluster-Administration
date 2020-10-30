# M103-Basic-Cluster-Administration
> MongoDB University course for basic cluster management.

Basic MongoDB cluster operations, this is a studying repository and the *answers aren't being shown here*, only import commands, the only reason of this repository is spreading knowledge.


![](assets/mongo_db_university.png)


## MongoDB University M103 - Basic Cluster Administration

> https://university.mongodb.com/courses/M103/about

## The Mongod - Chapter 1

Launch mongod in the first shell:

```
mongod
```

Connect to the Mongo shell in the second shell:

```
mongo
```

To create a new collection:

```
db.createCollection("employees")
```

Try to connect back to Mongo shell, without specifying a port:
```
use admin
db.shutdownServer()
exit
```
## The Mongod Options

Below are some of the available options for mongod. Note that this is not a comprehensive list of all possible mongod configurations. To see all available options, run mongod with the help flag.

```
mongod --help
```

This command will output the various options for mongod with a description of their functionality.

Note: The --fork option is not available on the Windows operating system.

> dbpath

The dbpath is the directory where all the data files for your database are stored. The dbpath also contains journaling logs to provide durability in case of a crash. As we saw before, the default dbpath is /data/db; however, you can specify any directory that exists on your machine. The directory must have read/write permissions since database and journaling files will be written to the directory. To use the dbpath option, include the dbpath flag and specify the name of your directory:

```
mongod --dbpath <directory path>
```

> port

The port option allows us to specify the port on which mongod will listen for client connections. If we don't specify a port, it will default to 27017. Database clients should specify the same port to connect to mongod. To specify a port, run:

```
mongod --port <port number>
```

> auth

auth enables authentication to control which users can access the database. When auth is specified, all database clients who want to connect to mongod first need to authenticate.

Before any database users have been configured, a Mongo shell running on localhost will have access to the database. We can then configure users and their permission levels using the shell. Once one or more users have been configured, the shell will no longer have default access. To enable authentication, run mongod with the auth option:

```
mongod --auth
```


> bind_ip

The bind_ip option allows us to specify which IP addresses mongod should bind to. When mongod binds to an IP address, clients from that address are able to connect to mongod. For instance, if we wanted to allow clients on IP address 123.123.123.123 to access our database, we'd use the following command:

```
mongod --bind_ip 123.123.123.123
```

To bind to multiple addresses and/or hosts, you can specify them in a comma-separated list:

```
mongod --bind_ip localhost,123.123.123.123
```

If using the bind_ip option with external IP addresses, it's recommended to enable auth to ensure that remote clients connecting to mongod have the proper credentials.

## The Configuration File

These lecture instructions are not meant to be reproduced in your environment. They reflect what you will see in the lecture video. However, they may point to non-existing resources and files.

Launch mongod using default configuration:

```
mongod
```

Launch mongod with specified --dbpath and --logpath:

```
mongod --dbpath /data/db --logpath /data/log/mongod.log
```

Launch mongod and fork the process:

```
mongod --dbpath /data/db --logpath /data/log/mongod.log --fork
```

Launch mongod with many configuration options:

Note that all "ssl" options have been edited to use "tls" instead. As of MongoDB 4.2, options using "ssl" have been deprecated.

```
mongod --dbpath /data/db --logpath /data/log/mongod.log --fork --replSet "M103" --keyFile /data/keyfile --bind_ip "127.0.0.1,192.168.103.100" --tlsMode requireTLS --tlsCAFile "/etc/tls/TLSCA.pem" --tlsCertificateKeyFile "/etc/tls/tls.pem"
```

Example configuration file, with the same configuration options as above:

```
storage:
  dbPath: "/data/db"
systemLog:
  path: "/data/log/mongod.log"
  destination: "file"
replication:
  replSetName: M103
net:
  bindIp : "127.0.0.1,192.168.103.100"
tls:
  mode: "requireTLS"
  certificateKeyFile: "/etc/tls/tls.pem"
  CAFile: "/etc/tls/TLSCA.pem"
security:
  keyFile: "/data/keyfile"
processManagement:
  fork: true
```

## File Structure

List --dbpath directory:

```
ls -l /data/db
```

List diagnostics data directory:

```
ls -l /data/db/diagnostic.data
```

List journal directory:

```
ls -l /data/db/journal
```

List socket file:

```
ls /tmp/mongodb-27017.sock
```

## Basic Commands

The explain() output has changed in MongoDB 4.2. Specifically, a new section called explain.queryPlanner.optimizedPipeline has been added to the output. You can read about it in the optimizedPipeline docs.

_Lecture Instructions_

User management commands:

```
db.createUser()
db.dropUser()
```

Collection management commands:

```
db.<collection>.renameCollection()
db.<collection>.createIndex()
db.<collection>.drop()
```

Database management commands:

```
db.dropDatabase()
db.createCollection()
```

Database status command:

```
db.serverStatus()
```

Creating index with Database Command:

```
db.runCommand(
  { "createIndexes": <collection> },
  { "indexes": [
    {
      "key": { "product": 1 }
    },
    { "name": "name_index" }
    ]
  }
)
```

Creating index with Shell Helper:

```
db.<collection>.createIndex(
  { "product": 1 },
  { "name": "name_index" }
)
```

Introspect a Shell Helper:

```
db.<collection>.createIndex
```

## Logging Basics

Get the logging components:

```
mongo admin --host 192.168.103.100:27000 -u m103-admin -p m103-pass --eval '
  db.getLogComponents()
'
```

Change the logging level:

```
mongo admin --host 192.168.103.100:27000 -u m103-admin -p m103-pass --eval '
  db.setLogLevel(0, "index")
'
```

View the logs through the Mongo shell:

```
db.adminCommand({ "getLog": "global" })
```

View the logs through the command line:

```
tail -f /data/db/mongod.log
```

Update a document:

```
mongo admin --host 192.168.103.100:27000 -u m103-admin -p m103-pass --eval '
  db.products.update( { "sku" : 6902667 }, { $set : { "salePrice" : 39.99} } )
'
```

Look for instructions in the log file with grep:

```
grep -i 'update' /data/db/mongod.log
```

## Profiling the Database

Note: The command ``show collections`` no longer lists the system.* collections. It changed after version 4.0.

To list all of the collection names you can run this command:

```
db.runCommand({listCollections: 1}) 
```

Get profiling level:

```
mongo newDB --host 192.168.103.100:27000 -u m103-admin -p m103-pass --authenticationDatabase admin --eval '
  db.getProfilingLevel()
'
```

Set profiling level:

```
mongo newDB --host 192.168.103.100:27000 -u m103-admin -p m103-pass --authenticationDatabase admin --eval '
  db.setProfilingLevel(1)
'
```

Show collections:

```
mongo newDB --host 192.168.103.100:27000 -u m103-admin -p m103-pass --authenticationDatabase admin --eval '
  db.getCollectionNames()
'
```

Note: show collections only works from within the shell

Set slowms to 0:

```
mongo newDB --host 192.168.103.100:27000 -u m103-admin -p m103-pass --authenticationDatabase admin --eval '
  db.setProfilingLevel( 1, { slowms: 0 } )
'
```

Insert one document into a new collection:

```
mongo newDB --host 192.168.103.100:27000 -u m103-admin -p m103-pass --authenticationDatabase admin --eval '
  db.new_collection.insert( { "a": 1 } )
'
```

Get profiling data from system.profile:

```
mongo newDB --host 192.168.103.100:27000 -u m103-admin -p m103-pass --authenticationDatabase admin --eval '
  db.system.profile.find().pretty()
'
```

## Basic Security

Print configuration file:

```
cat /etc/mongod.conf
``` 

Launch standalone mongod:

```
mongod -f /etc/mongod.conf
```

Connect to mongod:

```
mongo --host 127.0.0.1:27017
```

Create new user with the root role (also, named root):

```
use admin
db.createUser({
  user: "root",
  pwd: "root123",
  roles : [ "root" ]
})
```

Connect to mongod and authenticate as root:

```
mongo --username root --password root123 --authenticationDatabase admin
```

Run DB stats:

```
db.stats()
```

Shutdown the server:

```
use admin
db.shutdownServer()
```

## Built-In Roles

Authenticate as ```root``` user:

```
mongo admin -u root -p root123
```

Create security officer:

```
db.createUser(
  { user: "security_officer",
    pwd: "h3ll0th3r3",
    roles: [ { db: "admin", role: "userAdmin" } ]
  }
)
```

Create database administrator:

```
db.createUser(
  { user: "dba",
    pwd: "c1lynd3rs",
    roles: [ { db: "admin", role: "dbAdmin" } ]
  }
)
```

Grant role to user:

```
db.grantRolesToUser( "dba",  [ { db: "playground", role: "dbOwner"  } ] )
```

Show role privileges:

```
db.runCommand( { rolesInfo: { role: "dbOwner", db: "playground" }, showPrivileges: true} )
```

## Server Tools Overview

List mongodb binaries:

```
find /usr/bin/ -name "mongo*"
```

Create new dbpath and launch mongod:

```
mkdir -p ~/first_mongod
mongod --port 30000 --dbpath ~/first_mongod --logpath ~/first_mongod/mongodb.log --fork
```

Use mongostat to get stats on a running mongod process:

```
mongostat --help
mongostat --port 30000
```

Use mongodump to get a BSON dump of a MongoDB collection:

```
mongodump --help
mongodump --port 30000 --db applicationData --collection products
ls dump/applicationData/
cat dump/applicationData/products.metadata.json
```

Use mongorestore to restore a MongoDB collection from a BSON dump:

```
mongorestore --drop --port 30000 dump/
```

Use mongoexport to export a MongoDB collection to JSON or CSV (or stdout!):

```
mongoexport --help
mongoexport --port 30000 --db applicationData --collection products
mongoexport --port 30000 --db applicationData --collection products -o products.json
```

Tail the exported JSON file:

```
tail products.json
```

Use mongoimport to create a MongoDB collection from a JSON or CSV file:

```
mongoimport --port 30000 products.json
```

## Replication - Chapter 2

#### Setting Up a Replica Set

Lecture Instructions

Note: In the video lecture, we used the old hostname "m103.mongodb.university" which has been changed to "m103" . We have updated all of the following commands accordingly.

The configuration file for the first node (node1.conf):

```
storage:
  dbPath: /var/mongodb/db/node1
net:
  bindIp: 192.168.103.100,localhost
  port: 27011
security:
  authorization: enabled
  keyFile: /var/mongodb/pki/m103-keyfile
systemLog:
  destination: file
  path: /var/mongodb/db/node1/mongod.log
  logAppend: true
processManagement:
  fork: true
replication:
  replSetName: m103-example
```

Creating the keyfile and setting permissions on it:

```
sudo mkdir -p /var/mongodb/pki/
sudo chown vagrant:vagrant /var/mongodb/pki/
openssl rand -base64 741 > /var/mongodb/pki/m103-keyfile
chmod 400 /var/mongodb/pki/m103-keyfile
```

Creating the dbpath for node1:

```
mkdir -p /var/mongodb/db/node1
```
Starting a mongod with node1.conf:

```
mongod -f node1.conf
```

Copying node1.conf to node2.conf and node3.conf:

```
cp node1.conf node2.conf
cp node2.conf node3.conf
```

Editing node2.conf using vi:

```
vi node2.conf
```

Saving the file and exiting vi:

```
:wq
```

node2.conf, after changing the dbpath, port, and logpath:

```
storage:
  dbPath: /var/mongodb/db/node2
net:
  bindIp: 192.168.103.100,localhost
  port: 27012
security:
  keyFile: /var/mongodb/pki/m103-keyfile
systemLog:
  destination: file
  path: /var/mongodb/db/node2/mongod.log
  logAppend: true
processManagement:
  fork: true
replication:
  replSetName: m103-example
```

node3.conf, after changing the dbpath, port, and logpath:

```
storage:
  dbPath: /var/mongodb/db/node3
net:
  bindIp: 192.168.103.100,localhost
  port: 27013
security:
  keyFile: /var/mongodb/pki/m103-keyfile
systemLog:
  destination: file
  path: /var/mongodb/db/node3/mongod.log
  logAppend: true
processManagement:
  fork: true
replication:
  replSetName: m103-example
```

Creating the data directories for node2 and node3:

```
mkdir /var/mongodb/db/{node2,node3}
```

Starting mongod processes with node2.conf and node3.conf:

```
mongod -f node2.conf
mongod -f node3.conf
```

Connecting to node1:

```
mongo --port 27011
```

Initiating the replica set:

```
rs.initiate()
```

Creating a user:

```
use admin
db.createUser({
  user: "m103-admin",
  pwd: "m103-pass",
  roles: [
    {role: "root", db: "admin"}
  ]
})
```

Exiting out of the Mongo shell and connecting to the entire replica set:

```
exit
mongo --host "m103-example/192.168.103.100:27011" -u "m103-admin"
-p "m103-pass" --authenticationDatabase "admin"
```

Getting replica set status:

```
rs.status()
```

Adding other members to replica set:

```
rs.add("m103:27012")
rs.add("m103:27013")
```

Getting an overview of the replica set topology:

```
rs.isMaster()
```

Stepping down the current primary:

```
rs.stepDown()
```

Checking replica set overview after election:

```
rs.isMaster()
```

## Replication Commands

Commands covered in this lesson:

> rs.status()

> rs.isMaster()

> db.serverStatus()['repl']

> rs.printReplicationInfo()

## Local DB: Part 1

Make a data directory and launch a mongod process for a standalone node:

```
mkdir allbymyselfdb
mongod --dbpath allbymyselfdb
```

Display all databases (by default, only admin and local):

```
mongo
show dbs
```

Display collections from the local database (this displays more collections from a replica set than from a standalone node):

```
use local
show collections
```

Query the oplog after connected to a replica set:

```
use local
db.oplog.rs.find()
```

Get information about the oplog (remember the oplog is a capped collection).

Store oplog stats as a variable called stats:

```
var stats = db.oplog.rs.stats()
```

Verify that this collection is capped (it will grow to a pre-configured size before it starts to overwrite the oldest entries with newer ones):

```
stats.capped
```

Get current size of the oplog:

```
stats.size
```

Get size limit of the oplog:

```
stats.maxSize
```

Get current oplog data (including first and last event times, and configured oplog size):

```
rs.printReplicationInfo()
```

## Local DB: Part 2

Create new namespace m103.messages:

```
use m103
db.createCollection('messages')
```

Query the oplog, filtering out the heartbeats ("periodic noop") and only returning the latest entry:

```
use local
db.oplog.rs.find( { "o.msg": { $ne: "periodic noop" } } ).sort( { $natural: -1 } ).limit(1).pretty()
```

Insert 100 different documents:

```
use m103
for ( i=0; i< 100; i++) { db.messages.insert( { 'msg': 'not yet', _id: i } ) }
db.messages.count()
```

Query the oplog to find all operations related to m103.messages:

```
use local
db.oplog.rs.find({"ns": "m103.messages"}).sort({$natural: -1})
```

Illustrate that one update statement may generate many entries in the oplog:

```
use m103
db.messages.updateMany( {}, { $set: { author: 'norberto' } } )
use local
db.oplog.rs.find( { "ns": "m103.messages" } ).sort( { $natural: -1 } )
```


## Reconfiguring a Running Replica Set

> node4.conf:

```
storage:
  dbPath: /var/mongodb/db/node4
net:
  bindIp: 192.168.103.100,localhost
  port: 27014
systemLog:
  destination: file
  path: /var/mongodb/db/node4/mongod.log
  logAppend: true
processManagement:
  fork: true
replication:
  replSetName: m103-example
```

> arbiter.conf:

```
storage:
  dbPath: /var/mongodb/db/arbiter
net:
  bindIp: 192.168.103.100,localhost
  port: 28000
systemLog:
  destination: file
  path: /var/mongodb/db/arbiter/mongod.log
  logAppend: true
processManagement:
  fork: true
replication:
  replSetName: m103-example
```

Starting up mongod processes for our fourth node and arbiter:

```
mongod -f node4.conf
mongod -f arbiter.conf
```

From the Mongo shell of the replica set, adding the new secondary and the new arbiter:

```
rs.add("m103:27014")
rs.addArb("m103:28000")
```

Checking replica set makeup after adding two new nodes:

```
rs.isMaster()
```

Removing the arbiter from our replica set:

```
rs.remove("m103:28000")
```

Assigning the current configuration to a shell variable we can edit, in order to reconfigure the replica set:

```
cfg = rs.conf()
```

Editing our new variable cfg to change topology - specifically, by modifying cfg.members:

```
cfg.members[3].votes = 0
cfg.members[3].hidden = true
cfg.members[3].priority = 0
```

Updating our replica set to use the new configuration cfg:

```
rs.reconfig(cfg)
```

## Reads and Writes on a Replica Set

Connecting to the replica set:

```
mongo --host "m103-example/m103:27011" -u "m103-admin" -p
"m103-pass" --authenticationDatabase "admin"
```

Checking replica set topology:

```
rs.isMaster()
```

Inserting one document into a new collection:

```
use newDB
db.new_collection.insert( { "student": "Matt Javaly", "grade": "A+" } )
```

Connecting directly to a secondary node (this node may not be a secondary in your replica set!):

```
mongo --host "m103:27012" -u "m103-admin" -p "m103-pass"
--authenticationDatabase "admin"
```

Attempting to execute a read command on a secondary node (this should fail):

```
show dbs
```

Enabling read commands on a secondary node:

```
rs.slaveOk()
```

Reading from a secondary node:

```
use newDB
db.new_collection.find()
```

Attempting to write data directly to a secondary node (this should fail, because we cannot write data directly to a secondary):

```
db.new_collection.insert( { "student": "Norberto Leite", "grade": "B+" } )
```

Shutting down the server (on both secondary nodes)

```
use admin
db.shutdownServer()
```

Connecting directly to the last healthy node in our set:

```
mongo --host "m103:27011" -u "m103-admin" -p "m103-pass"
--authenticationDatabase "admin"
```

Verifying that the last node stepped down to become a secondary when a majority of nodes in the set were not available:

```
rs.isMaster()
```

## Failover and Elections

Storing replica set configuration as a variable cfg:

```
cfg = rs.conf()
```

Setting the priority of a node to 0, so it cannot become primary (making the node "passive"):

```
cfg.members[2].priority = 0
```

Updating our replica set to use the new configuration cfg:

```
rs.reconfig(cfg)
```

Checking the new topology of our set:

```
rs.isMaster()
```

Forcing an election in this replica set (although in this case, we rigged the election so only one node could become primary):

```
rs.stepDown()
```

Checking the topology of our set after the election:

```
rs.isMaster()
```

## Chapter 3: Sharding

### Setting Up a Sharded Cluster

Configuration file for first config server:

> csrs_1.conf

```
sharding:
  clusterRole: configsvr
replication:
  replSetName: m103-csrs
security:
  keyFile: /var/mongodb/pki/m103-keyfile
net:
  bindIp: localhost,192.168.103.100
  port: 26001
systemLog:
  destination: file
  path: /var/mongodb/db/csrs1.log
  logAppend: true
processManagement:
  fork: true
storage:
  dbPath: /var/mongodb/db/csrs1
```

> csrs_2.conf:

```
sharding:
  clusterRole: configsvr
replication:
  replSetName: m103-csrs
security:
  keyFile: /var/mongodb/pki/m103-keyfile
net:
  bindIp: localhost,192.168.103.100
  port: 26002
systemLog:
  destination: file
  path: /var/mongodb/db/csrs2.log
  logAppend: true
processManagement:
  fork: true
storage:
  dbPath: /var/mongodb/db/csrs2
```

> csrs_3.conf:

```
sharding:
  clusterRole: configsvr
replication:
  replSetName: m103-csrs
security:
  keyFile: /var/mongodb/pki/m103-keyfile
net:
  bindIp: localhost,192.168.103.100
  port: 26003
systemLog:
  destination: file
  path: /var/mongodb/db/csrs3.log
  logAppend: true
processManagement:
  fork: true
storage:
  dbPath: /var/mongodb/db/csrs3
```

Starting the three config servers:

```
mongod -f csrs_1.conf
mongod -f csrs_2.conf
mongod -f csrs_3.conf
```

Connect to one of the config servers:

```
mongo --port 26001
```

Initiating the CSRS:

```
rs.initiate()
```

Creating super user on CSRS:

```
use admin
db.createUser({
  user: "m103-admin",
  pwd: "m103-pass",
  roles: [
    {role: "root", db: "admin"}
  ]
})
```

Authenticating as the super user:

```
db.auth("m103-admin", "m103-pass")
```

Add the second and third node to the CSRS:

```
rs.add("192.168.103.100:26002")
rs.add("192.168.103.100:26003")
```

Mongos config (mongos.conf):

```
sharding:
  configDB: m103-csrs/192.168.103.100:26001,192.168.103.100:26002,192.168.103.100:26003
security:
  keyFile: /var/mongodb/pki/m103-keyfile
net:
  bindIp: localhost,192.168.103.100
  port: 26000
systemLog:
  destination: file
  path: /var/mongodb/db/mongos.log
  logAppend: true
processManagement:
  fork: true
```

Connect to mongos:

```
vagrant@m103:~$ mongo --port 26000 --username m103-admin --password m103-pass --authenticationDatabase admin
```

Check sharding status:

```
MongoDB Enterprise mongos> sh.status()
```

> Updated configuration for node1.conf:

```
sharding:
  clusterRole: shardsvr
storage:
  dbPath: /var/mongodb/db/node1
  wiredTiger:
    engineConfig:
      cacheSizeGB: .1
net:
  bindIp: 192.168.103.100,localhost
  port: 27011
security:
  keyFile: /var/mongodb/pki/m103-keyfile
systemLog:
  destination: file
  path: /var/mongodb/db/node1/mongod.log
  logAppend: true
processManagement:
  fork: true
replication:
  replSetName: m103-repl
```

> Updated configuration for node2.conf:

```
sharding:
  clusterRole: shardsvr
storage:
  dbPath: /var/mongodb/db/node2
  wiredTiger:
    engineConfig:
      cacheSizeGB: .1
net:
  bindIp: 192.168.103.100,localhost
  port: 27012
security:
  keyFile: /var/mongodb/pki/m103-keyfile
systemLog:
  destination: file
  path: /var/mongodb/db/node2/mongod.log
  logAppend: true
processManagement:
  fork: true
replication:
  replSetName: m103-repl
```

> Updated configuration for node3.conf:

```
sharding:
  clusterRole: shardsvr
storage:
  dbPath: /var/mongodb/db/node3
  wiredTiger:
    engineConfig:
      cacheSizeGB: .1
net:
  bindIp: 192.168.103.100,localhost
  port: 27013
security:
  keyFile: /var/mongodb/pki/m103-keyfile
systemLog:
  destination: file
  path: /var/mongodb/db/node3/mongod.log
  logAppend: true
processManagement:
  fork: true
replication:
  replSetName: m103-repl
```

Connecting directly to secondary node (note that if an election has taken place in your replica set, the specified node may have become primary):

```
mongo --port 27012 -u "m103-admin" -p "m103-pass" --authenticationDatabase "admin"
```

Shutting down node:

```
use admin
db.shutdownServer()
```

Restarting node with new configuration:

```
mongod -f node2.conf
```

Stepping down current primary:

```
rs.stepDown()
```

Adding new shard to cluster from mongos:

```
sh.addShard("m103-repl/192.168.103.100:27012")
```

If you'd like to explore the collections on the config database, you can find the instructions here:

### Config DB

Switch to config DB:

```
use config
```

> Query config.databases:

```
db.databases.find().pretty()
```

> Query config.collections:

```
db.collections.find().pretty()
```

> Query config.shards:

```
db.shards.find().pretty()
```

> Query config.chunks:

```
db.chunks.find().pretty()
```

> Query config.mongos:

```
db.mongos.find().pretty()
``` 

### Shard Keys

If you'd like to shard a collection, you can find instructions to create a shard key here:

> Show collections in m103 database:

```
use m103
show collections
```

> Enable sharding on the m103 database:

```
sh.enableSharding("m103")
```

> Find one document from the products collection, to help us choose a shard key:

```
db.products.findOne()
```

> Create an index on sku:

```
db.products.createIndex( { "sku" : 1 } )
```

> Shard the products collection on sku:

```
sh.shardCollection("m103.products", {"sku" : 1 } )
```

> Checking the status of the sharded cluster:

```
sh.status()
```

### Chunks

Show collections in config database:

```
use config
show collections
```

Find one document from the chunks collection:
```
db.chunks.findOne()
```

Change the chunk size:
```
use config
db.settings.save({_id: "chunksize", value: 2})
```

Check the status of the sharded cluster:

```
sh.status()
```

Import the new products.part2 dataset into MongoDB:

```
mongoimport /dataset/products.part2.json --port 26000 -u "m103-admin" -p "m103-pass" --authenticationDatabase "admin" --db m103 --collection products
```

## Release History

* 0.2.1
    * CHANGE: Update docs (module code remains unchanged)
* 0.2.0
    * CHANGE: Remove `setDefaultXYZ()`
    * ADD: Add `init()`
* 0.1.1
    * FIX: Crash when calling `baz()` (Thanks @GenerousContributorName!)
* 0.1.0
    * The first proper release
    * CHANGE: Rename `foo()` to `bar()`
* 0.0.1
    * Work in progress

## Meta

Thiago Lima

Distributed under the XYZ license. See ``LICENSE`` for more information.

[https://github.com/thiagoblima/M103-Basic-Cluster-Administration/blob/master/LICENSE](https://github.com/thiagoblima/M103-Basic-Cluster-Administration/blob/master/LICENSE)

## Contributing

1. Fork it (<https://github.com/thiagoblima/M103-Basic-Cluster-Administrationfork>)
2. Create your feature branch (`git checkout -b feature/fooBar`)
3. Commit your changes (`git commit -am 'Add some fooBar'`)
4. Push to the branch (`git push origin feature/fooBar`)
5. Create a new Pull Request


[wiki]: https://github.com/thiagoblima/M103-Basic-Cluster-Administration/wiki