# Сборка PHP 8.0.* из исходного кода

Получим последний дистрибутив.

```bash
wget https://www.php.net/distributions/php-8.0.19.tar.gz -O php-8.0.19.tar.gz
```

Распаковываем скачанный архив.

```bash
tar -zxvf php-8.0.19.tar.gz
```

Переходим в каталог с распакованным архивом.

```bash
cd php-8.0.19
```

## Установка дополнительных пакетов
Возможно, будет необходимо поставить дополнительные пакеты

### Ubuntu
```
apt install libsodium-dev libonig-dev
```

### CentOS

## Конфигурирация

./configure --prefix=/usr/local/php80 --with-config-file-path=/etc/php80/conf --with-fpm-user=nginx --with-fpm-group=nginx --enable-fpm --enable-mysqlnd --enable-mbstring --enable-sockets --enable-opcache --with-openssl --with-curl --with-mysql-sock=/var/run/mysqld/mysqld.sock --with-mysqli=mysqlnd --with-pdo-mysql --with-pdo-pgsql --with-bz2 --enable-intl --enable-gd --with-jpeg=/usr --with-webp=/usr --with-xpm=/usr --without-sqlite3 --without-pdo-sqlite --enable-pcntl --without-pear --with-readline --enable-soap --with-imap=/usr/local/imap-2000b --with-kerberos --with-imap-ssl --with-sodium --with-zip --with-zlib

## Компиляция и установка
Переходим к компиляции и установке PHP.

```bash
make -j2 && make install
```

Создадим каталог для конф. файлов, заданный опцией --with-config-file-path=/etc/php80/conf. Сама опция не создает каталог при установке, она лишь указывает системе где искать php.ini, помимо него, в данном каталоге можно хранить и другие .conf-файлы.

```bash
mkdir -p /etc/php80/conf
```

Дополнительно создадим каталог в котором будут лежать файлы php-fpm пулов.

```bash
mkdir /etc/php80/pool
```

Теперь необходимо задать конфигурационные файлы, точнее, скопировать и переименовать уже готовые.
Из каталога с исходниками копируем файл php.ini.production, в каталог /etc/php80/conf, попутно переименовывая его в php.ini.

```bash
cp /usr/local/src/php-8.0.19/php.ini-production /etc/php80/conf/php.ini
```

Сюда же переносим файл php-fpm.conf.default с попутным переименованием в php-fpm.conf.

```bash
cp /usr/local/php80/etc/php-fpm.conf.default /etc/php80/conf/php-fpm.conf
```

Создадим пустую заготовку файла пула test.conf.

```bash
touch /etc/php80/pool/test.conf
```

Переходим к правке конф. файлов. Начнем с php.ini и внесем некоторые изменения

```bash
vim /etc/php80/conf/php.ini
```

```ini
######################################################################################
# Изменяем следующие значения
######################################################################################
; Отключим для безопасности некоторые функции
disable_functions = system, exec
; Максимальный объем памяти разрешенный использовать скрипту
memory_limit = 15M
; Максимально допустимый объем данных отправляемых методом POST
post_max_size = 13M
; Максимальный размер файла для закачки на сервер, посредством PHP
upload_max_filesize = 12M
; Включаем sql.safe_mode при соединении с базой данных 
sql.safe_mode = On
; Скрываем версию PHP в HTTP-заголовках
expose_php = Off
; Запрещаем выполнение удаленных скриптов
; отключается в целях безопасности, но может понадобится для работы
; некоторых плагинов WordPress, например CAPTCHA от Google
allow_url_fopen = Off
; следующее значение надо добавить в файл в подраздел [opcache]
; или просто в конец файла, включает Opcache
zend_extension = opcache.so
```

Редактируем php-fpm.conf.

```bash
vim /etc/php80/conf/php-fpm.conf
```

```ini
[global]
; Путь к PID-файлу
pid = /var/run/php-fpm.pid
; Путь к log-файлу
error_log = /var/log/php-fpm/error.log
; Уровень логирования ошибок
log_level = notice
emergency_restart_threshold = 0
emergency_restart_interval = 1m
process_control_timeout = 5s
daemonize = yes
events.mechanism = epoll
; Указываем каталог в котором следует искать файлы пулов
include=/etc/php80/pool/*.conf
```

Поскольку мы указали `/var/log/php-fpm` в качестве каталога для log-файлов, то его необходимо создать, в противном случае при запуске демона PHP будет выдаваться ошибка.

```bash
mkdir /var/log/php-fpm
```

Редактируем файл php-fpm пула test.conf.

```bash
vim /etc/php80/pool/test.conf
```

```ini
; Имя пула
[test]
; Пользователь и группа
user = nginx
group = nginx
; Прием FastCGI-запросов
listen = /var/run/php-fpm.sock
; Пользователь и группа от чьего имени запущен сервер
listen.owner = nginx
listen.group = nginx
; Права на чтение и запись
listen.mode = 0660
; создание дочерних процессов, по умолчанию dynamic
pm = dynamic
; максимальное число процессов
pm.max_children = 10
; число дочерних процессов при запуске
pm.start_servers = 1
; минимальное число неактивных процессов сервера
pm.min_spare_servers = 1
; максимальное число неактивных процессов сервера	
pm.max_spare_servers = 3
; число запросов дочернего процесса, после которого он будет перезапущен
pm.max_requests = 500
```

Создадим несколько ссылок на файлы в каталоге bin.

```bash
ln -s /usr/local/php80/bin/php /bin/php
ln -s /usr/local/php80/bin/php-cgi /bin/php-cgi
ln -s /usr/local/php80/bin/php-config /bin/php-config
ln -s /usr/local/php80/bin/phpdbg /bin/phpdbg
ln -s /usr/local/php80/bin/phpize /bin/phpize
```

Теперь необходимо создать системный юнит, который будет управлять запуском PHP. Создадим файл php80.service. Обратите внимание на такую деталь, дальнейшие команды для управления демоном PHP будут зависеть от имени данного файла.

```bash
vim /lib/systemd/system/php80.service
```

```ini
[Unit]
Description=The PHP 8.0 FastCGI Process Manager
After=network.target
 
[Service]
Type=simple
PIDFile=/var/run/php-fpm.pid
ExecStart=/usr/local/php80/sbin/php-fpm --nodaemonize --fpm-config /etc/php80/conf/php-fpm.conf
ExecReload=/bin/kill -USR2 $MAINPID
 
[Install]
WantedBy=multi-user.target
```

Добавляем службу php80.service в автозагрузку и запускаем ее.

```bash
systemctl enable php80
systemctl start php80
```

Проверим запустилась ли служба.

```bash
systemctl status php80
```