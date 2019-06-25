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
```

##### Config master info

1. In master server, execute `show master status`, get `binlog File` and `Position`
2. In slave server, run following statement:

```
CHANGE MASTER TO MASTER_HOST='<master ip>', MASTER_USER='replication', MASTER_PASSWORD='<password>', MASTER_LOG_FILE='<master-bin-log-file-name>', MASTER_LOG_POS=<master bin log position>;
```

##### Start salve

```
START SLAVE;
```

##### Check slave status

```
show slave status;
```

Check `Slave_IO_State`, `Slave_IO_Running`, `Slave_SQL_Running`, `Seconds_Behind_Master`

#### Error

For replication error, please see `.err` for the detail.

### How it works

To Do
