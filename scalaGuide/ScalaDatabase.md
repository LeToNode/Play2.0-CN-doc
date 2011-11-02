# Accessing an SQL database

## Configuring JDBC connection pools

Play 2.0 provides a plugin able to manage JDBC connection pools. You can configure as many databases you need.

To enable the `DBPlugin`, configure a connection pool in the `conf/application.conf` file. By convention the default JDBC datasource must be called `default`:

```properties
# Default database configuration
db.default.driver=org.h2.Driver
db.default.url=jdbc:h2:mem:play
```