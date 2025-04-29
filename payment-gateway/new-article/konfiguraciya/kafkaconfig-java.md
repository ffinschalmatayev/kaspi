---
order: 1
title: KafkaConfig.java
---

Этот класс конфигурирует Kafka-продюсера для отправки объектов типа `PaymentEvent` в Kafka-темы. Он определяет необходимые бины для продюсера Kafka, включая `KafkaTemplate` для взаимодействия с Kafka и `ProducerFactory` для создания продюсера.

#### \#Пакет:

```
package com.example.payment.gateway.config;
```

#### \#Импорты:

```
import com.example.payment.gateway.domain.PaymentEvent;
import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.common.serialization.StringSerializer;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.core.DefaultKafkaProducerFactory;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.kafka.core.ProducerFactory;
import org.springframework.kafka.support.serializer.JsonSerializer;
import org.springframework.scheduling.annotation.EnableScheduling;
import java.util.HashMap;
import java.util.Map;
```

#### \#Аннотации:

-  `@Configuration`: Означает, что этот класс является источником определения бинов для контекста приложения.

-  `@EnableScheduling`: Включает возможность выполнения запланированных задач в Spring.

---

### \#Определения бинов:

1. **Бин KafkaTemplate**:

   ```
   @Bean
   public KafkaTemplate<String, PaymentEvent> kafkaTemplate(ProducerFactory<String, PaymentEvent> pf) {
       return new KafkaTemplate<>(pf);
   }
   ```

   Этот бин создаёт `KafkaTemplate`, который используется для отправки объектов `PaymentEvent` в Kafka. Он использует переданный `ProducerFactory` для конфигурации шаблона.

2. **Конфигурации продюсера**:

   ```
   @Bean
   public Map<String, Object> producerConfigs() {
       Map<String, Object> props = new HashMap<>();
       props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "${spring.kafka.bootstrap-servers}");
       props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
       props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, JsonSerializer.class);
       return props;
   }
   ```

   Этот метод определяет конфигурационные параметры, необходимые для Kafka-продюсеров, включая адреса bootstrap-серверов и сериализаторы для ключа (`StringSerializer`) и значения (`JsonSerializer`) Kafka-сообщений.

3. **Бин ProducerFactory**:

   ```
   @Bean
   public ProducerFactory<String, PaymentEvent> producerFactory() {
       return new DefaultKafkaProducerFactory<>(producerConfigs());
   }
   ```

   Этот бин создаёт `ProducerFactory` для Kafka-продюсера, который будет использовать ранее определённые настройки конфигурации.

---

### \#Резюме:

Этот класс конфигурации настраивает необходимые бины для отправки сообщений типа `PaymentEvent` в Kafka. Он использует Spring Kafka для сериализации ключа как строку и значения как объект `PaymentEvent` в формате JSON. Настройки продюсера Kafka берутся из конфигурации приложения.