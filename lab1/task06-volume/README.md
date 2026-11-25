# MySQL с постоянным хранилищем

## Создание тома

```text
$ docker volume create mysql-data
mysql-data

$ docker volume ls (фильтр по имени)
DRIVER    VOLUME NAME
local     mysql-data
```

## Запуск MySQL

Для Apple Silicon использован образ `mysql:8.0` на `linux/arm64`, как указано в задании. Пароль передан через переменную `MYSQL_ROOT_PASSWORD`.

```text
$ docker run -d --name mysql-container -e MYSQL_ROOT_PASSWORD \
    -v mysql-data:/var/lib/mysql mysql:8.0
```

```text
$ docker ps (фильтр по имени)
NAMES             IMAGE       STATUS
mysql-container   mysql:8.0   Up 24 seconds
```

Проверка подключения тома через `docker inspect` показала `volume mysql-data -> /var/lib/mysql (rw=true)`. MySQL хранит данные в `/var/lib/mysql` на томе `mysql-data`.

## Проверка готовности

Запрос `SELECT VERSION()` внутри контейнера вернул `8.0.46`. В последних пяти строках журнала MySQL сообщил `ready for connections` на порту `3306`.

```text
$ docker logs mysql-container 2>&1 | tail -n 5
2026-11-26T16:05:54.274677Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2026-11-26T16:05:54.274698Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
2026-11-26T16:05:54.275681Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
2026-11-26T16:05:54.282670Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Bind-address: '::' port: 33060, socket: /var/run/mysqld/mysqlx.sock
2026-11-26T16:05:54.282705Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.46'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server - GPL.
```

Контейнер и том оставлены для проверки сохранности базы в задании 7.

<div><hr></div>

# Задание 6. Тома: MySQL с постоянным хранилищем

**Что делаем:** запускаем базу данных MySQL с томом, чтобы данные
жили отдельно от контейнера.

**Зачем:** контейнер — временный: удалил — и всё внутри пропало.
Том (volume) хранит данные на диске Docker'а независимо от контейнера.

## Шаг 1. Создайте том

```bash
docker volume create mysql-data
docker volume ls
```

## Шаг 2. Запустите MySQL с подключённым томом

```bash
docker run -d \
  --name mysql-container \
  -e MYSQL_ROOT_PASSWORD=secret \
  -v mysql-data:/var/lib/mysql \
  mysql:5.7
```

> **Владельцам Mac на Apple Silicon (M1/M2/M3/M4):** у образа `mysql:5.7`
> нет сборки под ARM, Docker ответит `no matching manifest for
> linux/arm64/v8`. **Решение: используйте `mysql:8.0`** — есть нативная
> ARM-сборка. (Вариант `--platform linux/amd64` с 5.7 технически работает,
> но под эмуляцией всё стартует по несколько минут — на паре это
> непрактично.)
>
> На Windows/Linux с Intel/AMD флаги не нужны, `mysql:5.7` подойдёт.

Разбор:
- `-e MYSQL_ROOT_PASSWORD=secret` — переменная окружения: пароль root в MySQL;
- `-v mysql-data:/var/lib/mysql` — том `mysql-data` подключён в каталог,
  где MySQL хранит данные.

## Шаг 3. Проверьте, что контейнер жив

```bash
docker ps
docker logs mysql-container | tail -5
```

В логах должно быть `ready for connections`.

## Результат

**В отчёт:** вывод `docker volume ls` и последние строки логов.

> Базу создадим в следующем задании — и проверим, что она переживёт
> удаление контейнера.
