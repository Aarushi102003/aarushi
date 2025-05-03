---
## Front matter
title: "Отчёт по лабораторной работе №6


Информационная безопасность"
subtitle: "Мандатное разграничение прав в Linux"
author: "Выполнила: Сингх Ааруши , 


НКАбд-02-23, 132215095"

## Generic otions
lang: ru-RU
toc-title: "Содержание"

## Bibliography
bibliography: bib/cite.bib
csl: pandoc/csl/gost-r-7-0-5-2008-numeric.csl

## Pdf output format
toc: true # Table of contents
toc-depth: 2
lof: true # List of figures
fontsize: 12pt
linestretch: 1.5
papersize: a4
documentclass: scrreprt
## I18n polyglossia
polyglossia-lang:
  name: russian
  options:
	- spelling=modern
	- babelshorthands=true
polyglossia-otherlangs:
  name: english
## I18n babel
babel-lang: russian
babel-otherlangs: english
## Fonts
mainfont: PT Serif
romanfont: PT Serif
sansfont: PT Sans
monofont: PT Mono
mainfontoptions: Ligatures=TeX
romanfontoptions: Ligatures=TeX
sansfontoptions: Ligatures=TeX,Scale=MatchLowercase
monofontoptions: Scale=MatchLowercase,Scale=0.9
## Biblatex
biblatex: true
biblio-style: "gost-numeric"
biblatexoptions:
  - parentracker=true
  - backend=biber
  - hyperref=auto
  - language=auto
  - autolang=other*
  - citestyle=gost-numeric
## Pandoc-crossref LaTeX customization
figureTitle: "Рис."
tableTitle: "Таблица"
listingTitle: "Листинг"
lofTitle: "Список иллюстраций"
lolTitle: "Листинги"
## Misc options
indent: true
header-includes:
  - \usepackage{indentfirst}
  - \usepackage{float} # keep figures where there are in the text
  - \floatplacement{figure}{H} # keep figures where there are in the text
---

# Цель работы

Развить навыки администрирования ОС Linux. Получить первое практическое знакомство с технологией SELinux1 . Проверить работу SELinx на практике совместно с веб-сервером Apache.

# Теоретическое введение


  
**SELinux (Security-Enhanced Linux)** обеспечивает усиление защиты путем внесения изменений как на уровне ядра, так и на уровне пространства пользователя,
что превращает ее в действительно «непробиваемую» операционную систему.
Впервые эта система появилась в четвертой версии CentOS, а в 5 и 6 версии
реализация была существенно дополнена и улучшена.

*SELinux имеет три основных режим работы:*

- Enforcing: режим по умолчанию. При выборе этого режима все действия, которые каким-то образом нарушают текущую политику безопасности, будут блокироваться, а попытка нарушения будет зафиксирована в журнале.

- Permissive: в случае использования этого режима, информация о всех действиях, которые нарушают текущую политику безопасности, будут зафиксированы в журнале, но сами действия не будут заблокированы.

- Disabled: полное отключение системы принудительного контроля доступа.

Политика SELinux определяет доступ пользователей к ролям, доступ ролей к доменам и доступ доменов к типам.
Контекст безопасности — все атрибуты SELinux — роли, типы и домены.
Более подробно см. в [1].

2. **Apache** — это свободное программное обеспечение, с помощью которого можно создать веб-сервер. Данный продукт возник как доработанная версия другого HTTP-клиента от национального центра суперкомпьютерных приложений
(NCSA).

*Для чего нужен Apache сервер:*

- чтобы открывать динамические PHP-страницы,

- для распределения поступающей на сервер нагрузки,

- для обеспечения отказоустойчивости сервера,

- чтобы потренироваться в настройке сервера и запуске PHP-скриптов.

Apache является кроссплатформенным ПО и поддерживает такие операционные системы, как Linux, BSD, MacOS, Microsoft, BeOS и другие.

Более подробно см. в [2].



# Выполнение лабораторной работы

Вошли в систему под своей учетной записью и убедились, что SELinux работает в режиме enforcing политики targeted с помощью команд “getenforce” и “sestatus”

