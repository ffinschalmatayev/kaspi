---
order: 3
title: OutboxPublisher.java
---

Этот класс представляет собой компонент, который публикует события из таблицы `OutboxEvent` в Kafka. Он использует отложенную задачу (с помощью аннотации `@Scheduled`), чтобы периодически проверять, есть ли неподтверждённые события, и публиковать их в Kafka.

#### \#Пакет:

```
package com.example.payment.gateway.service;
```

#### \#Импорты:

```
import com.example.payment.gateway.domain.OutboxEvent;
import com.example.payment.gateway.domain.PaymentEvent;
import com.example.payment.gateway.repo.OutboxEventRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

import java.util.List;
```

#### \#Аннотации:

-  `@Component`: Указывает, что класс является компонентом Spring, который будет автоматически зарегистрирован в контексте приложения и доступен для внедрения.

-  `@RequiredArgsConstructor`: Генерирует конструктор для всех полей, которые помечены как `final`, обеспечивая инъекцию зависимостей.

-  `@Scheduled(fixedDelay = 5000)`: Устанавливает периодичность выполнения метода `publishPending()`. В данном случае, метод будет выполняться каждые 5000 миллисекунд (5 секунд), что позволяет периодически публиковать события в Kafka.

---

### \#Поля:

1. **repo**:

   ```
   private final OutboxEventRepository repo;
   ```

   Репозиторий для работы с сущностью `OutboxEvent`. Он используется для поиска и сохранения неподтверждённых событий.

2. **kafka**:

   ```
   private final KafkaTemplate<String, PaymentEvent> kafka;
   ```

   Это зависимость для работы с Kafka. `KafkaTemplate` используется для отправки сообщений в Kafka.

---

### \#Метод:

1. **publishPending()**:

   ```
   @Scheduled(fixedDelay = 5000)
   public void publishPending() {
     List<OutboxEvent> evts = repo.findByPublishedFalse();
     evts.forEach(e -> {
       kafka.send("payment.events.v1", e.getPaymentId().toString(),
         new PaymentEvent(e.getPaymentId(), e.getOrderId(), e.getStatus(), e.getAmount(), e.getTs()));
       e.setPublished(true);
       repo.save(e);
     });
   }
   ```

   Этот метод выполняется каждые 5 секунд и проверяет, есть ли в базе данных события, которые ещё не были опубликованы (`published = false`). Для каждого неподтверждённого события:

   -  Создаётся объект `PaymentEvent`.

   -  Отправляется событие в Kafka в тему `payment.events.v1`.

   -  После успешной отправки событие помечается как опубликованное (`setPublished(true)`), и его состояние сохраняется в базе данных.

---

### \#Резюме:

`OutboxPublisher` -- это компонент, который регулярно проверяет таблицу `OutboxEvent` на наличие неподтверждённых событий и публикует их в Kafka. С помощью аннотации `@Scheduled` метод `publishPending()` выполняется каждые 5 секунд, что позволяет обеспечить регулярную и своевременную отправку событий из базы данных в очередь сообщений. Это важно для интеграции событийной модели в архитектуру приложения.