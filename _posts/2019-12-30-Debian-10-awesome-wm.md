---
layout: post
published: true
title: Установка Debian 10 + Awesome WM
---

В данной статье описывается установка и настроика Debian 10 вместе с тайловым оконным менеджером Awesome.
В качестве образа для установки используется [Debian 10 netinst](https://www.debian.org/CD/netinst/). Цель такой "сборки" --- это получение минималистичного дистрибутива с минимумом пакетов и не требовательного к ресурсам системы. 


### Установка Debian

Во время установки Debian необходимо пройти стандартные шаги: выбор региона и раскладки, разметка диска, настройка пользователей, после чего предоставляется выбор дополнительных опции. 

***Выбор програмного обеспечения***

![debian install](/images/VirtualBox_debian_17_07_2019_00_51_04.png){: .shadow-image }

Из имеющихся пунктов необходимо оставить "Стандартные системные утилиты. В результате получится минимальная система без DM/WM/DE.


### Установка Awesome

После установки логинимся под пользователем root. Вначале сконфигурируйте сеть если она не работает.

Далее нужно установить пакеты:

```
apt install xorg awesome awesome-extra lightdm alsa-oss alsa-utils sudo
```

После установки добавим не-root пользователя в группу sudo.

`usermod -aG sudo username`

Далее логинимся под вашим пользователем,

`su - username`

и редактируем файлы ~/.bash_profile и ~/.xinitrc.

```
~/.bash_profile

if [[ -z $DISPLAY ]] && [[ $(tty) = /dev/tty1 ]]; then exec startx; fi

~/.xinitrc

exec awesome
```
Теперь можно перезагрузить систему, после чего запуститься lightdm, в котором нужно залогиниться под своим пользователем, после чего должен запуститься Awesome.

***Awesome WM***
![awesome default](/images/awesome_default.png){: .shadow-image }


### Настройка Awesome

Создаем директорию для файлов с настройками и копируем туда шаблон, после чего можно вносить свои изменения в настройки.

```
mkdir -p ~/.config/awesome/
cp /etc/xdg/awesome/rc.lua ~/.config/awesome/
```

Для применения настроек нужно перезапустить Awesome с помощью Cntrl+Mod+R.

Чтобы добавить русскую расскладку с переключением по Alt+Shift выполните команду (необходима перезагрузка):

`# localectl --no-convert set-x11-keymap us,ru "" "" grp:alt_shift_toggle`


### Горячие клавиши

* Cntrl-Mod-R - перезагрузка интерфейса
* Mod + arrow-left (arrow-right) - переключение между рабочими областями
* Mod + Shift + num - перенос окна на другую рабочую область
* Mod + Space - переключение расположений окон

Много информации по настройке awesome можно найти на arch wiki.

##### links
[https://wiki.archlinux.org/index.php/Awesome_(Русский)](https://wiki.archlinux.org/index.php/Awesome_(Русский))
[https://wiki.debian.org/ru/NetworkConfiguration](https://wiki.debian.org/ru/NetworkConfiguration)
[https://github.com/streetturtle/awesome-wm-widgets](https://github.com/streetturtle/awesome-wm-widgets)
[https://github.com/nicklan/pnmixer](https://github.com/nicklan/pnmixer)