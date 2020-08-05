# M103-Basic-Cluster-Administration
> MongoDB University course for basic cluster management.

Basic MongoDB cluster operations, this is a studying repository and the *answers aren't being shown here*, only import commands, the only reason of this repository is spreading knowledge.


![](assets/mongo_db_university.png)


## MongoDB University M103 - Basic Cluster Administration

> https://university.mongodb.com/courses/M103/about

## The Mongod

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