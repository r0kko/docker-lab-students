# Интерактивный контейнер Ubuntu

## Запуск контейнера

```text
$ docker run --name ipr-lab1-task02-ubuntu --env TERM=dumb -it ubuntu bash
```

Флаг `-i` оставляет стандартный ввод открытым, `-t` выделяет псевдотерминал. Внутри контейнера запустился `bash`. Команда `ls` показала каталоги Ubuntu, а `whoami` вернула `root`.

## Проверка операционной системы

```text
root@343b7d86bcf7:/# cat /etc/os-release
PRETTY_NAME="Ubuntu 26.04.1 LTS"
NAME="Ubuntu"
VERSION_ID="26.04"
VERSION="26.04.1 LTS (Resolute Raccoon)"
VERSION_CODENAME=resolute
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=resolute
LOGO=ubuntu-logo
```

В контейнере работает Ubuntu, а на хосте `sw_vers` показала macOS 27.2.1. Файл `/etc/os-release` описывает систему внутри контейнера.

## Завершение контейнера

```text
root@343b7d86bcf7:/# exit
exit

$ docker ps (фильтр по имени)
NAMES     STATUS

$ docker ps -a (фильтр по имени)
NAMES                    STATUS
ipr-lab1-task02-ubuntu   Exited (0) 14 seconds ago
```

После `exit` завершилась оболочка Bash, основная команда контейнера. Контейнер остановился с кодом `0`. `docker ps` показывает работающие контейнеры, а `docker ps -a` включает остановленные, поэтому контейнер виден только во втором списке.

<div><hr></div>

# Задание 2. Интерактивный контейнер

**Что делаем:** запускаем контейнер с Ubuntu и работаем внутри него,
как в обычной консоли Linux.

## Шаг 1. Запустите контейнер

```bash
docker run -it ubuntu bash
```

Разбор флагов:
- `-i` — интерактивный режим (держим stdin открытым);
- `-t` — выделяем псевдотерминал;
- `bash` — команда, которая запускается внутри.

Вы «провалитесь» внутрь контейнера: приглашение командной строки
сменится на `root@<id>:/#`.

## Шаг 2. Поработайте внутри

```bash
ls
whoami
cat /etc/os-release | head -2
```

Сравните вывод `cat /etc/os-release` внутри контейнера и на своей машине
(второй терминал) — это разные системы!

## Шаг 3. Выйдите

```bash
exit
```

## Шаг 4. Найдите завершившийся контейнер

```bash
docker ps -a
```

## Результат

**В отчёт:** вывод `cat /etc/os-release` из контейнера и ответ на вопрос:
что произошло с контейнером после `exit` — почему он виден в `docker ps -a`,
но не в `docker ps`?
