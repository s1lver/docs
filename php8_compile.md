# Сборка PHP 8.0.* из исходного кода

Получим последний дистрибутив.

```bash
wget https://www.php.net/distributions/php-8.1.6.tar.gz -O php-8.1.6.tar.gz
```

Распаковываем скачанный архив.

```bash
tar -zxvf php-8.1.6.tar.gz
```

Переходим в каталог с распакованным архивом.

```bash
cd php-8.1.6
```

## Установка дополнительных пакетов
Возможно, будет необходимо поставить дополнительные пакеты

### Ubuntu
```
apt install libsodium-dev libonig-dev
```

### CentOS

## Конфигурируем

./configure --prefix=/usr/local/php8 --with-config-file-path=/etc/php8/conf --with-fpm-user=nginx --with-fpm-group=nginx --enable-fpm --enable-mysqlnd --enable-mbstring --enable-sockets --enable-opcache --with-openssl --with-curl --with-mysql-sock=/var/run/mysqld/mysqld.sock --with-mysqli=mysqlnd --with-pdo-mysql --with-pdo-pgsql --with-bz2 --enable-intl --enable-gd --with-jpeg=/usr --with-webp=/usr --with-xpm=/usr --without-sqlite3 --without-pdo-sqlite --enable-pcntl --without-pear --with-readline --enable-soap --with-imap=/usr/local/imap-2000b --with-kerberos --with-imap-ssl --with-sodium --with-zip --with-zlib