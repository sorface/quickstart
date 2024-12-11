# sorface quickstart [DONT WORK]

Репозиторий быстрого запуска платформы sorface

2024.4Q | Доступен локальный запуск проекта

## Локальный запуск проекта (docker-compose)

Только backend часть:

```shell
    docker-compose -f sorface-quickstart.yaml up redis postgresql backend-passport backend-gateway backend-interview -d
```

Для запуска используется файл [sorface-quickstart.yaml](docker.infrastructure%2Fsorface-quickstart.yaml)

Данный файл содержит запуск следующих служб:

* redis # no sql
* postgresql # база данных PostgteSQL
* backend-passport # сервис аутентификации и авторизации
* backend-gateway # сервис API шлюз
* backend-interview # сервис

## Сервисы

### posrgresql

PostgteSQL СУБД

```
docker.infrastructure
    |- datastorage.postgresql
        |- .docker.pgsql.env
        |- Dockerfile
        |- init-script.sh
```

[.docker.pgsql.env](docker.infrastructure%2Fdatastorage.postgresql%2F.docker.pgsql.env)

Конфигурация переменных окружения

```properties
POSTGRES_MULTIPLE_DATABASES=sso,sorface  # название создаваемых баз данных
POSTGRES_USER=user                       # имя пользователя
POSTGRES_PASSWORD=user                   # пароль пользователя
```

[init-script.sh](docker.infrastructure%2Fdatastorage.postgresql%2Finit-script.sh)

Скрипт инициализации базы данных при старте контейнера

### redis

Redis NoSQL

```
docker.infrastructure
    |- datastorage.redis
        |- .docker.redus.env
        |- Dockerfile
```

[.docker.redis.env](docker.infrastructure%2Fdatastorage.redis%2F.docker.redis.env)

```properties
REDIS_ARGS='--requirepass testpassword' # пароль к сервису
PORT=6379                               # порт
```

### service.passport

Сервис аутентификации и авторизации пользователей

```
docker.infrastructure
    |- service.passport
        |- backend
            |- .env
            |- Dockerfile
```

```properties
# SPRING envs

SPRING_PROFILES_ACTIVE=docker

# DATABASE envs

DATABASE_URL=jdbc:postgresql://postgresql:5432/sso
DATABASE_USERNAME=****************
DATABASE_PASSWORD=****************
DRIVER_CLASS_NAME=org.postgresql.Driver

# MAIL envs

MAIL_NOTIFICATOR_USERNAME=****************
MAIL_NOTIFICATOR_PASSWORD=****************

# FRONTEND envs

PASSPORT_FRONTEND_DOMAIN=http://localhost:9020
PROFILE_FRONTEND_URL=http://localhost:9030

# REDIS envs

## connect
REDIS_HOST=redis
REDIS_USERNAME=****************
REDIS_PASSWORD=****************

## session
SPRING_SESSION_REDIS_NAMESPACE=passport:session
SPRING_SESSION_TIMEOUT=5d

## account
PASSPORT_ACCOUNT_REGISTRY_OTP_LIVE_TO_CACHE_SECONDS=180
PASSPORT_ACCOUNT_REGISTRY_LIVE_TO_CACHE_SECONDS=360

# COOKIE envs

## cookie registration new account

PASSPORT_ACCOUNT_REGISTRY_COOKIE_DOMAIN=localhost
PASSPORT_ACCOUNT_REGISTRY_COOKIE_NAME=account_tmp_id
PASSPORT_ACCOUNT_REGISTRY_COOKIE_PATH=/
PASSPORT_ACCOUNT_REGISTRY_COOKIE_HTTP_ONLY=true
PASSPORT_ACCOUNT_REGISTRY_COOKIE_SECURE=true

## cookie csrf
PASSPORT_CSRF_COOKIE_DOMAIN=localhost
PASSPORT_CSRF_COOKIE_PATH=/
PASSPORT_CSRF_COOKIE_NAME=x_csrf_token
PASSPORT_CSRF_COOKIE_HTTP_ONLY=true

## session cookie
PASSPORT_SESSION_COOKIE_DOMAIN_PATTERN=^.+?\.(\w+\.[a-z]+)$
PASSPORT_SESSION_COOKIE_DOMAIN_PATH=/
PASSPORT_SESSION_COOKIE_DOMAIN_NAME=pspt_sid
PASSPORT_SESSION_COOKIE_DOMAIN_HTTP_ONLY=true
PASSPORT_SESSION_COOKIE_DOMAIN_SAME_SITE=lax

# OAUTH2 env

OAUTH_SERVER_ISSUER_URL=http://localhost:8080

## access token
PASSPORT_OAUTH_TOKEN_ACCESS_CRON=minutes
PASSPORT_OAUTH_TOKEN_ACCESS_TTL=5

## refresh token
PASSPORT_OAUTH_TOKEN_REFRESH_CRON=days
PASSPORT_OAUTH_TOKEN_REFRESH_TTL=5

## authorization code
PASSPORT_OAUTH_TOKEN_AUTHORIZATION_CODE_CRON=minutes
PASSPORT_OAUTH_TOKEN_AUTHORIZATION_CODE_TTL=5

## github envs
OAUTH_CLIENT_GITHUB_ID=****************
OAUTH_CLIENT_GITHUB_SECRET=****************

## google envs
OAUTH_CLIENT_GOOGLE_ID=****************
OAUTH_CLIENT_GOOGLE_SECRET=****************

## yandex envs
OAUTH_CLIENT_YANDEX_ID=****************
OAUTH_CLIENT_YANDEX_SECRET=****************
OAUTH_CLIENT_YANDEX_REDIRECT_URL=http://localhost:8080/login/oauth2/code/yandex

## twitch envs
OAUTH_CLIENT_TWITCH_ID=****************
OAUTH_CLIENT_TWITCH_SECRET=****************
OAUTH_CLIENT_TWITCH_REDIRECT_URL=http://localhost:8080/login/oauth2/code/twitch

```

