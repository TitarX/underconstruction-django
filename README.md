# Under Construction

Простое Django-приложение "Сайт на реконструкции".

## Требования

- Python 3.11+
- Django 6.0+

## Установка для разработки

```bash
# Создать виртуальное окружение
python -m venv .venv

# Активировать (Windows)
.venv\Scripts\activate

# Активировать (Linux/Mac)
source .venv/bin/activate

# Установить зависимости
pip install -r requirements.txt

# Запустить сервер разработки
python manage.py runserver
```

## Деплой на продакшн

### 1. Настройка переменных окружения

Скопируйте `.env.example` в `.env` и заполните значения:

```bash
cp .env.example .env
```

Сгенерируйте новый SECRET_KEY:
```bash
python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
```

Пример `.env` файла:
```
SECRET_KEY=ваш-сгенерированный-секретный-ключ
DEBUG=False
ALLOWED_HOSTS=yourdomain.com,www.yourdomain.com
```

### 2. Сбор статических файлов

```bash
python manage.py collectstatic --noinput
```

### 3. Запуск с Gunicorn

```bash
gunicorn underconstruction.wsgi:application --bind 0.0.0.0:8000
```

### 4. Настройка Nginx (рекомендуется)

Пример конфигурации Nginx:

```nginx
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;

    location /static/ {
        alias /path/to/underconstruction/staticfiles/;
    }

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

## Структура проекта

```
underconstruction/
├── manage.py
├── requirements.txt
├── .env.example
├── .gitignore
├── README.md
├── main/                    # Основное приложение
├── templates/               # HTML шаблоны
├── static/                  # Статические файлы (dev)
│   └── images/
│       └── construction.jpg
├── staticfiles/             # Собранная статика (prod)
└── underconstruction/       # Настройки проекта
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```

## Безопасность

В продакшн режиме (DEBUG=False) автоматически включаются:
- HTTPS редирект
- Secure cookies
- HSTS headers
- XSS protection
- Content type nosniff
- X-Frame-Options: DENY
