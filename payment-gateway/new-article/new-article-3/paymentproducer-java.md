---
order: 1
title: PaymentProducer.java
---

Этот класс представляет собой компонент, который отвечает за отправку событий платежей в Kafka. Он использует `KafkaTemplate` для отправки сообщений в Kafka-тему, представляющую платёжные события.

#### \#Пакет:

```
package com.example.payment.gateway.messaging;
```

#### \#Импорты:

```
import com.example.payment.gateway.domain.PaymentEvent;
import lombok.RequiredArgsConstructor;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.stereotype.Component;
```

#### \#Аннотации:

-  `@Component`: Означает, что данный класс является Spring-компонентом, и его экземпляр будет автоматически внедрён в другие классы.

-  `@RequiredArgsConstructor`: Генерирует конструктор для всех полей, которые помечены как `final`. Это позволяет автоматически внедрить зависимость от `KafkaTemplate`.

---

### \#Поля:

1. **TOPIC**:

   ```
   private static final String TOPIC = "payment.events.v1";
   ```

   Это константа, которая указывает на Kafka-тему, в которую будут отправляться события платежей. В данном случае это тема `payment.events.v1`.

2. **kafkaTemplate**:

   ```
   private final KafkaTemplate<String, PaymentEvent> kafkaTemplate;
   ```

   Это зависимость, которая представляет собой шаблон для работы с Kafka. Он используется для отправки сообщений в Kafka.

---

### \#Метод:

1. **send(PaymentEvent event)**:

   ```
   public void send(PaymentEvent event) {
       kafkaTemplate.send(TOPIC, event.paymentId().toString(), event);
   }
   ```

   Этот метод отправляет событие `PaymentEvent` в Kafka. Событие передаётся в тему, указанную в `TOPIC`. Ключом сообщения будет `paymentId`, а значением -- сам объект `PaymentEvent`.

---

### \#Резюме:

`PaymentProducer` -- это компонент, который отправляет события платежей в Kafka. Он использует `KafkaTemplate` для публикации сообщений в указанную Kafka-тему. Этот класс полезен для передачи информации о платежах в другие системы через событийную модель, основанную на Kafka.