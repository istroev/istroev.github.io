---
published: false
title: Установка Debian 10 + awesome wm
---

В данной статье описывается установка и настроику Debian 10 вместе с тайловым оконным менеджером Awesome.
В качестве образа для установки используется только что вышедший Debian 10 netinst (https://www.debian.org/CD/netinst/). Цель такой "сборки" --- это получение минималистичного дистрибутива с минимумом пакетов и не требовательного к ресурсам системы. 


### Установка Debian

Во время установки Debian необходимо пройти стандартные шаги: разметка диска, выбор региона и раскладки, настройка пользователей, после чего предоставляется выбор дополнительных опции. 

***Скриншот***

![debian install](/images/VirtualBox_debian_17_07_2019_00_51_04.png){: .shadow-image }

Из имеющихся пунктов необходимо оставить "Стандартные системные утилиты
"


### установка X11

* bashrc
`
if [[ -z $DISPLAY ]] && [[ $(tty) = /dev/tty1 ]]; then
        startx
fi
`
* wifi
`
auto wlp13s0b1
iface wlp13s0b1 inet dhcp
wpa-ssid 
wpa-psk 
`

* dependencies
`
xorg awesome awesome-extra lightdm i3lock-fancy
`
* keyboard layout
` localectl --no-convert set-x11-keymap us,ru "" "" grp:ctrl_shift_toggle`