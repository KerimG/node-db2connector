# node-db2connector

Connect to remote DB2 on i (AS/400) for free.

## Motivation

AFAIK, connecting to a DB2 on i (AS/400) instance remotely requires a commercial db2connect license. Node solutions like the IBM provided(?) db2a conenctor (https://bitbucket.org/litmis/nodejs-idb-connector) or the node-ibm_db project (https://github.com/ibmdb/node-ibm_db) allow free DB2 connections when run on the same host the DB2 on i instance is running on.

This project intends to create a daemon (essentially a webservice) that listens for any remote connection and uses the above mentioned nodejs-idb-connector to then relay the SQL instructions to the DB2 on i instance.

Potential issues:

* Performance. It would be interesting to see how this solution compares performance-wise with the commercial db2connect one. I guess this solution will be somewhat slower due to potential overhead in the network protocol and whatever abstractions the idb-connector has. Even then, it might be useful to some folks out there.

## How is it supposed to work?

This project will run as a daemon on the AS/400 and listen for incoming requests similar to database connection requests on other systems:

```
client.connect(HOST, PORT, CREDENTIALS);
```

1. The daemon will do authentication
2. if successful, will open up an additional (web)socket to listen on
3. client and daemon will establish connection on new port (two-way stream)
4. client will send instructions through connection
5. daemon will relay instructions to idb-connector
6. idb-connector will execute instructions and return results/errors
7. daemon will send results/errors to client
