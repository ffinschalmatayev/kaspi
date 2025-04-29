---
order: 1
title: PaymentController.java
---

Этот класс контроллера обрабатывает запросы, связанные с платежами, и использует сервисы для выполнения различных операций, таких как создание платежа и его захват. Также реализована логика обеспечения идемпотентности для предотвращения повторной обработки одного и того же запроса.

#### \#Пакет:

```
package com.example.payment.gateway.controller;
```

#### \#Импорты:

```
import com.example.payment.gateway.dto.CaptureRequest;
import com.example.payment.gateway.dto.PaymentRequest;
import com.example.payment.gateway.dto.PaymentResponse;
import com.example.payment.gateway.service.IdempotencyService;
import com.example.payment.gateway.service.PaymentService;
import lombok.RequiredArgsConstructor;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
```

#### \#Аннотации:

-  `@RestController`: Означает, что класс является контроллером, и все методы возвращают данные в виде ответа (например, JSON).

-  `@RequiredArgsConstructor`: Автоматически генерирует конструктор для всех финальных полей класса.

-  `@RequestMapping("/payments")`: Устанавливает базовый путь для всех методов контроллера, который начинается с `/payments`.

---

### \#Методы контроллера:

1. **Создание платежа**:

   ```
   @PostMapping
   public ResponseEntity<PaymentResponse> create(@RequestHeader("Idempotency-Key") String key,
                                                 @RequestBody PaymentRequest req) {
       if (idempotencyService.isProcessed(key)) {
           return ResponseEntity.ok((PaymentResponse) idempotencyService.getResult(key));
       }
       PaymentResponse resp = paymentService.create(req);
       idempotencyService.register(key, resp);
       return ResponseEntity.ok(resp);
   }
   ```

   Этот метод обрабатывает POST-запросы на создание нового платежа. Он принимает заголовок `Idempotency-Key` для проверки идемпотентности и тело запроса, содержащее данные для нового платежа. Если запрос с таким ключом уже был обработан, возвращается сохранённый результат. Если запрос новый, он обрабатывается, а результат сохраняется в сервисе идемпотентности.

2. **Захват платежа**:

   ```
   @PostMapping("/{id}/capture")
   public ResponseEntity<PaymentResponse> capture(
           @RequestHeader("Idempotency-Key") String key,
           @PathVariable Long id,
           @RequestBody CaptureRequest req) {
       String lockKey = key + id;
       if (idempotencyService.isProcessed(lockKey)) {
           return ResponseEntity.ok((PaymentResponse) idempotencyService.getResult(lockKey));
       }
       PaymentResponse resp = paymentService.capture(id, req);
       idempotencyService.register(lockKey, resp);
       return ResponseEntity.ok(resp);
   }
   ```

   Этот метод обрабатывает POST-запросы на захват платежа. Он принимает заголовок `Idempotency-Key`, ID платежа, а также тело запроса с данными для захвата. Как и в предыдущем методе, используется механизм идемпотентности для проверки, был ли запрос уже обработан. Если запрос новый, он обрабатывается, и результат сохраняется.

---

### \#Резюме:

Класс `PaymentController` обрабатывает два основных действия с платежами:

1. Создание платежа.

2. Захват платежа.

Обе операции защищены от повторной обработки с помощью механизма идемпотентности, который предотвращает повторное выполнение одного и того же действия, если запрос с тем же ключом уже был обработан. Для этого используется сервис `IdempotencyService`, который хранит результаты обработки запросов по ключу и проверяет, были ли они ранее обработаны.