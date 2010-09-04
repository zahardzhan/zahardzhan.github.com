---
layout: post
title: Настройка Emacs для программирования на Clojure
---

В статье я расскажу как сделать из Emacs'а удобную среду для
программирования на языке Clojure. Рассчитываю на то, что вы
используете Debian или Ubuntu в качестве своей операционной системы.

Кто не знает, Clojure это современный диалект лиспа. Очень простой и
мощный язык программирования общего назначения с упором на
функциональный стиль, широкой поддержкой возможностей для
многопоточного программирования и, конечно, родным для лиспа
представлением кода как данных и наоборот. Clojure работает поверх
виртуальной машины Java и виртуальной машины Common Language Runtime —
части пакета Microsoft .NET Framework.

## Java

В репозитории есть несколько альтернативных джав — OpenJDK и Sun Java
— ставим на свой вкус любой JDK, но обязательно самый последний, 6-ой.

    #!bash
    sudo aptitude install sun-java6-jdk

Однако, надо соблюсти осторожность при использовании OpenJDK — в
swank-clojure есть пренеприятный баг при подключении к java-машине из
Slime. Однажды у меня возникла с ним проблема, и дабы разрешить её я
нарыл в сети следующее заклинание:

    #!bash
    sudo sed -i 's/net.ipv6.bindv6only\ =\ 1/net.ipv6.bindv6only\ =\ 0/'  /etc/sysctl.d/bindv6only.conf &amp;&amp; sudo invoke-rc.d procps restart

## Emacs

Emacs устанавливается командой

    #!bash
    sudo aptitude install emacs

