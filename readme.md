# PRTV

Проект разделен на 2 части: клиентскую и серверную.
Для начала работы нужно произвести первоначальную настройку проекта.

### Запуск у установка без докера
#### Важно чтобы версия node в yarn совпадала с версией node meteor
Для этого предлагается использовать или docker см ниже или nodeenv

1. установить nodeenv https://github.com/ekalinin/nodeenv
2. Создаем окружение `nodeenv --node=12.14.0 env-node`
3. Активируем: `. env-node/bin/activate` (далее любые операции нужно выполнять из по активированного окружения)
4. Устанавливаем зависимости `yarn`
5. Создаем бандл для webpack `yarn run client-bundle`
6. В папке `config` создайте копию файла `example.json` с именем `local.json` 
`cp config/example.json config/local.json`
7. Замените /path/ на другой путь в config/local.json например /tmp/
8. В папке `client` создайте копию файла `.env.example` с именем `.env`
   `cp client/.env.example client/.env`
9. Запуск проекта целиком (клиент и сервер) `yarn start-all`
10. Клиент запускается по адресу `http://localhost:3007`
11. Для логина использовать adminUser из `config/local.json`



Если нужно или только backend или только клиента запустить:
5. Запуск клиентской части `yarn start-client`
6. Запуск серверной части `yarn start-backend`

Клиент запускается по адресу `http://localhost:3007`

### Первоначальная установка для разработки (через docker)

- Скопируйте и настройте переменные окружения в файле `.env`
  ```
  cp .env.dist .env
  ```
 
- В папке `config` создайте копию файла `example.json` с именем `local.json`


- В папке `client` создайте копию файла `.env.example` с именем `.env`


- Соберите клиентскую часть 
  ```
  docker-compose run --rm meteor bash -c "yarn install && yarn client-bundle"
  ```
  

### Запуск проекта для разработки

 ```
docker-compose up -d
```

Клиент будет доступен по адресу http://localhost:3007
  

### Особенности работы с проектом

#### Новые зависимости

Если добавлены новые зависимости,
то перед стартом проекта нужно запустить сборку клиента
```
docker-compose run --rm meteor yarn run client-bundle
```

#### Переменные окружения

При добавлении новой переменной в файле `.env`,
требуется добавить эту переменную с шаблонными данными в файл `.env.dist`

#### Запуск на внешнем IP

> TODO: Описать запуск в Docker окружении

Иногда для тестирования необходимо запустить на внешнем IP
Для этого есть команда `yarn start-all-external`
Если смотреть в package.json видно, что там прописан IP `192.168.1.21`
Его нужно заменить на ваш внешний IP 
После того, нужно выполнить `yarn run client-bundle` чтобы meteor-client.js пересобрался обратно с правильными 
настройками


### Структура

#### Основные папки проекта:

* `client` - папка содержит исключительно код клиентский части приложения, недоступный для сервера. 
Точка входа для клиентского приложения: `client/main.ts`.
"Жадный" импорт отключен.
* `server` - папка содержит исключительно код серверной части приложения, недоступный для клиента.
Точка входа для клиентского приложения: `server/main.ts`.
"Жадный" импорт отключен.
* `shared` - папка содержит общий код как для клиента, так и для сервера.
Типичные файлы для этой папки: коллекции, модели, константы, некоторые методы.
* `config` - папка содержит файлы настроек для разных окружений в формате `json`.
Все настройки приложения (как клиента, так и сервера) должны находится тут.
* `private` - папка содержит статические файлы, недоступные из вне посредствам http запрос.
Доступ к содержимому имеет только серверный код.
* `public` - папка содержит статические файлы, доступные пользователям через http запрос.
Типичное содержание: изображения, шрифты, стили.

#### Структура клиентского приложения (client)
Клиент состоит из двух частей: редактор и показ слайдов. Каждый их модулей имеет свою точку входа.

