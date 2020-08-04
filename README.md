# M103-Basic-Cluster-Administration
> MongoDB University course for basic cluster management.

Basic MongoDB cluster operations.

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