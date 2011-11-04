# Configuring the JDBC pool.

## Advanced configuration for BoneCP

The Play 2.0 JDBC datasource are managed by [[BoneCP | http://jolbox.com/]]. In addition of the classical `driver`, `url`, `user`, `password` configuration properties, it supports more tuning if you need it:

```properties
# The JDBC driver to use
db.default.driver=org.h2.Driver

# The JDBC url
db.default.url=jdbc:h2:mem:play

# Username
db.default.user=sa

# Password
db.default.password=secret

# Set default autocommit for a connection
db.default.autocommit=true

# Set default isolation level for a connection
db.default.isolation=READ_COMMITTED

# In order to reduce lock contention and thus improve performance, 
# each incoming connection request picks off a connection from a 
# pool that has thread-affinity. 
# The higher this number, the better your performance will be for the 
# case when you have plenty of short-lived threads. 
# Beyond a certain threshold, maintenence of these pools will start 
# to have a negative effect on performance (and only for the case 
# when connections on a partition start running out).
db.default.partitionCount=2

# The number of connections to create per partition. Setting this to 
# 5 with 3 partitions means you will have 15 unique connections to the 
# database. Note that BoneCP will not create all these connections in 
# one go but rather start off with minConnectionsPerPartition and 
# gradually increase connections as required.
db.default.maxConnectionsPerPartition=5

# The number of connections to start off with per partition.
db.default.minConnectionsPerPartition=5

# When the available connections are about to run out, BoneCP will 
# dynamically create new ones in batches. This property controls 
# how many new connections to create in one go (up to a maximum of 
# maxConnectionsPerPartition). Note: This is a per partition setting.
db.default.acquireIncrement=1

# After attempting to acquire a connection and failing, try to 
# connect these many times before giving up.
db.default.acquireRetryAttempts=10

# Sets the number of ms to wait before attempting to obtain a 
# connection again after a failure.
db.default.acquireRetryDelay=5000

# Sets the maximum time (in milliseconds) to wait before a call 
# to getConnection is timed out.
db.default.connectionTimeout=1000

# Sets Idle max age (in seconds).
db.default.idleMaxAge=60

# Specifies an initial SQL statement that is run only when 
# a connection is first created.
db.default.initSQL=SELECT 1

# If enabled, log SQL statements being executed.
db.default.logStatements=false

# Sets the maxConnectionAge in seconds.
db.default.maxConnectionAge=60
```