# Репозитарий ДЗ по курсу [DevOps практики и инструменты](http://otus.ru/lessons/devops-praktiki-i-instrumenty/)

Приложение Reddit на Docker-инфраструктуре

# Структура репозитария

## monolith

Reddit в виде монолитного приложения. Создание образа командой
```
docker build -t reddit-app monolith/
```
Запуск
```
docker run reddit-app
```

## comment, post-py, ui

Reddit на микросервисной архитектуре. Создание образов
```
docker build -t comment:1.0 ./comment/
docker build -t post:1.0 ./post-py/
docker build -t ui:2.0 ./ui/
```

Запуск приложения потребует дополнительный контейнер с Mongodb. Для сохранения базы при пересоздании контейнера требуется создать docker volume
```
docker volume create reddit_db
```

Запуск приложения
```
docker run -d --network=reddit -v reddit_db:/data/db --network-alias=post_db --network-alias=comment_db mongo:latest
docker run -d --network=reddit --network-alias=post post:1.0
docker run -d --network=reddit --network-alias=comment comment:1.0
docker run -d --network=reddit -p 9292:9292 ui:2.0
```

## Запуск через docker-compose

В основной директории проекта выполнить
```
docker-compose up -d
```
Параметры для создаваемой инфраструктуры следует задать в файле .env. Пример .env.example.

## Мониторинг приложения

IP:9090 - prometheus
