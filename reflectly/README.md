# Reflectly Service

Этот каталог содержит конфигурацию для сервиса Reflectly.

## Локальная разработка

Для локальной разработки только Reflectly (без nginx proxy):

```bash
cd reflectly
docker compose up -d
```

Доступ: http://localhost:8080

## Интеграция с главным проектом

Reflectly автоматически запускается через главный docker-compose.yml в корне проекта.

```bash
cd ..  # Вернитесь в корневую папку
docker compose up -d
```

Доступ через nginx: http://reflectly.myroslavrepin.com:8080

## Конфигурация

- **Порт**: 8080
- **Домен**: reflectly.myroslavrepin.com
- **Сеть**: backend (shared with nginx)

## Структура

Поместите сюда:
- `Dockerfile` - для сборки образа
- `.env` - переменные окружения (не коммитить!)
- Исходный код приложения
- Другие конфигурационные файлы

## Примечания

- Убедитесь, что порт 8080 внутри контейнера совпадает с настройками nginx
- Для production используйте переменные окружения из .env файла
