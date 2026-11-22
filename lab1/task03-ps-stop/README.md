# Фоновый контейнер nginx, stop и rm

## Запуск nginx

```text
$ docker run -d --name ipr-lab1-task03-nginx -p 127.0.0.1::80 nginx
```

Флаг `-d` запустил контейнер в фоне. Порт 8080 уже был занят, поэтому Docker назначил свободный порт `127.0.0.1:49696` на хосте для порта `80/tcp` контейнера.

## Проверка до остановки

```text
$ docker ps (фильтр по имени)
NAMES                   IMAGE     STATUS         PORTS
ipr-lab1-task03-nginx   nginx     Up 7 seconds   127.0.0.1:49696->80/tcp

$ curl --noproxy '*' -fsS --connect-timeout 3 --max-time 5 \
    -w '\nHTTP %{http_code}\n' 'http://127.0.0.1:49696/'
<title>Welcome to nginx!</title>
HTTP 200
```

В `docker ps` контейнер был в статусе `Up`. В отчёте приведены заголовок страницы и код ответа. Полный HTML записан в `results.txt`.

## Остановка и удаление

```text
$ docker stop ipr-lab1-task03-nginx
ipr-lab1-task03-nginx

$ docker ps (фильтр по имени)
NAMES     IMAGE     STATUS    PORTS

$ docker ps -a (фильтр по имени)
NAMES                   IMAGE     STATUS                     PORTS
ipr-lab1-task03-nginx   nginx     Exited (0) 8 seconds ago

$ curl --noproxy '*' -fsS --connect-timeout 3 --max-time 5 \
    -w '\nHTTP %{http_code}\n' 'http://127.0.0.1:49696/'
curl: (7) Failed to connect to 127.0.0.1 port 49696 after 0 ms: Couldn't connect to server

HTTP 000

$ docker rm ipr-lab1-task03-nginx
ipr-lab1-task03-nginx

$ docker ps -a (фильтр по имени)
NAMES     IMAGE     STATUS    PORTS
```

## Чем отличаются stop и rm

`docker stop` останавливает процессы контейнера. Имя, настройки и записываемый слой файловой системы сохраняются. Контейнер остаётся в `docker ps -a`, и его можно снова запустить через `docker start`.

`docker rm` удаляет контейнер вместе с его настройками и записываемым слоем. После этого контейнер исчезает из `docker ps -a`. Образ nginx и именованные тома сохраняются.

<div><hr></div>

# Задание 3. Фоновый контейнер, stop и rm

**Что делаем:** запускаем веб-сервер в фоне, учимся останавливать
и удалять контейнеры.

## Шаг 1. Запустите nginx в фоне

```bash
docker run -d --name web -p 8080:80 nginx
```

Разбор флагов:
- `-d` — фоновый режим;
- `--name web` — своё имя (иначе Docker придумает случайное);
- `-p 8080:80` — порт 80 контейнера доступен на порту 8080 машины.

## Шаг 2. Проверьте работу

```bash
docker ps
curl http://localhost:8080
```

В ответе — HTML страницы «Welcome to nginx!».

## Шаг 3. Остановите контейнер

```bash
docker stop web
docker ps
```

Проверьте, что сайт больше не отвечает: `curl http://localhost:8080`.

## Шаг 4. Удалите контейнер

```bash
docker rm web
docker ps -a
```

## Результат

**В отчёт:** вывод `docker ps` до остановки и ответ на вопрос:
чем `docker stop` отличается от `docker rm`? Что останется после stop,
но исчезнет после rm?
