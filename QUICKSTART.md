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
- `reflectly` (reflectly на внутреннем порту 8080)
- `app_2` (demo app на внутреннем порту 8000)

## 🧪 Тестирование

```bash
# Тест reflectly
curl http://reflectly.myroslavrepin.com:8080/

# Тест app2
curl http://app2.localhost:8080/
```

В браузере:
- http://reflectly.myroslavrepin.com:8080
- http://app2.localhost:8080

## 📊 Логи

```bash
# Все сервисы
docker compose logs -f

# Только reflectly
docker compose logs -f reflectly

# Только nginx
docker compose logs -f nginx
```

## 🔄 Перезапуск после изменений

```bash
# Пересобрать и перезапустить все
docker compose down
docker compose up -d --build

# Только reflectly
docker compose up -d --build reflectly
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

### Reflectly
- **Внутренний порт**: 8080
- **Контейнер**: reflectly
- **Конфиг nginx**: `nginx/conf.d/reflectly.conf`

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
# Проверьте, запущен ли reflectly
docker compose ps reflectly
docker compose logs reflectly
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
# Проверьте docker-compose.yml в reflectly/
cd reflectly
docker compose config
```
