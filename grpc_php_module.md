# Сборка gRPC модуля для PHP

Получим последний дистрибутив

```bash
git clone -b v1.46.3 https://github.com/grpc/grpc
```

Переходим в каталог с исходным кодом.

```bash
cd grpc
```

Соберем gRPC C core library

```bash
git submodule update --init
EXTRA_DEFINES=GRPC_POSIX_FORK_ALLOW_PTHREAD_ATFORK make
```

Выбираем необходимый язык. В нашем случае - это PHP.

```bash
cd ./src/php/ext/grpc
```

## Инициализация

```bash
phpize
```

## Конфигурация

```bash
grpc_root="$(pwd)"
GRPC_LIB_SUBDIR=libs/opt ./configure --enable-grpc="${grpc_root}"
```

## Сборка и установка

```bash
make
[sudo] make install
```

## Обновим php.ini

Добавим в секцию расширений

```
extension=grpc.so
```