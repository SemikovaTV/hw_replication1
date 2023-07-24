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

Настройки на master:
```bash
mkdir -p /var/log/mysql
chown mysql: /var/log/m

nano /etc/mysql/mysql.conf.d/mysqld.cnf
```


*Приложите скриншоты конфигурации, выполнения работы: состояния и режимы работы серверов.*
