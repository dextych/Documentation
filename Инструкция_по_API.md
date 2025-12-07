
API для загрузки данных клиентов позволяет партнёрам (сувенирным магазинам, производителям) интегрировать свои каталоги товаров с платформой Tripzy. API предоставляет RESTful интерфейс для массовой загрузки данных.

**Базовый URL:** `http://localhost:3000/api` (для разработки)

## Основные эндпоинты

|Метод|Эндпоинт|Назначение|
|---|---|---|
|`POST`|`/clients/v1/register`|Регистрация нового клиента-партнёра|
|`POST`|`/clients/v1/shops/bulk`|Массовая загрузка магазинов|
|`POST`|`/clients/v1/shopAdministrators`|Массовая загрузка администраторов магазинов|
|`POST`|`/clients/v1/products`|Массовая загрузка товаров (сувениров)|
|`POST`|`/clients/v1/productAttributes`|Массовая загрузка тегов для товаров|

---

## Аутентификация

Все эндпоинты, **кроме** регистрации клиента, требуют API-ключ в заголовке запроса:

`X-API-Key: ваш_api_ключ`

API-ключ выдается при регистрации клиента и является уникальным идентификатором для доступа ко всем ресурсам партнёра.

---

## Эндпоинты

#### `POST /api/clients/v1/register`

Регистрация новой компании-партнёра в системе. Возвращает уникальный API-ключ для дальнейших операций.

**Запрос:**

```http
POST /api/clients/v1/register
Content-Type: application/json
{
  "name": "Название вашей компании"
}
```
Ответ (успех):

```json

{
  "data": {
    "client": {
      "guid": "uuid-клиента",
      "name": "Название вашей компании",
      "apiKey": "ваш_api_ключ_для_дальнейших_запросов",
      "removalMark": false,
      "createdAt": "2025-12-05T10:00:00.000Z",
      "updatedAt": "2025-12-05T10:00:00.000Z"
    }
  }
}
```

> **Важно:** Сохраните `apiKey` из ответа - он понадобится для всех последующих запросов.

---

#### `POST /api/clients/v1/shops`

Массовая загрузка магазинов. Добавление магазинов, принадлежащих клиенту. Поддерживает загрузку нескольких магазинов в одном запросе.

**Запрос:**

```http
POST /api/clients/v1/shops
Content-Type: application/json
X-API-Key: ваш_api_ключ
{
  "shops": [
    {
      "name": "Сувениры России",
      "address": "ул. Ленина, д. 10",
      "city": "Москва",
      "region": "Москва",
      "schedule": null
    }
  ]
}
```

**Поля:**

- `name` (обязательно) — название магазина
    
- `address` (обязательно) — физический адрес
    
- `city` (обязательно) — город
    
- `region` (обязательно) — регион
    
- `schedule` (опционально) — график работы в формате JSON
    

**Ответ:**

```json
{
  "data": {
    "shops": [
      {
        "guid": "uuid-магазина-1",
        "clientGuid": "uuid-клиента",
        "name": "Сувениры России",
        "address": "ул. Ленина, д. 10",
        "city": "Москва",
        "region": "Москва",
        "schedule": null,
        "removalMark": false,
        "createdAt": "2025-12-05T10:00:00.000Z",
        "updatedAt": "2025-12-05T10:00:00.000Z"
      }
    ],
    "count": 1
  }
}
```

> **Важно:** Сохраните `guid` каждого магазина для последующих операций.

---

#### `POST /api/clients/v1/shop-administrators/bulk`

Создание учётных записей администраторов для управления конкретными магазинами.

**Запрос:**

```http
POST /api/clients/v1/shopAdministrators
Content-Type: application/json
X-API-Key: ваш_api_ключ
{
  "administrators": [
    {
      "shopGuid": "uuid-магазина-1",
      "email": "admin@example.com",
      "password": "password123",
      "firstName": "Иван",
      "lastName": "Иванов",
      "patronymicName": "Иванович",
      "phone": "+79001234567",
      "country": "Россия",
      "age": 35
    }
  ]
}
```

**Поля:**

- `shopGuid` (обязательно) — GUID магазина
    
