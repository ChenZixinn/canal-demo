### 项目简介
利用canal实现redis与mysql数据双写一致


### mysql设置

##### 1、开启binlog
修改my.cnf,添加一下内容
```shell
log-bin=mysql-bin #开启 binlog
binlog-format=ROW #选择 ROW 模式
server_id=1    #配置MySQL replaction需要定义，不要和canal的 slaveId重复
```

***ROW模式*** 除了记录sql语句之外，还会记录每个字段的变化情况，能够清楚的记录每行数据的变化历史，但会占用较多的空间。
    
**STATEMENT模式**只记录了sql语句，但是没有记录上下文信息，在进行数据恢复的时候可能会导致数据的丢失情况；

**MIX模式**比较灵活的记录，理论上说当遇到了表结构变更的时候，就会记录为statement模式。当遇到了数据更新或者删除情况下就会变为row模式；


```mysql
SHOW VARIABLES LIKE "log_bin"; # 输出NO为打开
```

##### 2、新建canal用户
```mysql
 
DROP USER IF EXISTS 'canal'@'%';
CREATE USER 'canal'@'%' IDENTIFIED BY 'canal';  
GRANT ALL PRIVILEGES ON *.* TO 'canal'@'%' IDENTIFIED BY 'canal';  
FLUSH PRIVILEGES;
 
SELECT * FROM mysql.user;
```


### canal配置

##### 1、修改配置文件
修改/canal/conf/example/instance.properties文件
```
canal.instance.master.address=192.168.111.1:3306
```