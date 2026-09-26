---
order: 1
title: Зайцева Дарья
aliases:
  - path: >-
      arkhitekturnye-kata/sistema-onlayn-prodazhi-biletov-dlya-quickti/shablon/fio-shablon
    moved: "2026-09-26T04:34:04Z"
---

## 1\. 📖 Описание функциональных границ микросервиса

### 1\.1 Диаграмма компонентов архитектуры

```graphql
graph TD
    WEB["Web Client"] --> API["API Gateway"]
    API --> AUTH["Auth Service"]
    API --> BOOK["MS Bookings"]
    BOOK --> DB[("PostgreSQL: rooms, bookings")]
```

### 1\.2 Описание микросервиса

MS Bookings обеспечивает следующую функциональность:

-  **Поиск свободных переговорных комнат** на заданный интервал времени

-  **Создание брони** на выбранную комнату и интервал, без двойных броней 

-  **Отмена брони**, доступная только сотруднику, создавшему бронь

-  **Перенос брони** на другой интервал с той же гарантией отсутствия пересечений

-  **Инициирование уведомлений** о создании/отмене/переносе брони 

## 2\. 🧩 Концептуальное проектирование API метода

{% table header="row" %}

---

*  {% colwidth=[156] %}

   Потребители

*  {% colwidth=[156] %}

   Цель

*  {% colwidth=[156] %}

   Задачи

*  {% colwidth=[192] %}

   Входные данные

*  {% colwidth=[239] %}

   Выходные данные

---

*  {% colwidth=[156] %}

   

*  {% colwidth=[156] %}

   

*  {% colwidth=[156] %}

   

*  {% colwidth=[192] %}

   

*  {% colwidth=[239] %}

   

---

*  {% colwidth=[156] %}

   

*  {% colwidth=[156] %}

   

*  {% colwidth=[156] %}

   

*  {% colwidth=[192] %}

   

*  {% colwidth=[239] %}

   

{% /table %}

## 3\. 🤝 Swagger