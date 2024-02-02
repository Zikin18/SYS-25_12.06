# Домашнее задание к занятию «Репликация и масштабирование. Часть 1» - Юрий Белобородов


### Задание 1

На лекции рассматривались режимы репликации master-slave, master-master, опишите их различия.

*Ответить в свободной форме.*

Ответ:

В режиме master-slave, slave сервер получает все данные от master сервера. В таком варианте нагрузка по чтению и записи данных разпределяется по разным серверам. Запись (а возможно и чтение) происходит на master сервер, а получение данных с slave сервера/ов. 

В режиме master-master на обоих серверах может происходить как запись так и чтение и соответственно оба сервера отправляют друг-другу информацио о внесенных в них изменениях.


### Задание 2

Выполните конфигурацию master-slave репликации, примером можно пользоваться из лекции.

*Приложите скриншоты конфигурации, выполнения работы: состояния и режимы работы серверов.*

Ответ:

Конфигурация MASTER
```
    [mysqld]
    server-id = 1
    log_bin = mysql-bin
    skip-host-cache
    skip-name-resolve
    datadir = /var/lib/mysql
    socket = /var/run/mysqld/mysqld.socket
    secure-file-priv=/var/lib/mysql-files
    user=mysql
    pid-file = /var/run/mysqld/mysqld.pid
    [client]
    socket = /var/run/mysqld/mysqld.socket
    !includedir /etc/mysql/conf.d/
```

Настраиваем MASTER

![2-1_master_settings.png](https://github.com/Zikin18/SYS-25_12.06/blob/master/2-1_master_settings.png)

Конфигурация SLAVE
```
    [mysqld]
    server-id = 2
    log_bin = mysql-bin
    relay-log=/var/lib/mysql/mysql-relay-bin
    relay-log-index=/var/lib/mysql/mysql-relay-bin.index
    read_only = 1
    skip-host-cache
    skip-name-resolve
    datadir = /var/lib/mysql
    socket = /var/run/mysqld/mysqld.socket
    secure-file-priv=/var/lib/mysql-files
    user=mysql
    pid-file = /var/run/mysqld/mysqld.pid
    [client]
    socket = /var/run/mysqld/mysqld.socket
    !includedir /etc/mysql/conf.d/
```

Настраиваем SLAVE

![2-2_slave_settings.png](https://github.com/Zikin18/SYS-25_12.06/blob/master/2-2_slave_settings.png)

Статус SLAVE

![2-3_slave_status_joined.png](https://github.com/Zikin18/SYS-25_12.06/blob/master/2-3_slave_status_joined.png)

Для проверки создадим тестовую базу данных с одной таблицей на мастере

![2-4_master_createtable.png](https://github.com/Zikin18/SYS-25_12.06/blob/master/2-4_master_createtable.png)

Как видно позиция сместилась на `1095`

И посмотрим изменения на SLAVE

![2-5_slave_status_after_joined.png](https://github.com/Zikin18/SYS-25_12.06/blob/master/2-5_slave_status_after_joined.png)

После создания базы данных и одной талицы на master база данных так же появилась и на slave.
Из команды `SHOW SLAVE STATUS\G;` так же видно что позиция на master сместилась на 1095.
