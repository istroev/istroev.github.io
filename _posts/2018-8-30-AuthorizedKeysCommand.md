---
layout: post
published: true
tags: OpenSSH
---
В [OpenSSH](https://ru.wikipedia.org/wiki/OpenSSH) версии [6.2](https://www.openssh.com/txt/release-6.2) появилась опция AuthorizedKeysCommand, которая позволяет выполнять произвольный скрипт для авторизации пользователя по публичному ключу. AuthorizedKeysCommand предоставляет возможность авторизовать пользователя без добавления публичного ключа в файл `authorized_keys`, при этом  предполагается, что ключ хранится где-то на удаленном сервере.  Опции настраивается в [/etc/ssh/sshd_config](https://man.openbsd.org/sshd_config#AuthorizedKeysCommand).  В параметрах указывается скрипт, который должен быть в `PATH` и иметь права на запуск.

На вход скрипту можно передавать ряд [токенов](https://man.openbsd.org/sshd_config#TOKENS):
- %% - литерал ‘%’,
- %f - цифровой отпечаток (fingerprint) ключа или сертификата,
- %h - домашняя директория пользователя,
- %k - ключ или сертификата для авторизации, закодированный в base64,
- %t - тип ключа или сертификата,
- %U - числовой идентификатор целевого пользователя,
- %u - имя пользователя.

Также должна быть указана опция AuthorizedKeysCommandUser, в которой указывается пользователь от которого будет запускаться скрипт (по умолчанию это пользователь nobody).

Полезные ссылки:
- [https://blog.heckel.xyz/2015/05/04/openssh-authorizedkeyscommand-with-fingerprint/](https://blog.heckel.xyz/2015/05/04/openssh-authorizedkeyscommand-with-fingerprint/)
- [https://help.ubuntu.ru/wiki/ssh](https://help.ubuntu.ru/wiki/ssh)