### service.gateway

Сервис API шлюз

```
docker.infrastructure
    |- service.gateway
        |- backend
            |- .env
            |- Dockerfile
```


[.env](docker.infrastructure%2Fservice.passport%2Fbackend%2F.env)

```properties
# spring envs
SPRING_PROFILES_ACTIVE=docker                                      # активный профиль                           
                                                                    
INTERVIEW_SERVICE_URL=http://backend-interview:80                  # URL к внутренниму сервису Interview
GATEWAY_SERVICE_URL=http://localhost:9000                          # текущие внешний URL gateway

PASSPORT_CLIENT_ID=**************                                  # Идентификатор OAUTH2 клиента
PASSPORT_CLIENT_SECRET=**************                              # Секрет OAUTH2 клиента
PASSPORT_SERVICE_URL=http://localhost:8080                         # внешнний URL на сервер авторизации и аутентификации 
INTERNAL_PASSPORT_SERVICE_URL=http://backend-passport:8080         # внутренний URL на сервер авторизации и аутентификации
EXTERNAL_PASSPORT_COOKIE_NAME=pspt_sid                             # Название cookie аутентификации

CORS_ALLOWED_ORIGINS=http://localhost:9030,http://localhost:3000   # Доступные URL's CORS
# redis envs

REDIS_HOST=redis                                                   # Хост redis
REDIS_USERNAME=**************                                      # Имя пользователя redis
REDIS_PASSWORD=**************                                      # Пароль пользователя redis
REDIS_PORT=6379                                                    # Порт redis
```

### service.interview

Сервис проведения собеседований

```properties
ASPNETCORE_ENVIRONMENT=PreProduction

INTERVIEW_BACKEND_LOGGING__LOGLEVEL__DEFAULT=Trace
INTERVIEW_BACKEND_LOGGING__LOGLEVEL__MICROSOFT.ASPNETCORE=Debug
INTERVIEW_BACKEND_LOGGING__LOGLEVEL__ASPNET=Debug
INTERVIEW_BACKEND_LOGGING__LOGLEVEL__MICROSOFT=Debug

INTERVIEW_BACKEND_CORSOPTIONS__ALLOWEDORIGINS__0=http://backend-gateway:80,http://backend-gateway:80

INTERVIEW_BACKEND_CONNECTIONSTRINGS__DATABASE=Host=************;Port=5432;Username=************;Password=************;Database=sorface
INTERVIEW_BACKEND_CONNECTIONSTRINGS__TYPE=postgres

INTERVIEW_BACKEND_SWAGGEROPTION__ROUTEPREFIX=/

INTERVIEW_BACKEND_OPENIDCONNECTOPTIONS__ISSUER=http://backend-passport:8080

INTERVIEW_BACKEND_EVENTSTORAGE__ENABLED=true
INTERVIEW_BACKEND_EVENTSTORAGE__HOST=redis
INTERVIEW_BACKEND_EVENTSTORAGE__PORT=************
INTERVIEW_BACKEND_EVENTSTORAGE__USERNAME=************
INTERVIEW_BACKEND_EVENTSTORAGE__PASSWORD=************
```
