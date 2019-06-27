---
layout : post
title : MySql Master Slave
comments: true
categories : [MySql]
---

mysql 5.7

### How to do

#### Prerequisite

1. The version of master and slave are the same or similar(e.g. 5.7.24 and 5.7.26)
2. The data and structure of master and slave are the same

#### Master config

##### Modify mysql config file

```
[mysqld]
log-bin=mysql-bin
server-id=1
binlog_do_db=db1 #replicate specified database
```

##### Create replication user

```
CREATE USER 'replication'@'<slave-ip>' IDENTIFIED BY '<password>';

GRANT REPLICATION SLAVE ON *.* TO 'replication'@'<slave-ip>';
```

#### Slave config

##### Modify mysql config file

```
[mysqld]
server-id=2
read_only=1 # make slave server read-only
```

##### Config master info

1. In master server, execute `show master status`, get `binlog File` and `Position`
2. In slave server, run following statement:

```
CHANGE MASTER TO MASTER_HOST='<master ip>', MASTER_USER='replication', MASTER_PASSWORD='<password>', MASTER_LOG_FILE='<master-bin-log-file-name>', MASTER_LOG_POS=<master bin log position>;
```

`MASTER_LOG_POS` can be 0 which means read from the beginning of the bin log.

`MASTER_LOG_FILE` has to be got from master server initially, after set, if master server restart, slave server will update this value
automatically.

##### Start salve

```
START SLAVE;
```

##### Check slave status

```
show slave status;
```

Check `Slave_IO_State`, `Slave_IO_Running`, `Slave_SQL_Running`, `Seconds_Behind_Master`

##### Delay replication

`CHANGE MASTER TO MASTER_DELAY = N;`

`N` is in second.

It only affects the **SQL thread**, `I/O thread` already write the data to relay log.

#### Error

For replication error, please see `.err` for the detail.

### Switch slave server to master server

1. go to slave server
2. `mysql> STOP SLAVE;`
3. `mysql> RESET SLAVE ALL;` clear replication info such that it will not replicate when restart
4. comment `read_only` if it is set
5. restart mysql

If system use IP to access db, then application need to modify the IP in this this case.

A better solution is use DNS, only need to update DNS and clear DNS cache, there is no need to change application.

### How it works

To Do
