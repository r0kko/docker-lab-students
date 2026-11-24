# Сборка и запуск образа hello-docker

## Первая сборка и запуск

Образ собран из Dockerfile и `app.py` задания 4 для `linux/arm64`. Временная копия этих файлов использована как контекст сборки, `BUILD_DIR` — временный каталог для состояния Buildx.

```text
$ BUILDX_CONFIG="$BUILD_DIR" docker build --progress=plain -t hello-docker .
```

Проверка образа командой `docker image ls hello-docker` с выводом имени, тега, ID и размера.

```text
REPOSITORY     TAG       IMAGE ID       SIZE
hello-docker   latest    10d01170ed68   215MB

$ docker run --rm hello-docker
Hello, Docker!
```

## Пересборка и кэш

Во временной копии `app.py` текст вывода заменён на `Hello, Docker cache!`. Образ пересобран той же командой.

```text
#6 [2/3] WORKDIR /app
#6 CACHED

#7 [3/3] COPY app.py .
#7 DONE 0.0s

$ docker run --rm hello-docker
Hello, Docker cache!
```

`WORKDIR /app` взят из кэша. `COPY app.py .` выполнен заново, поскольку изменилось содержимое копируемого файла. Базовый образ `python:3.8-slim` остался тем же, что подтверждает совпадение digest в журнале сборки.

## Исходный вариант после эксперимента

После проверки кэша образ `hello-docker` пересобран с исходным `app.py`.

```text
$ docker run --rm hello-docker
Hello, Docker!
```

<div><hr></div>

# Задание 5. Сборка и запуск своего образа

**Что делаем:** собираем образ из Dockerfile задания 4 и запускаем
контейнер из него.

## Шаг 1. Перейдите в папку с Dockerfile

```bash
cd ../task04-dockerfile
```

## Шаг 2. Соберите образ

```bash
docker build -t hello-docker .
```

Разбор:
- `build` — собрать образ;
- `-t hello-docker` — дать образу имя (тег);
- `.` — контекст сборки: текущая папка (там ищутся Dockerfile и файлы
  для COPY).

Понаблюдайте за выводом: сборка идёт **слоями** — по слою на инструкцию.

## Шаг 3. Убедитесь, что образ появился

```bash
docker images
```

Найдите `hello-docker` и посмотрите его размер.

## Шаг 4. Запустите

```bash
docker run hello-docker
```

На экране — `Hello, Docker!`.

## Шаг 5 (эксперимент). Пересборка

Измените текст в `app.py` и соберите снова той же командой.
Обратите внимание: какие шаги сборки пошли заново, а какие взялись
из кэша (`CACHED`)? Запишите наблюдение.

## Результат

**В отчёт:** вывод `docker run hello-docker` и наблюдение про кэш
из шага 5.
