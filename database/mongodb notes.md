## replication
multiple db servers share the same data -> redundancy, load balance

## replica set
cluster of mongodb servers that do replication and [[#automated failover]]

### automated failover
redirect to replicas when main server down

# mongod
Primary Daemon, handles data requests, manages data access, and performs background management operations.