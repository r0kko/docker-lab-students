# Сохранение базы MySQL после пересоздания контейнера

## Создание базы

В контейнере MySQL из задания 6 создана база `testdb`. Запрос `SHOW DATABASES` подтвердил, что она появилась в списке.

```text
mysql> CREATE DATABASE testdb;

Перед удалением
da024b7ce77e running mysql:8.0 mysql-data -> /var/lib/mysql
```

## Удаление и повторный запуск

Старый контейнер штатно остановлен через `docker stop` и удалён через `docker rm`. После удаления `docker ps -a` с фильтром по имени показал пустой список, а `docker volume inspect` по-прежнему показал том `mysql-data`.

```text
$ docker ps -a (фильтр по имени)
NAMES     STATUS
```

Новый контейнер запущен из того же локального образа `mysql:8.0` с томом `mysql-data`. Идентификатор контейнера изменился.

```text
После пересоздания
980c737dab59 running mysql:8.0 mysql-data -> /var/lib/mysql
```

## Проверка данных

```text
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| testdb             |
+--------------------+
```

Список баз совпал до и после пересоздания. `testdb` сохранилась в томе, который подключён к `/var/lib/mysql` внутри контейнера.

По данным `docker volume inspect mysql-data` файлы тома лежат в `/var/lib/docker/volumes/mysql-data/_data` в Linux-ВМ Docker Desktop.

<div><hr></div>

# Задание 7. Данные переживают контейнер

**Что делаем:** создаём базу в MySQL, **удаляем контейнер**, запускаем
новый — и проверяем, что база на месте.

## Шаг 1. Создайте базу данных

Подключитесь к MySQL внутри контейнера (пароль — `secret`):

```bash
docker exec -it mysql-container mysql -uroot -p
```

Внутри MySQL:

```sql
CREATE DATABASE testdb;
SHOW DATABASES;
EXIT;
```

## Шаг 2. Удалите контейнер

```bash
docker rm -f mysql-container
docker ps -a
```

Контейнера больше нет. Если бы том не был подключён, данные пропали бы
вместе с ним.

## Шаг 3. Запустите новый контейнер с тем же томом

```bash
docker run -d \
  --name mysql-container \
  -e MYSQL_ROOT_PASSWORD=secret \
  -v mysql-data:/var/lib/mysql \
  mysql:5.7
```

## Шаг 4. Проверьте, что база сохранилась

```bash
docker exec -it mysql-container mysql -uroot -p
```

```sql
SHOW DATABASES;
```

`testdb` на месте, хотя контейнер — совершенно новый.

## Результат

**В отчёт:** вывод `SHOW DATABASES` после пересоздания контейнера
и ответ на вопрос: где физически лежат данные (подсказка:
`docker volume inspect mysql-data`)?
