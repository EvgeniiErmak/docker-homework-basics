# Домашнее задание: Docker

Курс «Администратор Linux. Professional» (OTUS).

## Структура репозитория

- [`nginx-custom/`](./nginx-custom) — кастомный образ nginx на базе Alpine с изменённой главной страницей
- [`redmine-theme/`](./redmine-theme) — Docker Compose проект: Redmine + PostgreSQL с кастомной темой оформления (задание со звёздочкой)
- [`screenshots/`](./screenshots) — скриншоты процесса выполнения задания

## Ссылка на образ в Docker Hub

https://hub.docker.com/r/djermak9000/nginx-custom

## Основное задание

### Кастомный nginx (Alpine)

Собран образ на базе `nginx:alpine`, дефолтная страница заменена кастомной (см. `nginx-custom/Dockerfile` и `nginx-custom/html/index.html`).

Сборка и запуск:

```
docker build -t djermak9000/nginx-custom:v1 ./nginx-custom
docker run -d -p 8080:80 djermak9000/nginx-custom:v1
```

### Разница между образом и контейнером

Образ (image) — неизменяемый, слоистый read-only шаблон файловой системы, собранный по инструкциям Dockerfile. Контейнер (container) — запущенный экземпляр образа: те же слои образа плюс тонкий read-write слой поверх, в который пишутся все изменения во время работы. Один образ может породить сколько угодно независимых контейнеров. Контейнер эфемерен — данные в его r/w-слое исчезают при удалении контейнера, образ же хранится постоянно, пока не будет удалён явно.

### Можно ли в контейнере собрать ядро?

Технически — да: скачать исходники Linux, установить toolchain (gcc, make, bison и т.д.) и выполнить `make` внутри контейнера можно, это обычная компиляция без особых привилегий. Но собранное ядро нельзя загрузить и использовать внутри самого контейнера — контейнер не имеет собственного ядра и работает поверх ядра хост-системы (в этом ключевое отличие контейнеризации от виртуализации). Готовый `vmlinuz`/модули можно только вынести из контейнера через volume и установить/загрузить уже на хосте или другой машине.

## Задание со звёздочкой: Redmine с кастомной темой

В `redmine-theme/` находится `docker-compose.yml`, поднимающий Redmine (собранный из своего `Dockerfile` с опцией `build`) и базу данных PostgreSQL. Тема оформления [Farend Bleuclair](https://github.com/farend/redmine_theme_farend_bleuclair) добавляется в образ на этапе сборки. Для персистентности данных настроены именованные volumes: `redmine_files`, `redmine_plugins`, `redmine_themes`, `db_data`.

> Изначально использовался MySQL 8, но mysql2-гем в образе Redmine некорректно согласовывал TLS-режим с сервером (`SSL is required, but the server does not support it` / `self-signed certificate in certificate chain`) — переключились на PostgreSQL, с которым проблема не воспроизводится.

Запуск:

```
cd redmine-theme
docker compose up -d --build
```

После запуска тема выбирается в **Administration → Settings → Display → Theme**.
