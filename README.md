
# Kittygram - инстаграм для котиков:3

Китиграм - это проект, представляющий собой платформу для публикации фотографий котиков, аналогичную Instagram. Пользователи могут создавать аккаунты и делиться красивыми фотографиями своих котиков с сообществом. Каждая фотография может сопровождаться информацией, включая имя котика, год рождения и цвет шерсти.



![Logo](https://lh3.googleusercontent.com/u/0/drive-viewer/AITFw-xud4Wg2aePLw9pVqYwfmvBLmh3Cyc-4q9g88twMwUU3T3e6XkWZVU3yjccytFxUX3tUS0TvfQ5TBwxkeaYA2Wk5IWg4g=w1920-h963)


## Стек технологий

**Клиент:**
React (JavaScript)

**Сервер:** Django (Django REST) + Python 3.9, nginx (веб-сервер), gunicorn (WSGI-сервер), PostgreSQL (база данных)

**Дополнительно:** Docker (контейнеризация)
## Развертывание

Для развертывания проекта выполните команды:

```bash
sudo apt update
sudo apt install curl
curl -fSL https://get.docker.com -o get-docker.sh
sudo sh ./get-docker.sh
sudo apt-get install docker-compose-plugin;
# Переходим в директорию проекта.
cd kittygram_final/
# Создаем и редактируем файл .env, в котором нужно указать данные
sudo nano .env
POSTGRES_DB=<Желаемое имя базы данных>
POSTGRES_USER=<Желаемое имя пользователя базы данных>
POSTGRES_PASSWORD=<Желаемый пароль пользователя базы данных>
DB_HOST=db
DB_PORT=5432
SECRET_KEY=<Укажите ваш секретный ключ от Джанги>
DEBUG=False
ALLOWED_HOSTS=<Укажите разрешенные хосты>
TIME_ZONE=UTC
USE_I18N=True
USE_L10N=True
USE_TZ=True
# Далее выполняем последовательно
sudo docker compose -f docker-compose.production.yml pull
sudo docker compose -f docker-compose.production.yml down
sudo docker compose -f docker-compose.production.yml up -d
sudo docker compose -f docker-compose.production.yml exec backend python manage.py migrate
sudo docker compose -f docker-compose.production.yml exec backend python manage.py collectstatic
sudo docker compose -f docker-compose.production.yml exec backend cp -r /app/collect_static/. /static_backend/static/
# Создаем суперпользователся. Следуем инструкциям при выполнении.
sudo docker compose -f docker-compose.production.yml exec backend python manage.py createsuperuser

# Генерируем новый секретный ключ Django
sudo docker compose -f docker-compose.production.yml exec backend python -c 'from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())'
# В ответ будет примерно такая строка: cg6*%6d51ef8f#4!r3*$vmxm4)abgjw8mo!4y-q*uq1!4$-88$
# Вставляем в .env файл. Должно получиться примерно такое:
DJANGO_KEY=django-insecure-cg6*%6d51ef8f#4!r3*$vmxm4)abgjw8mo!4y-q*uq1!4$-88$
```


## Устанавливаем и настраиваем NGINX
```bash
# Устанавливаем NGINX
sudo apt install nginx -y
# Запускаем
sudo systemctl start nginx
# Настраиваем firewall
sudo ufw allow 'Nginx Full'
sudo ufw allow OpenSSH
# Включаем firewall
sudo ufw enable
# Открываем конфигурационный файл NGINX
sudo nano /etc/nginx/sites-enabled/default
# Полностью удаляем из него все и пишем новые настройки

server {
    listen 80;
    server_name example.com;
    
    location / {
        proxy_set_header HOST $host;
        proxy_pass http://127.0.0.1:9000;

    }
}

# Сохраняем изменения и выходим из редактора
# Проверяем корректность настроек
sudo nginx -t
# Запускаем NGINX
sudo systemctl start nginx
```
## Подключаем SSL сертификат
```bash
# Установка пакетного менеджера snap.
# У этого пакетного менеджера есть нужный вам пакет — certbot.
sudo apt install snapd
# Установка и обновление зависимостей для пакетного менеджера snap.
sudo snap install core; sudo snap refresh core
# При успешной установке зависимостей в терминале выведется:
# core 16-2.58.2 from Canonical✓ installed 

# Установка пакета certbot.
sudo snap install --classic certbot
# При успешной установке пакета в терминале выведется:
# certbot 2.3.0 from Certbot Project (certbot-eff✓) installed

# Создание ссылки на certbot в системной директории,
# чтобы у пользователя с правами администратора был доступ к этому пакету.
sudo ln -s /snap/bin/certbot /usr/bin/certbot

# Получаем сертификат и настраиваем NGINX следуя инструкциям
sudo certbot --nginx
# Перезапускаем NGINX
sudo systemctl reload nginx
```
## 🔗 Автор
Александр Гавриленко

[![twitter](https://img.shields.io/badge/-telegram-red?color=white&logo=telegram&logoColor=black)](https://t.me/shirayanagi)
