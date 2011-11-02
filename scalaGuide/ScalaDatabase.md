# Accessing an SQL database

## Configuring JDBC connection pools

Play 2.0 provides a plugin able to manage JDBC connection pools. You can configure as many databases you need.

To enable the database plugin, configure a connection pool in the `conf/application.conf` file. By convention the default JDBC datasource must be called `default`:

```properties
# Default database configuration
db.default.driver=org.h2.Driver
db.default.url=jdbc:h2:mem:play
```

To configure several datasources:

```properties
# Orders database
db.orders.driver=org.h2.Driver
db.orders.url=jdbc:h2:mem:orders

# Customers database
db.customers.driver=org.h2.Driver
db.customers.url=jdbc:h2:mem:customers
```

If something isn't properly configured you will be notified right into your browser:
[[dbError.png]]


## Accessing the JDBC datasource

The `play.api.db` package provide access to the configured datasources:

```scala
import play.api.db._

val ds = DB.getDatasource()
```

## Integrating with a DataBase access layer

From here you can integrate any JDBC access layer that need a JDBC datasource. For example to integrate with [[ScalaQuery | https://github.com/szeiger/scala-query]]:

```scala
import play.api.db._
import play.api.Play.current

import org.scalaquery.ql._
import org.scalaquery.ql.TypeMapper._
import org.scalaquery.ql.extended.{ExtendedTable => Table}

import org.scalaquery.ql.extended.H2Driver.Implicit._ 

import org.scalaquery.session._

object Task extends Table[(Long, String, Date, Boolean)]("tasks") {
    
  lazy val database = Database.forDataSource(DB.getDataSource())
  
  def id = column[Long]("id", O PrimaryKey, O AutoInc)
  def name = column[String]("name", O NotNull)
  def dueDate = column[Date]("due_date")
  def done = column[Boolean]("done")
  def * = id ~ name ~ dueDate ~ done
  
  def findAll = database.withSession { implicit db:Session =>
      (for(t <- this) yield t.id ~ t.name).list
  }
  
}
```