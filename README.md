
# Проект YaMDb

## Описание
Проект YaMDb собирает отзывы (Review) пользователей на произведения (Title). Произведения делятся на категории: "Книги", "Фильмы", "Музыка". Список категорий (Category) может быть расширен.
Сами произведения в YaMDb не хранятся, здесь нельзя посмотреть фильм или послушать музыку.
В каждой категории есть произведения: книги, фильмы или музыка. Например, в категории "Книги" могут быть произведения "Винни Пух и все-все-все" и "Марсианские хроники", а в категории "Музыка" — песня "Давеча" группы "Насекомые" и вторая сюита Баха. Произведению может быть присвоен жанр из списка предустановленных (например, "Сказка", "Рок" или "Артхаус"). Новые жанры может создавать только администратор.
Благодарные или возмущённые читатели оставляют к произведениям текстовые отзывы (Review) и выставляют произведению оценку в диапазоне от одного до десяти (целое число); из пользовательских оценок формируется усреднённая оценка произведения — рейтинг (целое число). На одно произведение пользователь может оставить только один отзыв.

## Проект использует следующие технологии:
Python 3.8.10
Django 4.0.5
Django REST Framework 3.12.4
Djangorestframework-simplejwt 5.2.0
Django-filter 2.0.0
PyJWT 2.1.0
Requests 2.26.0

## Ресурсы API YaMDb
**AUTH**: аутентификация.

**USERS**: пользователи.

**TITLES**: произведения, к которым пишут отзывы (определённый фильм, книга или песенка).

**CATEGORIES**: категории (типы) произведений ("Фильмы", "Книги", "Музыка").

**GENRES**: жанры произведений. Одно произведение может быть привязано к нескольким жанрам.

**REVIEWS**: отзывы на произведения. Отзыв привязан к определённому произведению.

**COMMENTS**: комментарии к отзывам. Комментарий привязан к определённому отзыву.

## Алгоритм регистрации пользователей
Пользователь отправляет POST-запрос с параметром email на `/api/v1/auth/email/`.
YaMDB отправляет письмо с кодом подтверждения (confirmation_code) на адрес email (функция в разработке).
Пользователь отправляет POST-запрос с параметрами email и confirmation_code на `/api/v1/auth/token/`, в ответе на запрос ему приходит token (JWT-токен).
Эти операции выполняются один раз, при регистрации пользователя. В результате пользователь получает токен и может работать с API, отправляя этот токен с каждым запросом.

## Пользовательские роли
**Аноним** — может просматривать описания произведений, читать отзывы и комментарии.

**Аутентифицированный пользователь (user)** — может читать всё, как и Аноним, дополнительно может публиковать отзывы и ставить рейтинг произведениям (фильмам/книгам/песенкам), может комментировать чужие отзывы и ставить им оценки; может редактировать и удалять свои отзывы и комментарии.

**Модератор (moderator)** — те же права, что и у Аутентифицированного пользователя плюс право удалять и редактировать любые отзывы и комментарии.

**Администратор (admin)** — полные права на управление проектом и всем его содержимым. Может создавать и удалять произведения, категории и жанры. Может назначать роли пользователям.

**Администратор Django** — те же права, что и у роли Администратор.

## Установка проекта
1. Установите pyhton;
2. Склонируйте проект в локальную директорию;
3. Перейдите в корневую директорию проекта;
4. Разверните виртуальное окружение командой:
```
python3 -m venv venv
```
4. Активируйте виртуальное окружение командой:
```
source venv/bin/activate
```
5. Установите все необходимые зависимости командой:
```
pip install -r requirements.txt
```
6. Перейдите в директорию yatube_api:
```
cd api_yamdb
```
8. Выполните миграции командой:
```
python3 manage.py migrate
```

9. Создайте суперпользователя командой:
```
python3 manage.py createsuperuser
```
10. В каталог static положить тестовые данные для БД и запустить management команду:
```
python3 manage.py csv2db
```
#### Запустить проект:
```
python3 manage.py runserver
```

Проект запущен и доступен по адресу [localhost:8000](http://localhost:8000/).

## Примеры запросов к API

Регистрация пользователя через *username* и *email*. На почту отправляется код подтверждения *confirmation_code*:
````
POST http://127.0.0.1:8000/api/v1/auth/signup/
{"email": "string", "username": "string"}
````
Получение списка всех произведений:
````
GET http://127.0.0.1:8000/api/v1/titles/
````
Добавить новое произведение (необходимы права Администратора):
````
POST http://127.0.0.1:8000/api/v1/titles/
{
  "name": "string",
  "year": 0,
  "description": "string",
  "genre": ["string"],
  "category": "string"
}

Пример ответа:
[
  {
    "count": 0,
    "next": "string",
    "previous": "string",
    "results": [
      {
        "id": 0,
        "name": "string",
        "year": 0,
        "rating": 0,
        "description": "string",
        "genre": [
          {
            "name": "string",
            "slug": "string"
          }
        ],
        "category": {
          "name": "string",
          "slug": "string"
        }
      }
    ]
  }
]
````
Получение списка всех отзывов на произведение:
````
GET http://127.0.0.1:8000/api/v1/titles/{title_id}/reviews/

Пример ответа:
[
  {
    "count": 0,
    "next": "string",
    "previous": "string",
    "results": [
      {
        "id": 0,
        "text": "string",
        "author": "string",
        "score": 1,
        "pub_date": "2019-08-24T14:15:22Z"
      }
    ]
  }
]
````
Добавление нового отзыва (пользователь может оставить только один отзыв на произведение, оценка от 1 до 10):
````
POST http://127.0.0.1:8000/api/v1/titles/{title_id}/reviews/
{
  "text": "string",
  "score": 1
}

Пример ответа:
{
  "id": 0,
  "text": "string",
  "author": "string",
  "score": 1,
  "pub_date": "2019-08-24T14:15:22Z"
}
````
Документация и проверочные запросы доступны по адресу: `http://127.0.0.1:8000/redoc/`