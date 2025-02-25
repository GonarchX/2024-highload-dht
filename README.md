# 2024-highload-dht
Курсовой проект 2024 года [курса «Разработка high-load систем»](https://education.vk.company/curriculum/program/discipline/2007/) [Корпоративной магистерской программы «Распределённые веб-сервисы / Web scale systems»](https://dws.itmo.ru/).

## Этап 1. HTTP + storage (deadline 2024-02-21 23:59:59 MSK)
### Fork
[Форкните проект](https://help.github.com/articles/fork-a-repo/), склонируйте и добавьте `upstream`:
```
$ git clone git@github.com:<username>/2024-highload-dht.git
Cloning into '2024-highload-dht'...
...
$ git remote add upstream git@github.com:polis-vk/2024-highload-dht.git
$ git fetch upstream
From github.com:polis-vk/2024-highload-dht
 * [new branch]      main     -> upstream/main
```

### Make
Так можно запустить тесты:
```
$ ./gradlew test
```

А вот так -- сервер:
```
$ ./gradlew run
```

### Develop
Откройте в IDE -- [IntelliJ IDEA Community Edition](https://www.jetbrains.com/idea/) нам будет достаточно.

**ВНИМАНИЕ!** При запуске тестов или сервера в IDE необходимо передавать Java опцию `-Xmx128m`.

В своём Java package `ru.vk.itmo.test.<username>` реализуйте интерфейсы [`Service`](src/main/java/ru/vk/itmo/Service.java) и [`ServiceFactory.Factory`](src/main/java/ru/vk/itmo/test/ServiceFactory.java) и поддержите следующий HTTP REST API протокол:
* HTTP `GET /v0/entity?id=<ID>` -- получить данные по ключу `<ID>`. Возвращает `200 OK` и данные или `404 Not Found`.
* HTTP `PUT /v0/entity?id=<ID>` -- создать/перезаписать (upsert) данные по ключу `<ID>`. Возвращает `201 Created`.
* HTTP `DELETE /v0/entity?id=<ID>` -- удалить данные по ключу `<ID>`. Возвращает `202 Accepted`.

Используйте свою реализацию `Dao` из предыдущего курса `2023-nosql-lsm` или референсную реализацию, если своей нет.

Проведите нагрузочное тестирование с помощью [wrk2](https://github.com/giltene/wrk2) в **одно соединение**:
* `PUT` запросами на **стабильной** нагрузке (`wrk2` должен обеспечивать заданный с помощью `-R` rate запросов) **ниже точки разладки**
* `GET` запросами на **стабильной** нагрузке по **наполненной** БД **ниже точки разладки**

Нагрузочное тестирование и профилирование должны **проводиться в одинаковых условиях** (при одинаковой нагрузке на CPU). А почему не `curl`/F5, можно узнать [здесь](http://highscalability.com/blog/2015/10/5/your-load-generator-is-probably-lying-to-you-take-the-red-pi.html) и [здесь](https://www.youtube.com/watch?v=lJ8ydIuPFeU).

Приложите полученный консольный вывод `wrk2` для обоих видов нагрузки.

Отпрофилируйте приложение (CPU и alloc) под `PUT` и `GET` нагрузкой с помощью [async-profiler](https://github.com/async-profiler/async-profiler/).
Приложите SVG-файлы FlameGraph `cpu`/`alloc` для `PUT`/`GET` нагрузки.

**Объясните** результаты нагрузочного тестирования и профилирования и приложите **текстовый отчёт** (в Markdown). Все используемые инструменты были рассмотрены на лекции -- смотрите видео запись.

Продолжайте запускать тесты и исправлять ошибки, не забывая [подтягивать новые тесты и фиксы из `upstream`](https://help.github.com/articles/syncing-a-fork/).
Если заметите ошибку в `upstream`, заводите баг и присылайте pull request ;)

### Report
Когда всё будет готово, присылайте pull request со своей реализацией, результатами профилирования, отчётом с их анализом и проведёнными по результату профилирования оптимизациями на review. На всех этапах **оценивается и код, и анализ (отчёт)** -- без анализа полученных результатов работа оценивается минимальным количеством баллов.
Не забывайте **отвечать на комментарии в PR** (в том числе автоматизированные) и **исправлять замечания**!
