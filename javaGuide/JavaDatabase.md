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

The `play.db` package provide access to the configured datasources:

```java
import play.db.*;
import javax.sql.*;

DataSource ds = DB.getDatasource();
```