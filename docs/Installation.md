# Установка и настройка

Для самостоятельной сборки потребуется `dotnet-sdk:3.0`, а для запуска `dotnet-runtime:3.0`. Вам нужно выполнить `dotnet build` или `dotnet publish` внутри `Replica.App` указав необходимые параметры. <br/>
Вы также можете воспользоваться уже собранными исполняемыми файлами или развернуть Docker-контейнер.

Для запуска бота в рабочем каталоге (в большинстве случаев это каталог с исполнямыми файлами) должен находиться файл `settings.json`. <br/>
Конфигурационный файл должен выглядеть примерно так:

```json
{
    "controllers": {
        "tg": {
            "token": ""
        },
        "vk": {
            "token": "",
            "pollingGroup": 0
        }
    },
    "options": {
        "skip": true,
        "changelog": true,
        "database": {
            "type": "sqlite",
            "connection": "Data Source=data/db"
        },
        "su": ["exeteres"]
    },
    "core": {
        "language": "ru",
        "cache": "data/cache"
    }
}
```

Он содержит три секции: `controllers`, `options` и `core`. О причинах такого разделения вы узнаете позже. <br/>
Ниже представлено описание всех возможных параметров.

### Options

| Параметр            | Тип    | По умолчанию          | Описание                                                            |
| ------------------- | ------ | --------------------- | ------------------------------------------------------------------- |
| skip                | bool   | true                  | Пропустить сообщения, полученные с помощью longpoll до запуска бота |
| changelog           | bool   | false                 | Рассылать инфорамация об обновлениях бота в зарегистрированные чаты |
| database            | object |                       | Настройки базы данных (см. База данных)                             |
| database.type       | string | "sqlite"              | Тип базы данных                                                     |
| database.connection | string | "Data Source=data/db" | Строка для подключения к базе данных                                |
| su                  | array  | []                    | Список пользователей, обладающих правами суперадминистратора        |
| endpoint            | string | -                     | Адрес сервера для работы webhook (см. Webhook)                      |
| port                | number | 4000                  | Порт для работы webhook                                             |

### Core

| Параметр | Тип    | По умолчанию | Описание                 |
| -------- | ------ | ------------ | ------------------------ |
| language | string | "en"         | Локализация по умолчанию |
| cache    | string | data/cache   | Каталог для кеширования  |

### Controllers

Каждый из параметров этой секции - объект, содержащий настройки, специфичные для каждого "контроллера".
Общим для VK и Telegram будет параметр `token`. Для VK также требуется указать id группы. <br/>

## База данных

Для работы с базой данных Replica использует EF Core. Поддерживаются два провайдера: `sqlite` и `mysql`. <br/>
Для подключения необходимо задать параметр `connection`.

Для Sqlite:

```
Data Source=data/db
```

Для MySql:

```
Server=localhost;User Id=user;Password=pass;Database=replica
```

Обратите внимание, что провайдер `mysql` поддерживает все производные от mysql базы данных, например, mariadb. <br/>
Подробнее можно прочитать [тут](https://www.connectionstrings.com/).

## Webhook

Для активации webhook/callback достаточно просто установить параметр `endpoint`. Это должен быть реальный домен или ip адрес вашего сервера. Replica автоматически установит его при запуске.<br/>
Однако вам надо будет самостоятельно перенаправлять запросы на `4000` порт (его можно изменить) используя ваш любимый прокси-сервер. <br/>
Пример конфигурации для nginx:

```
server {
	listen 443 ssl;
	server_name bots.example.com;

	location /replica {
		proxy_pass http://replica:4000;
	}
}
```

В таком случае параметр `endpoint` = `https://bots.example.com/replica`.
