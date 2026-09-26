---
order: 1
title: Терин Иван (Ticket Service, микросервис по покупке и бронированию билетов)
---

## 1\. 📖 Описание функциональных границ микросервиса

### 1\.1 Диаграмма компонентов архитектуры

```
'''mermaid 
flowchart TD
    subgraph Clients [Клиентские приложения]
        Web[Web-сайт]
        Mobile[Mobile App]
    end

    Gateway[API Gateway]

    subgraph Core [Ядро системы]
        TicketService[Ticket Service\nУправление бронированием\nи покупкой билетов]
    end

    subgraph Microservices [Доменные микросервисы]
        Catalog[Catalog Service\nСправочник мероприятий]
        Payment[Payment Service\nПроведение платежей]
        Notification[Notification Service\nОтправка писем/SMS]
    end

    TicketDB[(Ticket DB\nPostgreSQL)]

    %% Связи
    Web -->|HTTP / REST| Gateway
    Mobile -->|HTTP / REST| Gateway
    Gateway --> TicketService
    
    TicketService --> Catalog
    TicketService --> Payment
    TicketService --> Notification
    TicketService <-->|SQL Queries| TicketDB

    %% Стилизация
    classDef client fill:#e1f5fe,stroke:#01579b,stroke-width:2px;
    classDef gateway fill:#fff3e0,stroke:#e65100,stroke-width:2px;
    classDef core fill:#e8f5e9,stroke:#1b5e20,stroke-width:2px;
    classDef micro fill:#f3e5f5,stroke:#4a148c,stroke-width:2px;
    classDef db fill:#ffebee,stroke:#b71c1c,stroke-width:2px;

    class Web,Mobile client;
    class Gateway gateway;
    class TicketService core;
    class TicketDB db;
    class Catalog,Payment,Notification micro;
```

[mermaid:./terin-ivan.mermaid::497px:363px]

### 1\.2 Описание микросервиса

```
Опишите сервис и его функциональные границы
```

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

[openapi:./_index-3.yaml:true]

### 