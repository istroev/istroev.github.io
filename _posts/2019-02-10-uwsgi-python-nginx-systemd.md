---
published: false
title: Запуск Python приложения через Nginx + UWSGI в Docker
---

![docker_images](/images/python-docker.jpg){: .shadow-image }


В Docker контейнеры работают на основе образов (images). Существует множество уже готовых (alpine-linux, python3, debian). 
Каждый образ имеет имя, тэг и id. 
Список образов в системе можно узнать командой `docker images`.

Чтобы создать образ нужно написать Dockerfile.

```shell
FROM python:3
COPY test.py /opt/
CMD ["/usr/bin/python3", "/opt/test.py"]
```

Запустим сборку командой `docker build -t python3-test .`, где python3-test это имя нашего образа, которое передается ключем `-t`.

В команде `FROM` мы указали базовый образ, на основе которого мы будем создавать наш.
Так как в системе нет данного образа, то сначала он будет скачан.

![docker_pull](/images/docker_pull.png){: .shadow-image }

Командой `COPY` наш скрипт копируется в контейнер.

После сборки в нашей системе появятся новые образы.

![docker_images](/images/docker_images.png){: .shadow-image }

Запустить образ можно командой `docker run python3-test`.
Контейнер завершил свое выполнение, об этом можно узнать командой `docker ps -a`.
Контейнер имеет id и имя.

#### Запуск flask

Скрипт `test.py` будет выглядеть так.
```
#!/usr/bin/env python3

from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
        return "Hello World!"
```
Изменим `Dockerfile`

```shell
FROM python:3
COPY test.py /opt
RUN pip3 install flask
ENV FLASK_APP test.py
ENTRYPOINT ["python3"]
CMD ["/opt/test.py"]
```

Запускаем контейнер командой `docker run -p5000:5000  python3-test`. Ключ `-p` указывает на порт, который мы хотим пробросить из контейнера.

![docker_flask](/images/docker_flask.png){: .shadow-image }

#### Запуск приложения с uwsgi
Создаем файл с зависимостями requirements.txt

```shell
Click==7.0
Flask==1.0.2
itsdangerous==1.1.0
Jinja2==2.10
MarkupSafe==1.1.1
uWSGI==2.0.18
Werkzeug==0.14.1
```

редактируем `Dockerfile`

```shell
FROM debian:stretch-slim
RUN apt-get update -qq && \
apt-get install -y build-essential python-dev python3-pip
ADD . /home/app
ENV HOME /home/app
WORKDIR /home/app
COPY . .
RUN pip3 install --no-cache-dir -r requirements.txt
ENV FLASK_APP test.py
ENTRYPOINT ["uwsgi",\
 "--http", "0.0.0.0:5000", "--wsgi-file", "test.py", "--callable", "app"]
```

#### docker-compose

Устанавливаем docker-compose.
```shell
pip install docker-compose
```
aaa

```yaml
version: '3'

services:
  db:
    image: postgres
  web:
    build: .
    command: python manage.py runserver 0.0.0.0:8000
    volumes:
      - .:/code
    ports:
      - "8000:8000"
    depends_on:
      - db
```

#### ngixn

`/etc/nginx/sites-available/api.conf`


#### systemd

```
systemctl start service
```

##### links
[https://the-bosha.ru/2017/01/04/zapusk-flask-prilozheniia-c-uwsgi-virtualenv-i-nginx/](https://the-bosha.ru/2017/01/04/zapusk-flask-prilozheniia-c-uwsgi-virtualenv-i-nginx/)  
[https://www.8host.com/blog/obsluzhivanie-prilozhenij-flask-s-pomoshhyu-uwsgi-i-nginx-v-ubuntu-16-04/](https://www.8host.com/blog/obsluzhivanie-prilozhenij-flask-s-pomoshhyu-uwsgi-i-nginx-v-ubuntu-16-04/)  
[https://habr.com/ru/post/346634/](https://habr.com/ru/post/346634/)  
[https://docs.docker.com/compose/django/](https://docs.docker.com/compose/django/)  
[https://github.com/erroneousboat/docker-django](https://github.com/erroneousboat/docker-django)  