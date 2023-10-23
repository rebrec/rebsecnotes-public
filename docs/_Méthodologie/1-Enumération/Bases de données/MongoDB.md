---
public: true 
#Tags: vhost, hashes, lfi, password-spraying, potato
---

Port par défaut : 27017, 27018, 27019, 27020

Commandes :

```
$ mongo

> show dbs
> use <db>
> show collections
> db.<collection>.find().forEach(printjson); #Dump the collection
> db.<collection>.count() #Number of records of the collection
> db.current.find({"username":"admin"}) #Find in current db the username admin
> db.<collection>.update({"_id":ObjectId("61ce278f46e0fb0012d47ee4")}, {$set:{"attributeXXX":"valueXXX"}})'
```
