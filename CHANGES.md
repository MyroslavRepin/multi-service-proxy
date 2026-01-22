# Что изменилось - Интеграция Reflectly

## Проблема решена ✅

Ошибка `service "reflectly" not found` исправлена!

## Что было сделано:

1. **Обновлен корневой docker-compose.yml**
   - Убран `extends` (который не работал)
   - Добавлены все сервисы Reflectly напрямую:
     - `reflectly-server` (backend на порту 8080)
     - `reflectly-frontend` (Vue frontend на порту 5173)
     - `reflectly-db` (PostgreSQL база данных)

2. **Обновлена nginx конфигурация**
   - Изменено имя upstream: `reflectly` → `reflectly-server`

3. **Все сервисы в одной сети**
   - Сеть `backend` объединяет: nginx, reflectly-server, reflectly-frontend, reflectly-db, app_2

## Теперь можно запускать:

```bash
# Добавьте в /etc/hosts
sudo sh -c 'echo "127.0.0.1 reflectly.myroslavrepin.com" >> /etc/hosts'
sudo sh -c 'echo "127.0.0.1 app2.localhost" >> /etc/hosts'

# Запустите все сервисы
docker compose up -d --build

# Проверьте статус
docker compose ps
```

## Ожидаемый результат:

```
NAME                 STATUS    PORTS
gateway-nginx        running   0.0.0.0:8080->80/tcp
reflectly-server     running   8080/tcp
reflectly-frontend   running   0.0.0.0:5173->5173/tcp
reflectly-db         running   5432/tcp
app_2                running   8000/tcp
```

## Тестирование:

```bash
# Backend через nginx
curl http://reflectly.myroslavrepin.com:8080/

# Frontend напрямую
open http://localhost:5173
```

## Важно:

- **Frontend** доступен на: http://localhost:5173
- **Backend через nginx**: http://reflectly.myroslavrepin.com:8080
- **База данных**: доступна внутри сети как `reflectly-db:5432`

## Если что-то не работает:

```bash
# Смотрите логи
docker compose logs -f reflectly-server
docker compose logs -f reflectly-db
docker compose logs -f nginx

# Перезапустите
docker compose down
docker compose up -d --build
```
