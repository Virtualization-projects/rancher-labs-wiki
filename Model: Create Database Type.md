# Create Database Type

Database schema manipulation is done on MySQL and then exported and translated to all other database.  Please setup MySQL if you haven't in your development environment already.

## Create Table

The schema of Rancher is intended to have a very basic style to it.  As such, most of the SQL is pretty straight forward and boilerplate.  To create a table a simple helper script exists.  In `cattle/resources/content/db/mysql` create a script, for example `create-table.sh` with the following content.

```sh
#!/bin/bash

. tables-common.sh

start host
string uri
bigint compute_free
bigint compute_total
ref agent
ref zone
end host
```

Run that script and it will output the SQL to create the table, for example

    ./create-table.sh | mysql -u root cattle

## Manually Modify Schema

Using some tool or just manually with `mysql` CLI modify the table as you see fit.

## Generate Liquibase Change Set

Run Rancher once against a MySQL database named `cattle_base`.  Do this by setting `db.cattle.mysql.name=cattle_base`.  The easiest way to do this is to modify your launch configuration in Eclipse and put a `-D` arguement for the JVM.  This will install the latest packaged schema to `cattle_base`.

Now run `cattle/resources/content/db/liquibase-dump.sh`.  This script will generate a change set and store it in `dump.xml`.  Rename `dump.xml` to `core-XXX.xml` and edit `changelog.xml` to point to it.

If you don't see column changes check whether you're running java 1.8 instead of java 1.7.

Now drop all the tables by running `cattle/resources/content/db/mysql/drop_tables.sh`.  This script will create a MySQL dump if you need to revert back.  Make sure you change `db.cattle.mysql.name` back to `cattle`.

Now refresh your workspace and start Rancher again.  Rancher should now deploy the correct schema.

## Generate jOOQ Model

Run `cattle/code/iaas/model/codegen.sh`.  This will generate all the POJOs needed for jOOQ.  This will also create the REST API.  Refresh your workspace and start Rancher and go to /v1 and see if your new type is in the API.

NOTE: Running the codegen.sh script will have problems running using java 1.8 instead of 1.7