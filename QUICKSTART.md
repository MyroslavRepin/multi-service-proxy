# Быстрый старт - Управление Multi-Service Proxy

## 📋 Что обновлено

✅ `app_1` заменён на `reflectly`
✅ Reflectly работает на порту 8080
✅ Домен: `reflectly.myroslavrepin.com`
✅ Nginx проксирует запросы на reflectly:8080

## 🚀 Запуск всей системы

### 1. Настройте hosts файл

```bash
sudo nano /etc/hosts
```

Добавьте:
```
127.0.0.1 reflectly.myroslavrepin.com
127.0.0.1 app2.localhost
```

### 2. Запустите все сервисы

```bash
docker compose up -d --build
```

### 3. Проверьте статус

```bash
docker compose ps
```

Должны работать:
- `gateway-nginx` (nginx на порту 8080)
- `reflectly-server` (reflectly backend на внутреннем порту 8080)
- `reflectly-frontend` (Vue frontend на порту 5173)
- `reflectly-db` (PostgreSQL database на внутреннем порту 5432)
- `app_2` (demo app на внутреннем порту 8000)

## 🧪 Тестирование

```bash
# Тест reflectly backend через nginx
curl http://reflectly.myroslavrepin.com:8080/

# Тест app2
curl http://app2.localhost:8080/
```

В браузере:
- **Reflectly Backend (через nginx)**: http://reflectly.myroslavrepin.com:8080
- **Reflectly Frontend**: http://localhost:5173
- **App2**: http://app2.localhost:8080

## 📊 Логи

```bash
# Все сервисы
docker compose logs -f

# Только reflectly backend
docker compose logs -f reflectly-server

# Только reflectly frontend
docker compose logs -f reflectly-frontend

# Только nginx
docker compose logs -f nginx

# Только база данных
docker compose logs -f reflectly-db
```

## 🔄 Перезапуск после изменений

```bash
# Пересобрать и перезапустить все
docker compose down
docker compose up -d --build

# Только reflectly backend
docker compose up -d --build reflectly-server

# Только reflectly frontend
docker compose up -d reflectly-frontend
```

## 🛑 Остановка

```bash
docker compose down
```

## 📁 Структура проекта

```
multi-service-proxy/
├── docker-compose.yml          # Главный orchestrator
├── reflectly/
│   ├── docker-compose.yml     # Конфигурация reflectly
│   └── README.md              # Инструкции для reflectly
├── app_2/
│   └── Dockerfile
└── nginx/
    ├── nginx.conf
    └── conf.d/
        ├── reflectly.conf     # Прокси для reflectly
        └── app2.conf          # Прокси для app2
```

## ⚙️ Важные настройки

### Reflectly Backend
- **Внутренний порт**: 8080
- **Контейнер**: reflectly-server
- **Конфиг nginx**: `nginx/conf.d/reflectly.conf`
- **База данных**: PostgreSQL (reflectly-db:5432)

### Reflectly Frontend
- **Порт**: 5173
- **Контейнер**: reflectly-frontend
- **Framework**: Vue 3 + Vite
- **API URL**: http://localhost:8080

### Nginx
- **Внешний порт**: 8080 (host:8080 → container:80)
- **Маршрутизация**: по server_name (Host header)

## 🔧 Разработка Reflectly отдельно

Если нужно работать только с reflectly без nginx:

```bash
cd reflectly
docker compose up -d
```

Доступ: http://localhost:8080

## ❓ Проблемы?

### 502 Bad Gateway
```bash
# Проверьте, запущен ли reflectly-server
docker compose ps reflectly-server
docker compose logs reflectly-server
```

### Cannot connect
```bash
# Проверьте hosts файл
cat /etc/hosts | grep reflectly

# Проверьте, что порт 8080 свободен
lsof -i :8080
```

### Reflectly не стартует
```bash
# Проверьте логи всех сервисов reflectly
docker compose logs reflectly-server
docker compose logs reflectly-frontend
docker compose logs reflectly-db
```

### База данных не подключается
```bash
# Проверьте статус БД
docker compose ps reflectly-db
docker compose logs reflectly-db

# Подключитесь к БД напрямую
docker exec -it reflectly-db psql -U root -d reflectly
```
