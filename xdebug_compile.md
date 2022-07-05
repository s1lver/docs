# Сборка XDebug

### Получаем последний дистрибутив

```bash
wget https://xdebug.org/files/xdebug-3.1.4.tgz
```

### Распаковываем

```bash
tar xvzf xdebug-3.1.4.tgz
```

### Переходим в папку

```bash
cd ./xdebug-3.1.4/
```

### Инициализируем

```bash
phpize
```

### Конфигурируем

```bash
./configure
```

Если установлено несколько версии PHP, то необходимо указать под какую именно конфигурируем (необходимо чтобы версии API не отличались). В данном случаем, PHP по умолчанию - 7.3, собираем под 8.0

```bash
./configure --with-php-config=/usr/bin/php-config8
```

### Собираем и устанавливаем

```bash
make -j$(nproc) && sudo make install
```

### Добавим расширение в php.ini

```ini
zend_extension=xdebug.so
```