---
published: false
title: Dropbox как storage для django
tags: dropbox django
---

В предыдущей [https://istroev.me/deploy-django-postgres/](статье) рассматривалось развертывание django на heroku. Как уже было сказано, в heroku используется эфемерная файловая система, поэтому использовать FileField вместе с FileSystemStorage не получится. Для того, чтобы использовать FileField в heroku в качестве хранилища для файлов можно использовать dropbox.
Для использования dropbox вместе с django нужно добавить в зависимости django-storages и Dropbox Python SDK.
```console
pipenv install django-storages dropbox
```
Далее нам нужно на странице [https://www.dropbox.com/developers/apps/create](https://www.dropbox.com/developers/apps/create) создать приложение. В настроиках приложения создаем токен для аутентификации - Generated access token.

В apps добавить приложение `storages` и добавить переменные `DROPBOX_OAUTH2_TOKEN` и `DROPBOX_ROOT_PATH`. 

settings.py

```python
INSTALLED_APPS = (
    ...
    'storages',
    ...
)
DEFAULT_FILE_STORAGE = 'storages.backends.dropbox.DropBoxStorage'
DROPBOX_OAUTH2_TOKEN='your token'
DROPBOX_ROOT_PATH='your path'
```

Обновить загруженный файл в хранилище можно следующим образом
```python
from storages.backends.dropbox import DropboxStorage
from dropbox.files import WriteMode

dbx = DropBoxStorage(settings.DROPBOX_OAUTH2_TOKEN)
mode = WriteMode.overwrite
name = instance.file.file.name
dbx.client.files_upload(bytes(data, 'utf-8'), name, mode)
```
