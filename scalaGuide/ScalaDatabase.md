# Accessing an SQL database

## Configuring JDBC connection pools

Play 2.0 provides a plugin for managing JDBC connection pools. You can configure as many databases you need.

To enable the database plugin, configure a connection pool in the `conf/application.conf` file. By convention the default JDBC data source must be called `default`:

```properties
# Default database configuration
db.default.driver=org.h2.Driver
db.default.url=jdbc:h2:mem:play
```

To configure several data sources:

```properties
# Orders database
db.orders.driver=org.h2.Driver
db.orders.url=jdbc:h2:mem:orders

# Customers database
db.customers.driver=org.h2.Driver
db.customers.url=jdbc:h2:mem:customers
```

If something isn’t properly configured you will be notified directly in your browser:

[[dbError.png]]

## Accessing the JDBC datasource

The `play.api.db` package provides access to the configured data sources:

```scala
import play.api.db._

val ds = DB.getDatasource()
```

## Obtaining a JDBC connection

There is several ways to retrieve a JDBC connection. The first is the most simple:

```
val connection = DB.getConnection()
```

But of course you need to call `close()` at some point on the opened connection to put it back to the connection pool. Another way is to let Play manage the connection close for you:

```
DB.withConnection { conn =>
  // do whatever you need with the connection
}
```

The connection will be automatically closed at the end of the block.

> **Tip:** Not only that but all `Statement` and `ResultSet` created with this connection will be closed as well.

A variant is to set the connection autocommit to `false` automatically and to manage a transaction for the block:

```
DB.withTransaction { conn =>
  // do whatever you need with the connection
}
```

> **Next:** [[Using Anorm to access your database | ScalaAnorm]]