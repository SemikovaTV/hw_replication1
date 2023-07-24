# Домашнее задание к занятию «Репликация и масштабирование. Часть 1»  - Семикова Т.В. FOPS-9
### Задание 1

На лекции рассматривались режимы репликации master-slave, master-master, опишите их различия.

*Ответить в свободной форме.*

Режим репликации master-slave - это режим копирования данных с основоного сервера - master на резервный - slave.
При этом данные на резервном серевере доступны только для чтения, вносить изменения в них нельзя.
В случае отказа основного сервера резервный (или один из резервных) должен быть назначен основным.
Такой режим позволяет производить операции записи и чтения на основном сервере и только чтение на резервном.

Режим репликации master-master - режим копирования данных между равноправными серверами, в таком случае каждый из них доступен и для чтения и для записи, что позволяет производить операции чтения и зписи на любом из них.
Однако такой режим нежелателен в использовании, поскольку при изменении одной и той же записи на разных серверах может возникнуть конфликт и риск потери данных, поэтому он используется реже.

---

### Задание 2

Выполните конфигурацию master-slave репликации, примером можно пользоваться из лекции.

*Приложите скриншоты конфигурации, выполнения работы: состояния и режимы работы серверов.*

Настройки на master:
```bash
mkdir -p /var/log/mysql
chown mysql: /var/log/m
nano /etc/mysql/mysql.conf.d/mysqld.cnf
```
Настройки на slave:
```bash
nano /etc/mysql/mysql.conf.d/mysqld.cnf
```
![alt text](https://github.com/SemikovaTV/hw_replication1/blob/main/0.jpg)

Далее на master:
```bash
systemctl restart mysql.service

mysql -u root -p

mysql> CREATE USER 'replication'@'%';
Query OK, 0 rows affected (0,03 sec)

mysql> GRANT REPLICATION SLAVE ON *.* TO 'replication'@'%';
Query OK, 0 rows affected (0,03 sec)

mysql> SHOW MASTER STATUS;
+------------------+----------+--------------+------------------+-------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------+----------+--------------+------------------+-------------------+
| mysql-bin.000006 |     1257 |              |                  |                   |
+------------------+----------+--------------+------------------+-------------------+
1 row in set (0,00 sec)

mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
```
![alt text](https://github.com/SemikovaTV/hw_replication1/blob/main/1.jpg)

slave:
```bash
mysql> CHANGE MASTER TO MASTER_HOST='192.168.1.192', MASTER_USER='replication', MASTER_LOG_FI  LE='mysql-bin.000006',
 MASTER_LOG_POS=1257;
Query OK, 0 rows affected, 7 warnings (0,16 sec)

mysql> START SLAVE;
Query OK, 0 rows affected, 1 warning (0,08 sec)

Slave_IO_State: Waiting for source to send event
                  Master_Host: 192.168.1.192
                  Master_User: replication
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000006
          Read_Master_Log_Pos: 1257
               Relay_Log_File: mysql-relay.bin
                Relay_Log_Pos: 873
        Relay_Master_Log_File: mysql-bin.000006
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 1257
              Relay_Log_Space: 1076
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 1
                  Master_UUID: 9f9c1827-2174-11ee-bec1-080027f36be0
             Master_Info_File: mysql.slave_master_info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
      Slave_SQL_Running_State: Replica has read all relay log; waiting for more updates
           Master_Retry_Count: 86400
                  Master_Bind:
      Last_IO_Error_Timestamp:
     Last_SQL_Error_Timestamp:
               Master_SSL_Crl:
           Master_SSL_Crlpath:
           Retrieved_Gtid_Set:
            Executed_Gtid_Set:
                Auto_Position: 0
         Replicate_Rewrite_DB:
                 Channel_Name:
           Master_TLS_Version:
       Master_public_key_path:
        Get_master_public_key: 0
            Network_Namespace:
1 row in set, 1 warning (0,00 sec)
```
![alt text](https://github.com/SemikovaTV/hw_replication1/blob/main/2.jpg)
![alt text](https://github.com/SemikovaTV/hw_replication1/blob/main/3.jpg)
![alt text](https://github.com/SemikovaTV/hw_replication1/blob/main/4.jpg)
![alt text](https://github.com/SemikovaTV/hw_replication1/blob/main/5.jpg)

Далее произведем проверку - создадим на master базу REPLICATEST и проверим ее на slave:
![alt text](https://github.com/SemikovaTV/hw_replication1/blob/main/6.jpg)
