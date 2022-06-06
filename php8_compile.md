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