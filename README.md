

<div align="center">
  <img width=200" height="67" src="https://i.ibb.co/Gp361Rp/YAMDB.jpg">
</div>

**REST API для сервиса YaMDb** — базы отзывов о фильмах, книгах и музыке. 

## Index
1. [Описание](#описание)
2. [Документация](#документация)
3. [Алгоритм регистрации пользователей](#алгоритм-регистрации-пользователей)
4. [Пользовательские роли](#пользовательские-роли)
5. [Доступные методы](#доступные-методы)
6. [Использование](использование)
7. [Технологии](#технологии)
8. [Участники](#участники)

## Описание

**API для сервиса YaMDb. Позволяет работать со следующими сущностями:**
 - **Отзывы**:
   - получить список всех отзывов;
   - создать новый отзыв; 
   - получить отзыв по id;
   - частично обновить отзыв по id;
   - удалить отзыв по id.
     
 - **Комментарии к отзывам**:
   - Получить список всех комментариев к отзыву по `id`;
   - создать новый комментарий для отзыва, получить комментарий для отзыва по `id`;
   - частично обновить комментарий к отзыву по `id`;
   - удалить комментарий к отзыву по `id`.
    
 - **JWT-токен**:
   - Отправление confirmation_code на переданный `email`;
   - получение JWT-токена в обмен на `email` и `confirmation_code`.
    
 - **Пользователи**:
   - получить список всех пользователей; 
   - создание пользователя получить пользователя по `username`;
   - изменить данные пользователя по `username`;
   - удалить пользователя по `username`;
   - получить данные своей учетной записи;
   - изменить данные своей учетной записи.
 
- **Категории (типы) произведений**:
   - получить список всех категорий;
   - создать категорию;
   - удалить категорию.
  
- **Категории жанров**:
   - получить список всех жанров
   - создать жанр;
   - удалить жанр.
  
- **Произведения, к которым пишут отзывы**: 
    - получить список всех объектов;
    - создать произведение для отзывов;
    - информация об объекте;
    - обновить информацию об объекте;
    - удалить произведение. 
    
## Документация
[Полная документация API](https://github.com/yandex-praktikum/api_yamdb/blob/master/static/redoc.yaml)


### Алгоритм регистрации пользователей

1. Пользователь отправляет запрос с параметром email на `/auth/email/`.
2. YaMDB отправляет письмо с кодом подтверждения `(confirmation_code)` на адрес `email`.
3. Пользователь отправляет запрос с параметрами `email` и `confirmation_code` на `/auth/token/`, 
в ответе на запрос ему приходит `token` (JWT-токен).
4. При желании пользователь отправляет PATCH-запрос на `/users/me/` и заполняет поля 
в своём профайле (описание полей — в документации).
   
### Пользовательские роли

- **Аноним** — может просматривать описания произведений, читать отзывы и комментарии.
- **Аутентифицированный пользователь** — может, как и Аноним, читать всё, дополнительно он может 
публиковать отзывы и ставить рейтинг произведениям (фильмам/книгам/песням), может комментировать 
чужие отзывы и ставить им оценки; может редактировать и удалять свои отзывы и комментарии.
- **Модератор** — те же права, что и у Аутентифицированного пользователя плюс право удалять любые отзывы и комментарии.
- **Администратор** — полные права на управление проектом и всем его содержимым. 
Может создавать и удалять категории и произведения. Может назначать роли пользователям.
- **Администратор Django** — те же права, что и у роли Администратор.

### Доступные методы:

```
/api/v1/posts (GET, POST, PUT, PATCH, DELETE)
```
```
/api/v1/posts/<id> (GET, POST, PUT, PATCH, DELETE)
```
```
/api/v1/posts/<id>/comments (GET, POST, PUT, PATCH, DELETE)
```
```
/api/v1/posts/<id>/comments/<id> (GET, POST, PUT, PATCH, DELETE)
```
```
/api/v1/group/ (GET, POST)
```
```
/api/v1/follow/ (GET, POST)
```

## Использование

1) Создаем `.env` и заполняем переменные окружения:

```shell
vim .env
```
```text
SECRET_KEY=
DEBUG=
DJANGO_ALLOWED_HOSTS=
DEFAULT_FROM_EMAIL=

DB_ENGINE=
DB_NAME=
POSTGRES_USER=
POSTGRES_PASSWORD=
DB_HOST=
DB_PORT=
```


2) Устанавливаем [Docker](https://docs.docker.com/engine/install/)

3) Собираем `docker-compose` в detach mode (background):
```shell
docker-compose up -d --build
```

4) Собираем статические файлы в `STATIC_ROOT`:
```shell
docker-compose exec web python3 manage.py collectstatic --noinput
```
<details>
<summary>STDOUT</summary>

```shell
163 static files copied to '/usr/src/web/static'.
```

</details>

5) Запускаем миграции:
```shell
docker-compose exec web python3 manage.py migrate --noinput
```
<details>
<summary>STDOUT</summary>

```shell
Operations to perform:
  Apply all migrations: admin, auth, comments, content, contenttypes, sessions, users
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0001_initial... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying auth.0009_alter_user_last_name_max_length... OK
  Applying auth.0010_alter_group_name_max_length... OK
  Applying auth.0011_update_proxy_permissions... OK
  Applying users.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  Applying content.0001_initial... OK
  Applying content.0002_auto_20201119_1626... OK
  Applying content.0003_auto_20201119_1629... OK
  Applying content.0004_auto_20201119_1712... OK
  Applying content.0005_auto_20201119_1719... OK
  Applying content.0006_auto_20201119_1720... OK
  Applying comments.0001_initial... OK
  Applying comments.0002_reviews_title... OK
  Applying comments.0003_auto_20201114_2342... OK
  Applying comments.0004_auto_20201115_0124... OK
  Applying comments.0005_auto_20201115_1214... OK
  Applying content.0007_auto_20201119_1752... OK
  Applying comments.0006_auto_20201121_1730... OK
  Applying comments.0007_auto_20201121_1746... OK
  Applying comments.0008_auto_20201121_2128... OK
  Applying content.0008_auto_20201123_1330... OK
  Applying content.0009_auto_20210207_1135... OK
  Applying sessions.0001_initial... OK
  Applying users.0002_auto_20210207_1843... OK
  Applying users.0003_auto_20210207_1848... OK
```

</details>


6) Наполняем `Postgres` данными:
```shell
docker-compose exec web python3 manage.py loaddata fixture.json
```
<details>
<summary>STDOUT</summary>

```shell
Installed 194 object(s) from 1 fixture(s)
```

</details>


7) Создаем суперпользователя Django:
```shell
docker-compose run web python manage.py createsuperuser
```
<details>
<summary>STDOUT</summary>

```shell
Username:
Email address:
Password: 
Password (again):
Superuser created successfully.
```
</details>

8) Останавливаем и удаляем контейнеры, сети, тома и образы:
```shell
docker-compose down -v
```
<details>
<summary>STDOUT</summary>

```shell
Stopping nginx           ... done
Stopping infra_sp2_web_1 ... done
Stopping infra_sp2_db_1  ... done
Removing infra_sp2_web_run_2637ec89582d ... done
Removing nginx                          ... done
Removing infra_sp2_web_1                ... done
Removing infra_sp2_db_1                 ... done
Removing network infra_sp2_default
Removing volume infra_sp2_postgres_data
Removing volume infra_sp2_static_data
Removing volume infra_sp2_media_data
```
</details>

## Технологии
- [Python](https://www.python.org/)
- [Django](https://www.djangoproject.com/)
- [Django REST framework](https://www.django-rest-framework.org/)
- [DRF Simple JWT](https://django-rest-framework-simplejwt.readthedocs.io/en/latest/)

## Участники 
 - [vamotest](https://github.com/vamotest) - управление пользователями (Auth и Users): 
   - система регистрации и аутентификации;
   - права доступа, работа с токеном;
   - система подтверждения e-mail, поля.
 - [kleine-alberta](https://github.com/kleine-alberta) - категории (Categories), жанры (Genres) и произведения (Titles): 
   - модели и view; 
   - эндпойнты.
 - [MrGrumpy87](https://github.com/MrGrumpy87) - отзывы (Review) и комментарии (Comments): 
   - модели и view;
   - эндпойнты;
   - права доступа для запросов;
   - рейтинги произведений.