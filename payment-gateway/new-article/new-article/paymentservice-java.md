---
order: 1
title: PaymentService.java
---

Этот класс представляет собой сервис, который управляет процессами создания и захвата платежей. Он включает логику для проверки подозрительности платежей, а также для обработки событий, связанных с состоянием платежей. Все операции с базой данных выполняются в рамках транзакций для обеспечения консистентности данных.

#### \#Пакет:

```
package com.example.payment.gateway.service;
```

#### \#Импорты:

```
import com.example.payment.gateway.domain.OutboxEvent;
import com.example.payment.gateway.domain.Payment;
import com.example.payment.gateway.domain.PaymentStatus;
import com.example.payment.gateway.dto.CaptureRequest;
import com.example.payment.gateway.dto.PaymentRequest;
import com.example.payment.gateway.dto.PaymentResponse;
import com.example.payment.gateway.repo.OutboxEventRepository;
import com.example.payment.gateway.repo.PaymentRepository;
import io.micrometer.core.annotation.Timed;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
```

#### \#Аннотации:

-  `@Service`: Указывает, что этот класс является сервисом Spring, который будет автоматически зарегистрирован в контексте приложения и доступен для внедрения.

-  `@RequiredArgsConstructor`: Генерирует конструктор для всех полей, помеченных как `final`, для внедрения зависимостей.

-  `@Timed("payment.create.duration")`: Аннотация из библиотеки Micrometer, которая используется для измерения времени выполнения метода. В данном случае она применяется к методу `create`, чтобы отслеживать длительность операции создания платежа.

---

### \#Поля:

1. **repo**:

   ```
   private final PaymentRepository repo;
   ```

   Репозиторий для работы с сущностью `Payment`, используется для сохранения и извлечения информации о платежах.

2. **outbox**:

   ```
   private final OutboxEventRepository outbox;
   ```

   Репозиторий для работы с сущностью `OutboxEvent`, используется для сохранения событий, которые нужно опубликовать (например, в Kafka).

3. **fraud**:

   ```
   private final PaymentChecker fraud;
   ```

   Сервис для проверки подозрительных платежей. Он используется для определения, является ли платеж мошенническим, основываясь на его сумме.

---

### \#Методы:

1. **create(PaymentRequest req)**:

   ```
   @Timed("payment.create.duration")
   @Transactional
   public PaymentResponse create(PaymentRequest req) {
     Payment p = new Payment();
     p.setOrderId(req.orderId());
     p.setAmount(req.amount());
     p.setStatus(fraud.isSuspicious(req.amount()) ? PaymentStatus.FAILED : PaymentStatus.AUTHORIZED);
     repo.save(p);
     OutboxEvent outboxEvent = new OutboxEvent(p.getId(), p.getOrderId(), p.getStatus(), p.getAmount());
     outbox.save(outboxEvent);
     return new PaymentResponse(p.getId(), p.getStatus());
   }
   ```

   Этот метод создаёт новый платёж на основе данных из объекта `PaymentRequest`. Платёж может быть помечен как `FAILED`, если сумма превышает порог, определённый в `PaymentChecker`. После этого платёж сохраняется в базе данных, и создаётся событие, которое сохраняется в таблице `OutboxEvent`.

2. **capture(Long id, CaptureRequest req)**:

   ```
   @Transactional
   public PaymentResponse capture(Long id, CaptureRequest req) {
     Payment p = repo.findById(id)
             .orElseThrow();
     if (p.getStatus() != PaymentStatus.AUTHORIZED)
       throw new IllegalStateException();
     if (req.amount() < p.getAmount()) {
       p.setCapturedAmount(req.amount());
       p.setStatus(PaymentStatus.PARTIALLY_CAPTURED);
     } else {
       p.setCapturedAmount(p.getAmount());
       p.setStatus(PaymentStatus.CAPTURED);
     }
     repo.save(p);
     OutboxEvent outboxEvent = new OutboxEvent(p.getId(), p.getOrderId(), p.getStatus(), p.getCapturedAmount());
     outbox.save(outboxEvent);
     return new PaymentResponse(p.getId(), p.getStatus());
   }
   ```

   Этот метод выполняет захват средств с платежа. Он проверяет, был ли платёж авторизован, и если сумма, указанная в запросе, меньше, чем полная сумма платежа, то платёж считается частично захваченным. В противном случае платёж помечается как полностью захваченный. Затем сохраняется обновлённый платёж и создаётся событие для публикации.

---

### \#Резюме:

`PaymentService` -- это сервис, который управляет созданием и захватом платежей. Он использует логику для проверки подозрительных транзакций, а также сохраняет события о платежах в таблице `OutboxEvent`, которые впоследствии могут быть отправлены в Kafka или другие системы. Вся работа с данными происходит в рамках транзакций, что обеспечивает консистентность данных.