* `actions` - папка содержит redux action creators.
* `collections` - папка содержит файлы определения коллекций, используемые только на клиенте.
* `components` - папка содержит компоненты react, реализующие функции приложения. 
Все компоненты разбиты по папкам: каждая папка содержит компоненты для одной из страниц или раздела приложения.
* `constants` - папка содержит файлы с константами, такие как роуты и общие константы клиента.
* `layouts` - папка содержит компоненты react, являющимися базовыми шаблонами для страниц приложения.
* `models` - папка содержит typescript типы, используемые только на клиенте.
* `reducers` - папка содержит redux reducers.
* `store` - папка содержит настройки redux store.
* `utils` - папка содержит различную общую бизнес-логику клиентского приложения и настройки для библиотек.
* `types` - папка с типами TS для тех сторонних библиотек у которых нет типов в npm (только для клиента).
* `main.ts` - точка входа. Основная задача - импорт остальных файлов клиентской части.
* `main.html` - корневой html файл.
* `main.scss` - главный файл стилей. Содержит общие стили и импорт стилей сторонних зависимостей.
* `entrypoints/editor.tsx` - точка входа для редактора.
* `entrypoints/slideshow.tsx` - точка входа для показа слайдшоу.
* `Routes.tsx` - компонент роутов для `redux-router`.

#### Структура серверного приложения (server)

* `integrations` - папка содержит файлы интеграции со сторонними API (такие как VK, OK, OpenWeatherMap).
Для каждого API - отдельный файл. Если файлов несколько, то в отдельную папку.
* `collections` - папка содержит файлы определения коллекций, используемые только на сервере и недоступные на клиенте.
* `models` - папка содержит typescript типы, используемые только на сервере.
* `scheduler` - папка содержит файлы для запуска задач по расписанию, используя менеджер очереди `agenda`.
* `modules` - папка содержит папки модулей, каждый модуль реализует отдельную часть приложения.
Типовые модули: `auth`, `admin`, `slideshow` и тд. Содержание папки модуля
    * `index.ts` - импортирует все файлы модуля в себя. Так же может выполнять код, нужный для работы/настройки модуля.
    * `methods.ts` - файл содержит meteor-методы модуля, код которых должны быть недоступен на клиенте.
    * `publish.ts` - файл содержит код для публикации данных с помощью Meteor.publish.
    * другие необходимые для модуля файлы и папки.
* `constants` - папка содержит файлы с только серверными константами, недоступными на клиенте.
* `types` - папка с типами TS для тех сторонних библиотек у которых нет типов в npm (только для сервера).
* `main.ts` - точка входа. Основная задача - импорт остальных файлов серверной части.
* `startup.ts` - файл содержит код, необходимый для запуска приложения, настройки и тд.

#### Структура папки общего кода (shared)

* `collections` - папка содержит файлы определения коллекций, общих для всего приложения.
* `constants` - папка содержит файлы с общими константами.
* `utils` - папка содержит различную общую бизнес-логику как для клиента, так и для сервера. 
* `models` - папка содержит typescript типы, используемые как на клиенте, так и на сервере.
* `types` - папка с типами TS для тех сторонних библиотек у которых нет типов в npm (клиент и сервер).

### Описание модулей и важных файлов
* `server/modules/dataSources` - модуль хранит в себе файлы для получения и обработки данных из внешних источников 
(twitter, instagram, vk и тд.).
* `shared/constants/methodNames` - файл содержит имена всех метод приложения.
* `shared/constants/publishNames.ts` - файл содержит имена всех публикаций приложения.

## Инструкции по запуску Mongodb replicaSet
* mongo -u root -p
* rs.initiate()
* use prtv
* db.createUser({user: "prtv", pwd: "KtD2ZBaD4DpZb85Smr4S", roles: [{"role": "readWrite", db: "prtv"}]})
* use admin
* db.createUser({user: "oplogger", pwd: "JKX3umPyGgrJM7f6PBhG", roles: [{role: "read", db: "local"}]});

## Доставка новой версии на продакшен
- Деплой осуществляется с помощью GitLab CI/CD.
  - `.gitlab-ci.yml`

