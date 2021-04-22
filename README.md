# Django + DjangoRESTFramework + Vue Application

## Installation

`pip install django djangorestframework django-cors-headers`

On `settings.py` file:

1. Put `rest_framework` and `corsheaders` in `INSTALLED_APPS`.

2. Put `corsheaders.middleware.CorsMiddleware` in `MIDDLEWARE`.

3. Put `127.0.0.1` or `localhost` in `ALLOWED_HOST` list.

4. Create new variable `CORS_ORIGIN_ALLOW_ALL` with `True` value.

5. Run `python manage.py makemigrations`.

6. Run `python manage.py migrate`.

7. Create superuser by running `python manage.py createsuperuser`.