- `email` (обязательно) — email администратора
    
- `password` (обязательно) — пароль для входа
    
- `firstName` (обязательно) — имя
    
- `lastName`, `patronymicName`, `phone`, `country`, `age` — опциональные поля
    

**Ответ:**

```json
{
  "data": {
    "administrators": [
      {
        "shopGuid": "uuid-магазина-1",
        "userGuid": "uuid-пользователя-1",
        "user": {
          "guid": "uuid-пользователя-1",
          "email": "admin@example.com",
          "firstName": "Иван",
          "lastName": "Иванов",
          "patronymicName": "Иванович",
          "phone": "+79001234567",
          "country": "Россия",
          "age": 35
        }
      }
    ],
    "count": 1
  }
}
```

---

#### `POST /api/clients/v1/products`

Добавление товаров в каталог магазина. Каждый товар привязывается к конкретному магазину.

**Запрос:**

```http
POST /api/clients/v1/products
Content-Type: application/json
X-API-Key: ваш_api_ключ
{
  "products": [
    {
      "shopGuid": "uuid-магазина-1",
      "name": "Матрёшка классическая",
      "price": 1500,
      "quantity": 50
    }
  ]
}
```

**Поля:**

- `shopGuid` (обязательно) — GUID магазина
    
- `name` (обязательно) — название товара
    
- `price` (обязательно) — цена в рублях
    
- `quantity` (обязательно) — количество на складе
    

**Ответ:**

```json
{
  "data": {
    "products": [
      {
        "guid": "uuid-товара-1",
        "shopGuid": "uuid-магазина-1",
        "name": "Матрёшка классическая",
        "price": 1500,
        "quantity": 50,
        "removalMark": false,
        "createdAt": "2025-12-05T10:00:00.000Z",
        "updatedAt": "2025-12-05T10:00:00.000Z"
      }
    ],
    "count": 1
  }
}
```

> **Важно:** Сохраните `guid` каждого товара для загрузки тегов.

---

#### `POST /api/clients/v1/productAttributes`

Категоризация товаров через систему тегов для улучшения работы рекомендательной системы.

**Запрос:**

```http
POST /api/clients/v1/product-tags/bulk
Content-Type: application/json
X-API-Key: ваш_api_ключ
{
  "productTags": [
    {
      "productGuid": "uuid-товара-1",
      "tag": "children"
    }
  ]
}
```

**Поля:**

- `productGuid` (обязательно) — GUID товара
    
- `tag` (обязательно) — тег из списка допустимых значений
    

**Доступные теги:**

- `children` — для детей
    
- `relatives` — для родственников
    
- `colleagues` — для коллег
    
- `friends` — для друзей
    
- `partner` — для партнёра
    
- `parents` — для родителей
    
- `myself` — для себя
    
- `other` — прочее
    

**Ответ:**

```json
{
  "data": {
    "productTags": [
      {
        "productGuid": "uuid-товара-1",
        "tag": "children",
        "createdAt": "2025-12-05T10:00:00.000Z",
        "updatedAt": "2025-12-05T10:00:00.000Z"
      }
    ],
    "count": 1
  }
}
```

---

## Обработка ошибок

### Коды состояний HTTP

| Код   | Название              | Описание                          |
| ----- | --------------------- | --------------------------------- |
| `200` | OK                    | Успешный запрос                   |
| `201` | Created               | Ресурс успешно создан             |
| `400` | Bad Request           | Некорректные данные в запросе     |
| `401` | Unauthorized          | Отсутствует или неверный API-ключ |
| `403` | Forbidden             | Доступ к ресурсу запрещён         |
| `404` | Not Found             | Ресурс не найден                  |
| `409` | Conflict              | Ресурс уже существует             |
| `422` | Unprocessable Entity  | Данные не прошли валидацию        |
| `429` | Too Many Requests     | Превышен лимит запросов           |
| `500` | Internal Server Error | Внутренняя ошибка сервера         |

### Формат ответа с ошибкой

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Неверный формат данных",
    "details": {
      "field": "price",
      "reason": "must be a positive number"
    }
  }
}
```
