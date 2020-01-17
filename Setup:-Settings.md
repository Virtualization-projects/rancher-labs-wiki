This applies to Rancher 1.6 only!
===

Configuring Rancher
==================

The configuration of Rancher is very flexible.  Under the hood it is based on Netflix's [Archaius](https://github.com/Netflix/archaius) which is in turn based on [Apache Commons Configuration](http://commons.apache.org/proper/commons-configuration/).  The configuration of Rancher is essentially key value based.  The key values pairs will be read from the following sources.  If the configuration is found in the source it will not continue to look for it in further sources.  The order is as below:

1. Environment variables
2. Java system properties
3. `cattle-local.properties` on the classpath
4. `cattle.properties` on the classpath
5. The database from cattle.setting table

The current configuration can be viewed from and modified from the API at http://localhost:8080/v1/settings.  Any changes done using the API will saved in the database and immediately refreshed on all Rancher services.  If you have overridden the settings using environment varibles, Java system properties, or local config files, the changes to the database will not matter as the configuration is not coming from the database.  Refer to the "source" property in the API to determine from where the configuration is being read.

## Environment Variables

Environment variables have a special format.  Configuration keys in Rancher are dot separated names like `db.cattle.database`.  Environment variables can not have dots in their name so `.` is replaced by `_`.  Additionally, in order to not read all the environment variables available and only read the environment variables specific to Rancher, the environment variable must start with `CATTLE`.  The `CATTLE_` prefix will be stripped.  To sum this all up, `db.cattle.database`, if set through an environment variable, must be `CATTLE_DB_CATTLE_DATABASE`.

## Why are settings CATTLE and not RANCHER?

The main orchestration engine is called [Cattle](../WhatIsCattle.md) and the configuration conventions started with that component and just continued throughout the system.
