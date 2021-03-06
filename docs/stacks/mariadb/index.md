# MariaDB stack documentation

MariaDB can be configured with the following [environment variables](https://github.com/wodby/mariadb#environment-variables)

## External access

There are two ways to connect to a MariaDB server externally: publish port or set up an SSH tunnel.  

### Publish port

Publish MariaDB's port (3306) from [stack configuration page](../../stacks/config.md#ports) to a dynamic node port and connect as:

```shell
mysql -h=[IP] -P[PORT] -u[USER] -p[PASSWORD] [DATABASE]
```

Where `[PORT]` is the generated node port (you can find it on a service page `App Instance > Stack > MariaDB`) and `[IP]` is the IP of the server where app instance deployed (or use the server hostname `node-[SERVER UUID].wod.by`).

### Set up tunnel

If you deploy MariaDB as a service inside of a stack that comes with an SSHD container, you can set up a secure tunnel:

1. Set up SSH tunnel on port `53306` (you can change it). You can find `[SSH Port]` on `Instance > Stack > SSH` page. For MariaDB (port `3306` by default) use the following command:    
    ```shell
    ssh -L 53306:mariadb:3306 -p [SSH Port] wodby@[Server IP] -N
    ```
2. Connect to the database via the tunnel on port `53306` (replace `[tokens]`):
    ```shell
    mysql --protocol=TCP -P53306 -u[USER] -p[PASSWORD] [DATABASE]
    ```

## Changelog

This changelog is for MariaDB stack on Wodby, to see image changes see tags description on [repository page](https://github.com/wodby/mariadb/releases).

### 2.4.1

MariaDB updates: 10.2.21, 10.3.12, ~~10.1.37~~ https://github.com/wodby/mariadb/issues/10

### 2.4.0

* Patch updates: 10.3.11, 10.2.19
* We now run `mysql_upgrade` automatically on stack upgrades      

### 2.3.4

MariaDB 10.0 `innodb_default_row_format` now set to `dynamic` by default

### 2.3.3

* MariaDB patch update 10.3.10
* Adminer: 
    * Bugfix: some `$PHP_` env vars were ignored
    * Default memory limit set to 512M
    * Adminer and Webgrind rebased to the latest php image

### 2.3.2

* MariaDB 
    * Patch update: 10.2.18
    * Improved performance for backup orchestration action
* Adminer:
    * Added the default list of plugins, enabled via `$ADMINER_PLUGINS`
    * You can now change Adminer design via `$ADMINER_DESIGN` z   
    * Updated to the latest stable PHP image

### 2.3.1

MariaDB patch update: 10.1.36

### 2.3.0

* MariaDB patch updates: 10.3.9, 10.2.17, 10.1.35
* Image rebased to Alpine Linux 3.8
* Backup action performance improvement: no intermediate file created
* Bugfix: triggers duplicated during db dump
* Bugfix: no privileges before import could cause failure
* ionice no longer used in orchestration actions 

### 2.2.0

* New version 10.3 added (10.3.7)
* MariaDB updates: 10.2.15, 10.1.34
* `optimizer_prune_level` and `optimizer_search_depth` are now configurable https://github.com/wodby/mariadb/issues/4
* ❗Default `innodb_buffer_pool_size` set to `128M` that should significantly decrease memory usage by MariaDB container. See stack documentation to learn how to calculate the optimal size of `innodb_buffer_pool_size` for your application
* Default `innodb_buffer_pool_instances` set to `1`

### 2.1.0

* Updated to 10.1.31, 10.2.12
* Rebased to Alpine Linux 3.7
* Default [memory request](../config.md#resources) set to 64m

### 2.0.1

* Restored MariaDB 10.1 `innodb_large_prefix` setting (enabled by default) removed in 2.0.0

### 2.0.0

* New MariaDB 10.2.11
* MariaDB updated to 10.1.29
* Shutdown grace period increased to 5 minutes
* Health check timeout increased to 30 seconds
* Deployment strategy no longer can be changed
* Optimized default values in `my.cnf`
* New environment variables to configure recovery options
* Default user/group in a container now `mysql`
* Backup action now runs with `nice` (10) and `ionice` (7)
* Improved error handling in import action

### 1.0.4

* MariaDB updated to 10.1.26

### 1.0.3

* Directory __MACOSX now excluded from import archive

### 1.0.2

* MariaDB now recovers privileges in case of an error during import

### 1.0.1

* Priveleges are now revoked for a regular user during import
* Bug fix: sometimes tables weren't ignored during backup

### 1.0.0

Initial release
