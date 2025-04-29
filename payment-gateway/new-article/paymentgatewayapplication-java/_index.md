---
order: 1
title: PaymentGatewayApplication.java
---

Этот класс является основной точкой входа для приложения Spring Boot. Он инициирует запуск приложения, используя Spring Boot и его механизмы автоконфигурации.

#### \#Пакет:

```java
package com.example.payment.gateway;
```

#### \#Импорты:

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
```

#### \#Аннотация:

-  `@SpringBootApplication`: Это ключевая аннотация для Spring Boot приложений. Она объединяет три другие аннотации:

   -  `@Configuration`: Указывает, что класс содержит конфигурацию для Spring.

   -  `@EnableAutoConfiguration`: Включает автоконфигурацию Spring Boot, что позволяет автоматически настраивать необходимые компоненты.

   -  `@ComponentScan`: Разрешает Spring искать компоненты, конфигурации и сервисы в текущем пакете и его подпакетах.

---

### \#Метод:

1. **main(String\[\] args)**:

   ```java
   public static void main(String[] args) {
       SpringApplication.run(PaymentGatewayApplication.class, args);
   }
   ```

   Это основной метод, который запускает приложение. Он вызывает `SpringApplication.run`, который инициирует контекст Spring и запускает приложение на встроенном сервере (например, Tomcat, если не настроено иначе). Этот метод является стандартным способом запуска Spring Boot приложений.



---

### \#Резюме:

`PaymentGatewayApplication` -- это точка входа в приложение, которое использует Spring Boot для упрощённой конфигурации и запуска. Аннотация `@SpringBootApplication` и метод `main()` обеспечивают запуск приложения и создание контекста Spring.