- Проект собирается в Docker образе непосредственно на сервере.
  - `docker/prod/Dockerfile`

- Сценарий сборки:
  - `docker/prod/build`


- Деплой осуществляется с помощью GitLab CI/CD.
- Проект собирается в Docker образе непосредственно на сервере.
- После ревью есть возможность сборки на тестовый сервер.
- Для старта процесса деплоя требуется запустить задачу `deploy-review` в созданном Pipeline
- Ход работы задачи можно увидеть в интерфейсе GitLab
- Для старта процесса деплоя требуется запустить задачу `deploy-production` в созданном Pipeline
- Ход работы задачи можно увидеть в интерфейсе GitLab


### Алгоритм работы с CI/CD системой
- После коммита в ветку **master** 
  в интерфейсе GitLab появляется [Pipeline](https://gitlab.com/prtvsu/prtv/-/pipelines) для сборки проекта
- Для старта процесса деплоя требуется запустить задачу `deploy-production` в созданном [Pipeline](https://gitlab.com/prtvsu/prtv/-/pipelines)
- Ход работы задачи можно увидеть в интерфейсе GitLab

Или можно на самом сервере

`cd /root/prtv && docker-compose restart mongo && git pull && docker/prod/build`

Если на сервере закончилось место, советую, при этом нужно убедиться, что нужные контейнеры запушены

`docker system prune -a`

### Отправка почты
Сейчас используется sendpulse.com для отправки писем
настойки см тут https://login.sendpulse.com/smtp/index/settings/


## Сборка продакшен версии на сервере через докер 
  1. Для сбоки докера нужно точно выделить больше 2 gb.
  2. Необходимо создать файл в папке client с названием `.env.prod` и скопировать в него код из `.env.example`.
  3. Далее зайти в файл `docker-compose.prod.yaml` и указать в нем порты для монги `127.0.0.1:27017:27017`
  4. В папке `config` создайте копию файла `example.json` с именем `local.json`
  5. В папке `client` создайте копию файла `.env.example` с именем `.env`
  6. Также перед запуском необходимо выполнить скрипт `docker network create nginx-proxy`
  7. В файле `docker-compose.prod.yaml up` стоит добавить поле ports 
```
ports:
    # здесь необходимо указать правильный порт
      - "80:80"
``` 
  8. Также пока работает версия без оплога, поэтому следует закомментировать строчку ```# passenger_env_var MONGO_OPLOG_URL mongodb://oplogger:JKX3umPyGgrJM7f6PBhG@mongo:27017/local?authSource=admin;``` в файле `nginx.config` - в этом же файле необходимо указать нужный ip сервера
  9. Запустить файл `docker-compose.prod.yaml` командой ```docker-compose -f docker-compose.prod.yaml up```
  10. Необходимо создать новую базу prtv и создать юзера(подключится к базе `mongo mongodb://root:5eX9axFSDv7dN2PN@127.0.0.1:27017/admin`)
Далее выполняем следующие команды (контейнеры должны быть запущены в этот момент)
   * rs.initiate()
   * use prtv
   * db.createUser({user: "prtv", pwd: "KtD2ZBaD4DpZb85Smr4S", roles: [{"role": "readWrite", db: "prtv"}]})
  11. После этого нужно добавить разрешение на папку проекта 
  ```
  chmod -R 777 prtv_priv
  ```
  12. После этого останавливаем контейнеры и по новой их запускаем командой ```docker-compose -f docker-compose.prod.yaml up```
  13. После этого будут созданы два докер образа - серверная и клиентская часть, также они будут автоматически запущены
  14. Все свойства для конфигурации приложения стоит прописывать в `docker-compose.prod.yaml`

## Как выложить на тестовый сервер
1. Руками коннектишься к серверу (ssh root@82.146.53.61) доступы в телеграмм
2. Выбираешь правильную ветку (git checkout ...)
3. Пулишь (git pull )
4. Запускаешь сборку (cd /root/prtv_priv/prtv_priv && git pull && docker/prod/build)