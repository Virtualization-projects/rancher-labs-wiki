### This applies to Rancher 1.6 only!

By default Rancher.io launches using an embedded H2 database.  This is for simplicity as this approach is sufficient for small deployments of Rancher.  To switch to MySQL the following [[Settings|Settings.md]] must be set.

```
db.cattle.database=mysql
db.cattle.username=cattle
db.cattle.password=cattle
db.cattle.mysql.host=localhost
db.cattle.mysql.port=3306
db.cattle.mysql.name=cattle
```

All values above are the defaults except `db.cattle.database`.  If the defaults are fine, then all you need to set is `db.cattle.database=mysql`.
