# Проверка установки Docker и запуск hello-world

## Проверка установки

`docker version` подтвердил доступность клиента Docker 29.9.3 (darwin/arm64) и сервера Docker Engine 29.9.3 (linux/arm64). `docker compose version` вернул v5.7.1.

## Запуск контейнера. Вывод команды

`docker run --name ipr-lab1-task01-hello hello-world`. При запуске также использован `--cidfile` для записи ID.

```text
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
58dee6a49ef1: Pulling fs layer
c3bdf82c34d1: Download complete
58dee6a49ef1: Download complete
58dee6a49ef1: Pull complete
Digest: sha256:1e4c05b897a751332ce85c7e9449da2d93514ea2dea6dbb0e2753c9e949b2bfa
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (arm64v8)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

## Проверка контейнера и образа

```text
$ docker ps --filter 'name=^/ipr-lab1-task01-hello$' --format 'table {{.Names}}\t{{.Status}}'
NAMES     STATUS

$ docker ps -a --filter 'name=^/ipr-lab1-task01-hello$' --format 'table {{.Names}}\t{{.Status}}'
NAMES                   STATUS
ipr-lab1-task01-hello   Exited (0) 17 seconds ago

$ docker image ls hello-world --format 'table {{.Repository}}\t{{.Tag}}\t{{.ID}}'
REPOSITORY    TAG       IMAGE ID
hello-world   latest    1e4c05b897a7
```

## Ответы на вопросы

1. Docker проверил локальное хранилище, скачал образ и создал из него контейнер.
2. Образ скачан из Docker Hub, из официального репозитория `docker.io/library/hello-world` с тегом `latest`.
3. После вывода сообщения контейнер завершился с кодом 0. `docker ps` показывает работающие контейнеры. Флаг `-a` включает и остановленные, поэтому контейнер виден со статусом `Exited (0)`.

<div><hr></div>

# Задание 1. Проверка установки и hello-world

**Что делаем:** убеждаемся, что Docker работает, и запускаем первый контейнер.

## Шаг 1. Проверьте Docker

```bash
docker version
docker compose version
```

Должны увидеть версии клиента и сервера. Если ошибка — запустите
Docker Desktop.

## Шаг 2. Запустите hello-world

```bash
docker run hello-world
```

Прочитайте вывод внимательно — там Docker сам объясняет, что произошло.

## Шаг 3. Ответьте письменно (это и есть отчёт по заданию)

1. Что Docker сделал, когда образа `hello-world` не оказалось на машине?
2. Где он его взял?
3. Контейнер после вывода сообщения продолжил работать или завершился?
   Проверьте: `docker ps` и `docker ps -a` — в чём разница вывода?

## Шаг 4. Найдите образ в локальном хранилище

```bash
docker images
```

## Результат

**В отчёт:** вывод `docker run hello-world` и ответы на три вопроса
из шага 3.
