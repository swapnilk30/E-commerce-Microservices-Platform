Below is a **clean, production-ready step-by-step guide** to create a **Spring Boot 3.4.3 microservices project** with:

* **API Gateway**
* **Service Discovery (Eureka)**
* **Product Service**
* **User Service**
* **Order Service**

This matches your **Java + Spring Boot + MySQL** background and is commonly used in **payment & e-commerce systems**.

---

# 🏗️ Architecture Overview

```
Client
   ↓
API-GATEWAY (Spring Cloud Gateway)
   ↓
----------------------------------
| PRODUCT-SERVICE | USER-SERVICE | ORDER-SERVICE |
----------------------------------
           ↓
     SERVICE-DISCOVERY (EUREKA)
```

---

## 🔧 Tech Stack

| Component     | Technology                |
| ------------- | ------------------------- |
| Java          | 17                        |
| Spring Boot   | 3.4.3                     |
| Spring Cloud  | 2024.0.x                  |
| Discovery     | Netflix Eureka            |
| Gateway       | Spring Cloud Gateway      |
| DB            | MySQL                     |
| Build         | Maven                     |
| Communication | REST (OpenFeign optional) |

---

# STEP 1️⃣ Create Parent Project (Recommended)

Create a **multi-module Maven project**.

### 1. Create root project

```
ecommerce-microservices
```

### 2. Parent `pom.xml`

```xml
<project>
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.ecommerce</groupId>
  <artifactId>ecommerce-microservices</artifactId>
  <version>1.0.0</version>
  <packaging>pom</packaging>

  <modules>
    <module>service-discovery</module>
    <module>api-gateway</module>
    <module>product-service</module>
    <module>user-service</module>
    <module>order-service</module>
  </modules>

  <properties>
    <java.version>17</java.version>
    <spring-cloud.version>2024.0.0</spring-cloud.version>
  </properties>

  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-dependencies</artifactId>
        <version>${spring-cloud.version}</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
    </dependencies>
  </dependencyManagement>

</project>
```

---

# STEP 2️⃣ Service Discovery (Eureka Server)

### Module name

```
service-discovery
```

### Dependencies

* Spring Web
* Eureka Server

```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```

### Main Class

```java
@SpringBootApplication
@EnableEurekaServer
public class ServiceDiscoveryApplication {
    public static void main(String[] args) {
        SpringApplication.run(ServiceDiscoveryApplication.class, args);
    }
}
```

### `application.yml`

```yaml
server:
  port: 8761

spring:
  application:
    name: service-discovery

eureka:
  client:
    register-with-eureka: false
    fetch-registry: false
```

📍 URL: `http://localhost:8761`

---

# STEP 3️⃣ API Gateway

### Module name

```
api-gateway
```

### Dependencies

* Spring Cloud Gateway
* Eureka Discovery Client

```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>

<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

### `application.yml`

```yaml
server:
  port: 8080

spring:
  application:
    name: api-gateway

  cloud:
    gateway:
      routes:
        - id: product-service
          uri: lb://PRODUCT-SERVICE
          predicates:
            - Path=/products/**

        - id: user-service
          uri: lb://USER-SERVICE
          predicates:
            - Path=/users/**

        - id: order-service
          uri: lb://ORDER-SERVICE
          predicates:
            - Path=/orders/**

eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
```

---

# STEP 4️⃣ Product Service

### Module name

```
product-service
```

### Dependencies

* Spring Web
* Spring Data JPA
* MySQL Driver
* Eureka Client

### `application.yml`

```yaml
server:
  port: 8081

spring:
  application:
    name: PRODUCT-SERVICE

  datasource:
    url: jdbc:mysql://localhost:3306/productdb
    username: root
    password: root

  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true

eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
```

### Sample Controller

```java
@RestController
@RequestMapping("/products")
public class ProductController {

    @GetMapping
    public String getProducts() {
        return "Product Service Working";
    }
}
```

---

# STEP 5️⃣ User Service

### Module name

```
user-service
```

### `application.yml`

```yaml
server:
  port: 8082

spring:
  application:
    name: USER-SERVICE

  datasource:
    url: jdbc:mysql://localhost:3306/userdb
    username: root
    password: root

eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
```

### Controller

```java
@RestController
@RequestMapping("/users")
public class UserController {

    @GetMapping
    public String getUsers() {
        return "User Service Working";
    }
}
```

---

# STEP 6️⃣ Order Service

### Module name

```
order-service
```

### `application.yml`

```yaml
server:
  port: 8083

spring:
  application:
    name: ORDER-SERVICE

  datasource:
    url: jdbc:mysql://localhost:3306/orderdb
    username: root
    password: root

eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
```

### Controller

```java
@RestController
@RequestMapping("/orders")
public class OrderController {

    @GetMapping
    public String getOrders() {
        return "Order Service Working";
    }
}
```

---

# STEP 7️⃣ Run Order (IMPORTANT)

1️⃣ Start **Service Discovery**
2️⃣ Start **Product, User, Order Services**
3️⃣ Start **API Gateway**

---

# STEP 8️⃣ Test via Gateway

| URL                              | Result          |
| -------------------------------- | --------------- |
| `http://localhost:8080/products` | Product Service |
| `http://localhost:8080/users`    | User Service    |
| `http://localhost:8080/orders`   | Order Service   |

---

# STEP 9️⃣ Next Enhancements (Real Project)

Since you work on **payment gateways & transactions**, next steps:

✅ OpenFeign for service-to-service
✅ Centralized exception logging (DB)
✅ Spring Security + JWT
✅ Resilience4j (circuit breaker)
✅ Distributed tracing (Zipkin)
✅ Config Server

---

If you want, I can next provide:

* **Complete GitHub-ready project structure**
* **Feign + Order → Product call**
* **Centralized logging DB design**
* **JWT security at API Gateway**

Just tell me 👍
