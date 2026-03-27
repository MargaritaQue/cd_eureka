## checkdev_config

### Описание проекта

`checkdev_config` — это сервис, реализующий **Eureka Server** на базе **Spring Boot** и **Spring Cloud Netflix**.
Он используется для **регистрации и обнаружения микросервисов** в распределённой системе.
Другие микросервисы (клиенты Eureka) могут автоматически находить друг друга без необходимости указывать IP-адреса и порты вручную.


### Основные возможности

* Запуск **Eureka Server** для централизованного управления микросервисами.
* **Автоматическая регистрация** и **обнаружение** сервисов.
* Простая интеграция с другими проектами Spring Boot.
* Возможность настройки отказоустойчивого кластера Eureka (peer-to-peer replication).
* Поддержка внешних клиентов (через REST API или Spring Cloud DiscoveryClient).


### Технологии

| Компонент                              | Версия / Стек                 |
| -------------------------------------- | ----------------------------- |
| **Java**                               | 17+                           |
| **Spring Boot**                        | 2.7+ или 3.x                  |
| **Spring Cloud Netflix Eureka Server** | 3.x                           |
| **Maven**                              | В качестве системы сборки     |
| **Jenkins**                            | Для CI/CD (см. `Jenkinsfile`) |


### Структура проекта

```
checkdev_config/
├── pom.xml
├── Jenkinsfile
├── checkstyle.xml
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── ru/checkdev/eureka/
│   │   │       └── EurekaApplication.java
│   │   └── resources/
│   │       └── application.properties
│   └── test/
│       └── java/
│           └── ru/
└── .gitignore
```


### Конфигурация приложения

Файл `src/main/resources/application.properties`:

```properties
spring.application.name=eureka
server.port=9009

eureka.client.fetch-registry=false
eureka.client.register-with-eureka=false
eureka.client.service-url.defaultZone=http://localhost:9009/eureka
```


### Как запустить проект локально

#### 1. Сборка проекта

```bash
mvn clean package
```

#### 2. Запуск приложения

```bash
java -jar target/cd_eureka-0.0.1-SNAPSHOT.jar
```

или через Maven:

```bash
mvn spring-boot:run
```

#### 3. Доступ к Eureka Dashboard

После запуска сервер будет доступен по адресу:

[http://localhost:9009](http://localhost:9009)


### Подключение клиента к Eureka

Чтобы зарегистрировать другой микросервис в этом сервере, в его `application.properties` нужно добавить:

```properties
eureka.client.service-url.defaultZone=http://localhost:9009/eureka
eureka.client.register-with-eureka=true
eureka.client.fetch-registry=true
spring.application.name=your-service-name
```


### Jenkins

Файл `Jenkinsfile` содержит пайплайн для CI/CD, который может включать этапы:

* сборки (`mvn clean package`)
* тестирования (`mvn test`)
* деплоя Docker-образа (если настроен)


### Развёртывание в Docker (опционально)

Можно добавить следующий `Dockerfile`:

```dockerfile
FROM openjdk:17-jdk-slim
WORKDIR /app
COPY target/cd_eureka-0.0.1-SNAPSHOT.jar app.jar
EXPOSE 9009
ENTRYPOINT ["java", "-jar", "app.jar"]
```

Сборка образа:

```bash
docker build -t cd-eureka .
```

Запуск контейнера:

```bash
docker run -p 9009:9009 cd-eureka
```