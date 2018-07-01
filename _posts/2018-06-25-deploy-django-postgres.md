---
published: true
title: Деплой Django + Postresql на Heroku
---

В настоящее время существуют облачные платформы, такие как heroku и redhat openshift, которые предоставляют возможность бесплатно разместить у себя ваше приложение. Конечно бесплатный тариф имеет множество ограничений, но он может отлично подойти для учебного проекта или какого-нибудь простого приложения не требующего много ресурсов.

![heroku](/images/heroku.png)

Heroku предоставляет бесплатно 500 mb на диске и  512 mb RAM (подробнее тут - 
[https://devcenter.heroku.com/articles/limits](https://devcenter.heroku.com/articles/limits)). Также есть возможность использовать свой домен. Приложение работает в специальном Unix-контейнере, который называется Dyno. Free plan предоставляет 1 dyno без возможности масштабирования. Также время работы ограничено 1000 часами в месяц и приложение "засыпает" после 30 минут без активности. В dyno используется эфемерная файловая система. Это означает, что созданные приложением файлы удаляются. 

#### настроика окружения
Развертывание приложения в heroku осуществляется с помощью git. Для начала необходимо установить [heroku cli](https://devcenter.heroku.com/articles/heroku-cli) и выполнить аутентификацию с помощью `heroku login`. Затем, в папке с вашим приложением нужно выполнить команду `heroku create`, которая создаст в git удаленный репозиторий с названием heroku.

Перед отправкой в репозиторий нужно настроить приложение.
Необходимо скрыть SECRET-KEY и другие секретные переменные. Для этого можно использовать переменные окружения. В Heroku переменные окружения можно задавать в личном кабинете, либо командой `heroku config:set`.

Для установки библиотек python heroku использует [pipenv](https://docs.pipenv.org/). Если вы еще не используете pipenv, то установить его можно через pip, командой `pip install pipenv`. Далее командой `pipenv install` из файла requiremnts.txt будут созданы файл с зависимостями Pipenv и Pipenv.lock, которые используются вместо requirements.txt.
Для запуска django приложения потребуется web-сервер gunicorn.
```console
pipenv install gunicorn
```
Чтобы настроить запуск django через gunicorn, нужно создать файл Procfile содержащий в себе команду:  
```console
web: gunicorn yourapp-name.wsgi
```

Heroku по умолчанию не может отдавать staticfiles. Чтобы это исправить можно использовать whitenoise.
```console
pipenv install whitenoise
```
settings.py
```python
MIDDLEWARE = [
    'whitenoise.middleware.WhiteNoiseMiddleware',]
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
```

#### настроика postgres
Для соединения dyno с базой данных используется переменная `DATABASE_URL`. Для парсинга переменной в формат django, рекомендуется использовать `dj-database-url`.
```console
pipenv install dj-database-url
```
settings.py
```python
# Heroku: Update database configuration from $DATABASE_URL.
import dj_database_url
db_from_env = dj_database_url.config(conn_max_age=500)
DATABASES['default'].update(db_from_env)
```
Для работы с postgresql потребуется установить addon [Heroku Postgres](https://elements.heroku.com/addons/heroku-postgresql).
```console
heroku addons:create heroku-postgresql:<PLAN_NAME>
```

#### deploy
После всех необходимых настроек отправляем приложение в heroku командой 
```console
git push heroku master.
```
Запускаем миграции
```console
heroku run python manage.py migrate
```
Если выкатка прошла без ошибок, то приложение можно открыть командой `heroku open`.
Посмотреть логи можно командой:
```console
heroku logs --tail
```