![(рис. 1. Проверка режима enforcing политики targeted)](image/1.PNG){ #fig:001 width=70% height=70% }

Обратились с помощью браузера к веб-серверу, запущенному на  компьютере, и убедились, что последний работает с помощью команды “service httpd status”

![(рис. 2. Проверка работы веб-сервера)](image/2.PNG){ #fig:002 width=70% height=70% }

С помощью команды “ps auxZ | grep httpd” определили контекст безопасности веб-сервера Apache - httpd_t

![(рис. 3. Контекст безопасности веб-сервера Apache)](image/3.PNG){ #fig:003 width=70% height=70% }

Посмотрели текущее состояние переключателей SELinux для Apache с помощью команды “sestatus -bigrep httpd”, многие из переключателей находятся в положении “off”

![(рис. 4.1. Текущее состояние переключателей SELinux)](image/4.1.PNG){ #fig:004 width=70% height=70% }

![(рис. 4.2. Текущее состояние переключателей SELinux)](image/4.2.PNG){ #fig:004 width=70% height=70% }


Посмотрели статистику по политике с помощью команды “seinfo”. Множество пользователей - 8, ролей - 14, типов 5100

![(рис. 5. Статистика по политике)](image/5.PNG){ #fig:005 width=70% height=70% }

С помощью команды “ls -lZ /var/www” посмотрели файлы и поддиректории, находящиеся в директории /var/www. Используя команду “ls -lZ /var/www/html”, определили, что в данной директории файлов нет. Только владелец/суперпользователь может создавать файлы в директории /var/www/html

![(рис. 6. Просмотр файлов и поддиректориий в директории /var/www)](image/6.PNG){ #fig:006 width=70% height=70% }

От имени суперпользователя создали html-файл /var/www/html/test.html. Контекст созданного файла - httpd_sys_content_t

![(рис. 7. Создание файла /var/www/html/test.html)](image/7.PNG){ #fig:007 width=70% height=70% }

Обратились к файлу через веб-сервер, введя в браузере адрес “http://127.0.0.1/test.html”. Файл был успешно отображен

![(рис. 8. Обращение к файлу через веб-сервер)](image/8.PNG){ #fig:008 width=70% height=70% }

Изучив справку man httpd_selinux, выяснили, что для httpd определены следующие контексты файлов: 

httpd_sys_content_t, httpd_sys_script_exec_t,

httpd_sys_script_ro_t, httpd_sys_script_rw_t, 

httpd_sys_script_ra_t, httpd_unconfined_script_exec_t.

Контекст моего файла - httpd_sys_content_t (в таком случае содержимое должно быть доступно для всех скриптов httpd и для самого демона). Изменили контекст файла на samba_share_t командой “sudo chcon -t samba_share_t/var/www/html/test.html” и проверили, что контекст поменялся

![(рис. 9. Изменение контекста)](image/9.PNG){ #fig:009 width=70% height=70% }

Попробовали еще раз получить доступ к файлу через веб-сервер, введя в браузере адрес “http://127.0.0.1/test.html” и получили сообщение об ошибке (т.к. к
установленному ранее контексту процесс httpd не имеет доступа)

![(рис. 10. Обращение к файлу через веб-сервер)](image/10.PNG){ #fig:010 width=70% height=70% }

Командой “ls -l /var/www/html/test.html” убедились, что читать данный файл может любой пользователь. Просмотрели системный лог-файл веб-сервера Apache командой “sudo tail /var/log/messages”, отображающий ошибки

![(рис. 11. Просмотр log-файла)](image/11.PNG){ #fig:011 width=70% height=70% }

В файле /etc/httpd/conf/httpd.conf заменили строчку “Listen 80” на “Listen 81”, чтобы установить веб-сервер Apache на прослушивание TCP-порта 81

![(рис. 12. Установка веб-сервера Apache на прослушивание TCP-порта 81)](image/12.PNG){ #fig:012 width=70% height=70% }

Перезапускаем веб-сервер Apache и анализируем лог-файлы командой “tail -nl /var/log/messages”

![(рис. 13. Перезапуск веб-сервера и анализ лог-файлов)](image/13.PNG){ #fig:013 width=70% height=70% }

Просмотрели файлы “var/log/http/error_log”, “/var/log/http/access_log” и "/var/log/audit/audit.log” и выяснили, что запись появилась в последнем файле

![(рис. 14. Содержание файла var/log/audit/audit.log)](image/14.PNG){ #fig:014 width=70% height=70% }

Выполнили команду “semanage port -a -t http_port_t -р tcp 81” и убедились, что порт TCP-81 установлен. Проверили список портов командой “semanage port -l | grep http_port_t”, убедились, что порт 81 есть в списке и запускаем веб-сервер Apache снова

![(рис. 15. Проверка установки порта 81)](image/15.PNG){ #fig:015 width=70% height=70% }

Вернули контекст “httpd_sys_cоntent_t” файлу “/var/www/html/test.html” командой “chcon -t httpd_sys_content_t /var/www/html/test.html” и после этого попробовали получить доступ к файлу через веб-сервер, введя адрес “http://127.0.0.1:81/test.html”, в результате чего увидели содежимое файла - слово “test”

![(рис. 16. Возвращение исходного контекста файлу)](image/16.PNG){ #fig:016 width=70% height=70% }

![(рис. 17. Обращение к файлу через веб-сервер)](image/17.PNG){ #fig:017 width=70% height=70% }

Исправили обратно конфигурационный файл apache, вернув “Listen 80”. Попытались удалить привязку http_port к 81 порту командой “semanage port -d -t http_port_t -p tcp 81”, но этот порт определен на уровне политики, поэтому его нельзя удалить

![(рис. 18. Возвращение Listen 80 и попытка удалить порт 81)](image/18.PNG){ #fig:018 width=70% height=70% }

Удалили файл “/var/www/html/test.html” командой “rm /var/www/html/test.html”

![(рис. 19. Удаление файла test.html)](image/19.PNG){ #fig:019 width=70% height=70% }
 
       


# Вывод

В ходе выполнения данной лабораторной работы были развиты навыки администрирования ОС Linux, получено первое практическое знакомство с технологией SELinux и проверена работа SELinux на практике совместно с веб-сервером
Apache.


# Список литературы. Библиография
 
[0] Методические материалы курса

[1] SELinux: https://habr.com/ru/companies/kingservers/articles/209644/

[2] Apache: https://2domains.ru/support/vps-i-servery/shto-takoye-apache
     

