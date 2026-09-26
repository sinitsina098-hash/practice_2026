---
order: 1.5
title: Кошелева Анна Константиновна
aliases:
  - path: >-
      arkhitekturnye-kata/set-medicinskikh-klinik/arkhitekturnoe-kata-edupath-platforma-onla/kosheleva-anna-konstantinovna
    moved: "2026-09-26T04:43:32Z"
  - path: >-
      arkhitekturnye-kata/set-medicinskikh-klinik/bally-za-vypolnenie-laboratornoy-rest-api-maximu/kosheleva-anna-konstantinovna
    moved: "2026-09-26T04:43:38Z"
---

## 1\. 📖 Описание функциональных границ микросервиса

### 1\.1 Диаграмма компонентов архитектуры

![](./kosheleva-anna-konstantinovna.webp){width=2122px height=1138px}

![](./kosheleva-anna-konstantinovna-2.webp){width=2122px height=1396px}

### 1\.2 Описание микросервиса clients-srv

*Назначение:* управление учётными записями клиентов (владельцев питомцев) -- регистрация, изменение и удаление данных, синхронизация с микросервисом питомцев.

Пароль будет хранить внешний провайдер (например, keycloak). FE получает токен от ldP, и с ним ходит в clients-srv, поэтому эндпоинт с входом/выходом я убрала. Функциональность:

-  **Регистрация** клиента: создание новой УЗ, инициализация базовых атрибутов (роль, дата обновления/создания).

-  **Обновление/удаление** личных данных пользователя.

-  **Синхронизация с карточкой питомца:** обеспечение актуальности данных о питомцах, привязанных к пользователю.

## 2\. 🧩 Концептуальное проектирование API метода

Уточнение: В pets id питомцев остались int64, потому что в микросервисе питомцев будут использоваться внутренние идентификаторы. Помимо того, не запрашиваем каждый раз в качестве хедера AUthorization (jwtToken), так как считаем его security-требованием.

{% table header="row" %}

---

*  {% colwidth=[134] %}

   Потребители

*  {% colwidth=[174] %}

   Цель

*  {% colwidth=[383] %}

   Задачи

*  {% colwidth=[192] %}

   Входные данные

*  {% colwidth=[329] %}

   Выходные данные

---

*  {% colwidth=[134] %}

   FE PetCare

*  {% colwidth=[174] %}

   регистрация пользователя при первичном входе на сайт

*  {% colwidth=[383] %}

   создать учетную запись для нового пользователя (обяз. параметры: id = sub из IdP, имя УЗ, имя, фамилия, почта)

*  {% colwidth=[192] %}

   ```
   {
     "id": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
     "userName": "petrivanov",
     "firstName": "Петр",
     "lastName": "Иванов",
     "email": "useremail@example.com"
   }
   ```

*  {% colwidth=[329] %}

   201:

   ```
   {
     "id": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
     "userName": "petrivanov",
     "firstName": "Петр",
     "lastName": "Иванов",
     "email": "useremail@example.com",
     "pets": [
       123,
       234,
       345
     ],
     "role": "user",
     "createdAt": "2026-10-09T10:30:00Z",
     "updatedAt": "2026-10-09T10:30:00Z",
     "deletedAt": null
   }
   ```

---

*  {% colwidth=[134] %}

   FE PetCare

*  {% colwidth=[174] %}

   обновление учетных данных в УЗ в случае, если пользователю хочется сменить имя пользователя или у него сменилась почта

*  {% colwidth=[383] %}

   изменить один из параметров (как минимум 1 из): имя пользователя, почта

*  {% colwidth=[192] %}

   id (== sub из IdP)

   ```
   {
     "userName": "petrivanov",
     "email": "useremail@example.com"
   }
   ```

