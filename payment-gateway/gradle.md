---
order: 1
title: Gradle
---

## \#1. gradle-wrapper.properties

Этот файл содержит настройки для Gradle Wrapper, который позволяет запускать Gradle без необходимости его предварительной установки. В нем указана версия Gradle, которая будет использована для проекта.

```java
#Mon Apr 28 13:07:45 ALMT 2025
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-8.12-bin.zip
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
```

#### \#Описание:

-  **distributionUrl** -- указывает на URL для загрузки нужной версии Gradle (в данном случае `gradle-8.12-bin.zip`).

---

## \#2. build.gradle

Это основной файл сборки для проекта Gradle. Он определяет плагины, зависимости и настройки для проекта.

```java
plugins {
    id 'org.springframework.boot' version '3.2.5'
    id 'io.spring.dependency-management' version '1.1.4'
    id 'java'
}

version = '0.0.1-SNAPSHOT'
sourceCompatibility = '21'

repositories { mavenCentral() }

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.kafka:spring-kafka'
    runtimeOnly  'com.h2database:h2'

    compileOnly  'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'

    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testImplementation 'org.springframework.kafka:spring-kafka-test'
}

tasks.named('test') { useJUnitPlatform() }
```

#### \#Описание:

-  Плагины:

   -  `org.springframework.boot`: Плагин для работы с Spring Boot.

   -  `io.spring.dependency-management`: Управление зависимостями в Spring проектах.

   -  `java`: Плагин для работы с Java.

-  Зависимости: включает стартеры Spring Boot для веба, JPA, Kafka и H2 для базы данных.

-  Настройка для тестирования с использованием JUnit и Spring Kafka.

---

## \#3. gradlew.bat

Этот скрипт для Windows используется для запуска Gradle через командную строку. Он настроен на автоматическое определение местоположения Java и запуск Gradle.

```java
@rem
@rem Copyright 2015 the original author or authors.
@rem
@rem Licensed under the Apache License, Version 2.0 (the "License");
@rem you may not use this file except in compliance with the License.
@rem You may obtain a copy of the License at
@rem
@rem      https://www.apache.org/licenses/LICENSE-2.0
@rem
@rem Unless required by applicable law or agreed to in writing, software
@rem distributed under the License is distributed on an "AS IS" BASIS,
@rem WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
@rem See the License for the specific language governing permissions and
@rem limitations under the License.
@rem

@if "%DEBUG%" == "" @echo off
@rem ##########################################################################
@rem
@rem  Gradle startup script for Windows
@rem
@rem ##########################################################################
...
```

#### \#Описание:

-  Скрипт проверяет наличие установленной Java, находит путь к `java.exe` и запускает Gradle с заданными параметрами.

---

## \#4. settings.gradle

Этот файл определяет имя корневого проекта. В данном случае, это `payment-gateway`.

```java
rootProject.name = 'payment-gateway'
```

#### \#Описание:

-  Устанавливает имя для корневого проекта, которое будет использоваться для всей сборки.

---

## \#5. gradlew

Этот скрипт для Linux/Mac использует оболочку bash для запуска Gradle. Он выполняет проверку на корректность установки Java и обрабатывает различные операционные системы.

```java
# Attempt to set APP_HOME
...
```

#### \#Описание:

-  Скрипт аналогичен `gradlew.bat`, но предназначен для операционных систем, основанных на UNIX (Linux, macOS). Он выполняет проверку Java, находит путь к `gradle-wrapper.jar` и запускает Gradle.