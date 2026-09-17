
## Installing MongoDB

MongoDB is a source-available cross-platform document-oriented database program. Classified as a NoSQL database program, MongoDB uses JSON-like documents with optional schemas. MongoDB is developed by MongoDB Inc.

Goto : [MongoDB Installation Docs](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/) for installation process.

**If error: [mongod.service could not found] occurs after installation, try the following command below.**

`$ sudo systemctl unmask mongodb`

`$ sudo systemctl start mongod`

**NOTE: error: could not compile `mongodb` might occure due to low memory on 1GB instance without sawp memory.**

## Configuration

MongoDB configuration file is located in `/etc/mongod.conf`.

### Limit MongoDB Cache Size

MongoDB, in its default configuration, will use will use the larger of either 256 MB or ½ of (ram – 1 GB) for its cache size. You can limit the MongoDB cache size by adding the `cacheSizeGB` argument to the `/etc/mongod.conf` configuration file

_NOTE: MongoDB uses both its internal cache and the system’s file system cache. So limiting it in one place will cut make it consume more in the other._

Add following lines of code to the `/etc/mongod.conf` to limit the memory consumption:

```yaml
# engine:
wiredTiger:
  engineConfig:
    cacheSizeGB: 1
```

_NOTE: Make sure you copy & paste the code in exactly, otherwise `mongod` will through error!_

## Convert a Standalone `mongod` Instance to a Replica Set

**Remarks:** MongoDB's ACID transactions doesn't work for the standalone mode. Therefore we need at least one replica set to be ACID complaint.

Follow [this link](https://www.mongodb.com/docs/manual/tutorial/convert-standalone-to-replica-set/) on how to convert to Replica Set.

**NOTE:** Make sure to add appropriate `--dbpath` i.e., `/var/lib/mongodb/` and `--bind-ip` i.e., `127.0.0.1`

For the general understanding on Replication follow [this link](https://www.mongodb.com/docs/manual/replication/).

### Quick Setup

Visit [this link](https://hevodata.com/learn/mongodb-replica-set-config/) for more on replica set configuration.

Now stop the MongoDB and make changes to the `.service` file by running `sudo nano /lib/systemd/system/mongod.service`.

Find the following line:

```
ExecStart=/usr/bin/mongod --config /etc/mongod.conf
```

Replace it with the line below:

```
ExecStart=/usr/bin/mongod --port 27017 --dbpath /var/lib/mongodb/ --replSet rs0 --bind_ip 127.0.0.1 --config /etc/mongod.conf
```

And run: `sudo systemctl daemon-reload && sudo systemctl start mongod`

**NOTE:** In case of Error: 100; run `sudo chown -R mongodb:mongodb /var/lib/mongodb/*` and restart MongoDB. If that doesn't solve the issue then run `sudo mongod` and see the error message. if it's the `/tmp/mongodb-*.sock` file the run the following command.

```bash
sudo chown mongodb:mongodb /tmp/mongodb-*.sock
sudo chmod 755 /tmp/mongodb-*.sock
```

To enable **MongoDB Replica Set** goto `mongosh` and run:

```
rs.initiate()
```

## Common Queries

List all databases:

```
show databases
```

Switch to a database:

```
use <db_name>
```

Show collections on a selected database:

```
show collections
```

Documents count based on a query:

```
db.<collection>.find({"<key>": "<value>"}).count()
```

For more accurate counts:

```
db.<collection>.countDocuments({"<key>": "<value>"}).count()
```

Show all documents of a collection:

```
db.<collection>.find()
```

Update single documents specified field based on query:

```
db.<collection>.update({"<key>": "<value>"}, {"$set": {"<key>": "<value>"}})
```

Update many documents specified field based on query:

```
db.<collection>.updateMany({"<key>": "<value>"}, {"$set": {"<key>": "<value>"}})
```

## Indexes

To create an index for a particular collection run:

```
db.<collection-name>.createIndex({ <uuid>: 1})
```

Here `1` means ascending and `-1` means descending order.

To view available index on a collection run:

```
db.<collection-name>.getIndexes()
```

## Free up Spaces

By default MongoDB's WireTiger engine hold the space for the deleted documents to use in future. To release these empty space from a particular collection run:

```
db.runCommand({compact: <collection-name>})
```

## Import and Export a MongoDB Database for a Collection

```
mongoimport --db <database_name> --collection <name> --file data.json
mongoexport --db <database_name> --collection <name> --out data.json
```

## Import and Export an Entire MongoDB Database

To create backup of database in MongoDB, you should use mongodump command. This command will dump the entire data of your server into the dump directory. `dump` directory will be created on the current working directory. There are many options available by which you can limit the amount of data or create backup of your remote server.

To create a backup run:

`$ mongodump`

_You don't have to log in to `mongo` shell for this command. `mongodump` is a binary comes with mongo installation._

To restore a backup run:

`$ mongorestore`

**NOTE: Check the `dump` directory timeStamp before restore to make sure you are restoring the right backup.**

**IMPORTANT: Not tested yet. Perhaps this command shoud run on the directory where `dump` folder exists or inside the `dump` directory.**