*  {% colwidth=[329] %}

   200:

   ```
   {
     "id": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
     "userName": "petrivanov",
     "firstName": "Петр",
     "lastName": "Иванов",
     "email": "useremail@example.com",
     "pets": [
       123,
       234,
       345
     ],
     "role": "user",
     "createdAt": "2026-10-09T10:30:00Z",
     "updatedAt": "2026-10-09T10:30:00Z",
     "deletedAt": null
   }
   ```

---

*  {% colwidth=[134] %}

   FE PetCare

*  {% colwidth=[174] %}

   удалить аккаунт в случае, если услуги сайта становятся не актуальны для пользователя/другая причина

*  {% colwidth=[383] %}

   сделать запрос в БД по id для удаления соответствующей записи об УЗ пользователя (SELECT и потом UPDATE deletedAt == True)

*  {% colwidth=[192] %}

   id (== sub из IdP)

*  {% colwidth=[329] %}

   200:

   ```
   {
     "id": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
     "userName": "petrivanov",
     "firstName": "Петр",
     "lastName": "Иванов",
     "email": "useremail@example.com",
     "pets": [
       123,
       234,
       345
     ],
     "role": "user",
     "createdAt": "2026-10-09T10:30:00Z",
     "updatedAt": "2026-10-09T10:30:00Z",
     "deletedAt": "2026-10-09T10:30:00Z"
   }
   ```

---

*  {% colwidth=[134] %}

   FE PetCare, clients-srv и микросервис питомцев

*  {% colwidth=[174] %}

   получение пользователя

*  {% colwidth=[383] %}

   проверить полномочия пользователя в системе: проверяется параметр role. Допускаю (но не в рамках этого микросервиса), что после проверки если role == '‘admin’, то пользователь может удалять другие аккаунты или записи к ветеринару/назначать другим пользователей админами/иметь другой дополнительный функционал. пользователь с role == ‘user’ имеет базовый функционал с созданием карточки питомца, записью его к ветеринару

*  {% colwidth=[192] %}

   id (== sub из IdP)

*  {% colwidth=[329] %}

   200:

   ```
   {
     "id": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
     "userName": "petrivanov",
     "firstName": "Петр",
     "lastName": "Иванов",
     "email": "useremail@example.com",
     "pets": [
       123,
       234,
       345
     ],
     "role": "user",
     "createdAt": "2026-10-09T10:30:00Z",
     "updatedAt": "2026-10-09T10:30:00Z",
     "deletedAt": null
   }
   ```

{% /table %}

Таким образом, как я вижу процесс:

{% table header="row" %}

---

*  {% colwidth=[178] %}

   Шаг

*  {% colwidth=[256] %}

   Описание

*  {% colwidth=[235] %}

   Метод API

---

*  {% colwidth=[178] %}

   Регистрация

*  {% colwidth=[256] %}

   Пользователь регистрируется на сайте

*  {% colwidth=[235] %}

   POST /user

---

*  {% colwidth=[178] %}

   Получение пользователя

*  {% colwidth=[256] %}

   Получение данных пользователя, включая role

*  {% colwidth=[235] %}

   GET /user/\{id}

---

*  {% colwidth=[178] %}

   Обновление пользователя

*  {% colwidth=[256] %}

   Обновление пользователя

*  {% colwidth=[235] %}

   PATCH /user/\{id}

---

*  {% colwidth=[178] %}

   Удаление пользователя

*  {% colwidth=[256] %}

   Удаление пользователя

*  {% colwidth=[235] %}

   DELETE /user/\{id}

{% /table %}

![](./kosheleva-anna-konstantinovna-3.webp){width=1063px height=1467px}

## 3\. 🤝 Swagger

[swagger.json](./swagger.json) на всякий случай, так как ниже почему-то нет тела ответов в ответах.

[openapi:./_index-2-2.yaml:true]

То есть имеем:

![](./kosheleva-anna-konstantinovna-4.webp){width=413px height=751px}

Связь с id питомцев будет по uuid == owner_id. БД clients:

![](./kosheleva-anna-konstantinovna-5.webp){width=331px height=465px}