В Ubuntu ставится самый последний, 23-ий Emacs, в Debian же, в
стабильном репозитории lenny лежит старый, 22-ой, а свежий нужно
ставить из тестируемого репозитория squeeze или
[репозитория](http://emacs.orebokech.com/) со снапшотами.

## ELPA

Как говорится, Emacs — не только редактор, но и весьма себе неплохая
ОС, и как у всякой хорошей оси у него есть свой собственный пакетный
менеджер а'ля apt, только устанавливает он специально заготовленные
пакеты-расширения эмакса. На сайте
[ELPA](http://tromey.com/elpa/install.html) приведена инструкция по
установке: копируем этот код

    #!lisp
    (let ((buffer (url-retrieve-synchronously
          "http://tromey.com/elpa/package-install.el")))
      (save-excursion
        (set-buffer buffer)
        (goto-char (point-min))
        (re-search-forward "^$" nil 'move)
        (eval-region (point) (point-max))
        (kill-buffer (current-buffer))))

в scratch буфер эмакса, наводим курсор на последнюю скобку, и жмём
**C-x C-e**, чтобы запустить код — он автоматически скачает файл
package.el в ~/.emacs.d/elpa/package.el и отредактирует основной файл
с настройками ~/.emacs, чтобы менеджер пакетов и всё, что он
понаустанавливал потом запускалось при старте эмакса.

## Swank-clojure и clojure-mode

Swank-clojure это расширение для Emacs, которое позволяет исользовать
в нём Slime на пару с Clojure. А сlojure-mode — это режим эмакса для
удобного редактирования исходников на Clojure, он даёт подсветку
элементов синтаксиса, ключевых слов и табуляцию лиспокода в лучших
традициях. Оба этих пакета ставятся через ELPA и тянут за собой Slime
оттуда же как зависимость. Это ничего, но если нужна поддержка других
лиспов, будь то Common Lisp или Scheme, надо будет поставить Slime еще
и через Git или репозиторий оси.

В Emacs'е откроте список пакетов ELPA

> **M-x package-list-packages**

и выберите клавишей **i** пакеты (если выбрали не то — клавиша **u**
отменит выбор)

- clojure-mode
- clojure-test-mode
- slime
- slime-repl
- swank-clojure

Нажмите **x** чтобы установить то, что выбрали. Как скачается и
установится — можно закрыть Emacs и продолжить.

## Запуск REPL'а Clojure с целью поиграться

Собственно, мы установили всё что нужно для того, чтобы запустить
Clojure, кроме самой Clojure. Тут нас поджидает приятный момент — всё
остальное будет устанавливаться автоматически, без нашего на то
вмешательства. Запустим Emacs и поиграем с Clojure в репле командой 

> **M-x slime**

по ходу дела в директорию ~/.swank-clojure скачается 3 jar-пакета —
сама clojure, стандартная библиотека clojure-contrib и swank-clojure.

Это была всего лишь базовая установка, однако чтобы извлечь из Clojure
больше пользы и создать свой собственный проект, а не просто потыкать
REPL, нужно установить еще несколько программ, без которых ну никак не
обойтись в разработке.

## Git

Git — распределённая система управления версиями файлов, созданная
Линусом Торвальдсом для управления разработкой ядра Linux. Ко всему
она тоже понадобится, ибо большая часть проектов Clojure-коммьюнити,
включая саму Clojure, используют эту систему контроля версий, и почти
все они хостятся на [гитхабе](http://github.com/).

    #!bash
    sudo aptitude install git-core

## Maven и Аnt

Это фреймворки для сборки Java-проектов. Используются как платформа
для leiningen.

Установим их

    #!bash
    sudo aptitude install ant maven2

## Leiningen

Leiningen — это система сборки для проектов на Clojure, созданная,
чтобы лисперы не морочили голову с родными для платформы Java
системами сборки, которые имеют пристрастие к обильным XML-конфигам, в
которых чёрт ногу сломит. В лейнингене Clojure-проект описывается в
одном файле на самой же Clojure в виде простой удобочитаемой
конструкции. Еще поддерживаются плугины, например, для
автодокументации в стиле документации с официального сайта
[clojure](http://clojure.org).

Скачайте скрипт

    #!bash
    wget http://github.com/technomancy/leiningen/blob/master/bin/lein

и скопируйте его в какую-нибудь директорию, что прописана в пути PATH,
лучше всего в /usr/local/bin/, и дайте право на выполнение +x

    #!bash
    sudo cp lein /usr/local/bin/lein
    sudo chmod +x /usr/local/bin/lein

Лейнинген устанавливает себя сам, наше дело лишь сказать ему, что оно неплохо начать

    #!bash
    lein self-install

И директория ~/.m2 отныне станет кэшем для всех автоматически скачаных
jar'ов.

## Работа со Slime

Практически вся разработка проекта проходит в REPL'е Slime, и есть
несколько способов открыть проект в нём.

Способ первый — использовать команду эмакса c указанием пути к проекту

> **M-x swank-clojure-project**

Способ второй — в директории проекта выполнить команду 

    #!bash
    lein swank

что запустит REPL на порту 4005, к которому можно будет подключиться из эмакса командой

> **M-x slime-connect**

После подключения получаем REPL Clojure с автоматически установленными
Java classpath и да прибудет с нами сила!

## Работа с Clojure и Common Lisp

Slime, идущий в поставке ELPA урезан настолько, что годен только для работы с Clojure. Если от эмакса требуется работа с другими лиспами, придется поставить полноценный Slime из git-репозитория

    #!bash
    git clone git://github.com/nablaone/slime.git ~/.emacs.d/slime

и прописать в ~/.emacs следующие строки

    #!lisp
    (add-to-list 'load-path "~/.emacs.d/slime")
    (add-to-list 'load-path "~/.emacs.d/slime/contrib")

    (setq slime-backend "~/.emacs.d/slime/swank-loader.lisp")

    (load "slime-autoloads")

    (require 'slime)
    (require 'slime-autoloads)

    (eval-after-load "slime"
      `(progn
         (slime-setup '(slime-repl))
         (custom-set-variables
          '(inhibit-splash-screen t)
          '(slime-complete-symbol*-fancy t)
          '(slime-complete-symbol-function 'slime-fuzzy-complete-symbol)
          '(slime-net-coding-system 'utf-8-unix)
          '(slime-startup-animation nil)
          '(slime-lisp-implementations '((sbcl ("/usr/bin/sbcl")))))))

что позволит запускать и SBCL и Clojure командами

> **M-- M-x slime sbcl**
> **M-- M-x slime clojure**
