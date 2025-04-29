---
order: 4
title: OutboxEvent.java
---

Этот класс представляет собой сущность для хранения событий, которые должны быть опубликованы в системе (например, для eventual consistency). Он используется для хранения информации о платёжных событиях, которые могут быть асинхронно отправлены в другие сервисы или системы.

#### \#Пакет:

```
package com.example.payment.gateway.domain;
```

#### \#Импорты:

```
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.Id;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

import java.time.Instant;
```

#### \#Аннотации:

-  `@Entity`: Означает, что класс является сущностью JPA и будет маппирован в таблицу базы данных.

-  `@Getter`, `@Setter`: Генерирует геттеры и сеттеры для всех полей класса.

-  `@NoArgsConstructor`: Генерирует конструктор без параметров.

---

### \#Поля:

1. **id**:

   ```
   @Id
   @GeneratedValue
   private Long id;
   ```

   Это уникальный идентификатор события. Он будет автоматически генерироваться при сохранении события в базе данных.

2. **paymentId**:

   ```
   private Long paymentId;
   ```

   Идентификатор платежа, к которому относится это событие.

3. **orderId**:

   ```
   private Long orderId;
   ```

   Идентификатор заказа, с которым связано событие.

4. **status**:

   ```
   private PaymentStatus status;
   ```

   Статус платежа. Это может быть, например, статус "успешно", "неудачно" и т.д.

5. **amount**:

   ```
   private Long amount;
   ```

   Сумма платежа.

6. **ts (timestamp)**:

   ```
   private Instant ts = Instant.now();
   ```

   Время создания события, которое по умолчанию устанавливается в текущее время.

7. **published**:

   ```
   private boolean published = false;
   ```

   Флаг, указывающий, было ли это событие опубликовано. По умолчанию оно равно `false`, что означает, что событие ещё не опубликовано.

---

### \#Конструктор:

```
public OutboxEvent(Long id, Long orderId, PaymentStatus status, Long amount) {
    this.id = id;
    this.orderId = orderId;
    this.status = status;
    this.amount = amount;
}
```

Этот конструктор позволяет создать объект `OutboxEvent` с переданными значениями для `id`, `orderId`, `status` и `amount`.

---

### \#Резюме:

Класс `OutboxEvent` представляет собой сущность, которая используется для хранения событий, которые должны быть асинхронно отправлены в другие сервисы. Он содержит информацию о платёжных событиях, таких как идентификаторы платежа и заказа, статус, сумма и время создания события. Поле `published` помогает отслеживать, было ли событие уже опубликовано.