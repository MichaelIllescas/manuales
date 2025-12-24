# Manual de Arquitectura de Microservicios
## La Guía Definitiva para Principiantes

> **Aprende a diseñar sistemas distribuidos escalables y resilientes**
>
> Este manual explica la arquitectura de microservicios de forma simple, práctica y con ejemplos reales, sin asumir conocimientos previos.

---

## 📚 Índice

1. [¿Qué son los Microservicios?](#1-qué-son-los-microservicios)
2. [El Problema que Resuelven](#2-el-problema-que-resuelven)
3. [Características de los Microservicios](#3-características-de-los-microservicios)
4. [Arquitectura de Microservicios](#4-arquitectura-de-microservicios)
5. [Patrones de Comunicación](#5-patrones-de-comunicación)
6. [Patrones de Datos](#6-patrones-de-datos)
7. [Service Discovery y API Gateway](#7-service-discovery-y-api-gateway)
8. [Resiliencia y Tolerancia a Fallos](#8-resiliencia-y-tolerancia-a-fallos)
9. [Despliegue y DevOps](#9-despliegue-y-devops)
10. [Ejemplo Práctico Completo](#10-ejemplo-práctico-completo)
11. [Microservicios vs Monolitos](#11-microservicios-vs-monolitos)
12. [Cuándo Usar (y Cuándo No)](#12-cuándo-usar-y-cuándo-no)
13. [Migración de Monolito a Microservicios](#13-migración-de-monolito-a-microservicios)
14. [Errores Comunes](#14-errores-comunes)
15. [Observabilidad y Monitoreo](#15-observabilidad-y-monitoreo)
16. [Preguntas Frecuentes](#16-preguntas-frecuentes)

---

## 1. ¿Qué son los Microservicios?

### Definición Simple

**Microservicios** es un estilo arquitectónico donde una aplicación se construye como un **conjunto de servicios pequeños, independientes y autónomos** que se comunican entre sí mediante APIs bien definidas.

```
┌────────────────────────────────────────────┐
│         MONOLITO                           │
│  ┌──────────────────────────────────────┐  │
│  │                                      │  │
│  │   TODO en una sola aplicación        │  │
│  │   - Users                            │  │
│  │   - Products                         │  │
│  │   - Orders                           │  │
│  │   - Payments                         │  │
│  │   - Shipping                         │  │
│  │                                      │  │
│  └──────────────────────────────────────┘  │
└────────────────────────────────────────────┘

        ↓ DESCOMPOSICIÓN ↓

┌────────────────────────────────────────────┐
│         MICROSERVICIOS                     │
├────────────────────────────────────────────┤
│  ┌──────┐  ┌──────┐  ┌──────┐            │
│  │Users │  │Product│ │Orders│            │
│  │Service│ │Service│ │Service│           │
│  └──────┘  └──────┘  └──────┘            │
│                                           │
│  ┌──────┐  ┌──────┐                      │
│  │Payment│ │Shipping│                    │
│  │Service│ │Service │                    │
│  └──────┘  └──────┘                      │
│                                           │
│  Cada servicio:                           │
│  - Independiente                          │
│  - Desplegable por separado               │
│  - Con su propia base de datos            │
│  - Escalable independientemente           │
└────────────────────────────────────────────┘
```

### Analogía Simple

Imagina un restaurante:

```
❌ MONOLITO = Restaurante pequeño con un solo chef
- Un chef hace TODO (cocina, limpia, atiende)
- Si el chef se enferma, el restaurante cierra
- Difícil escalar (el chef tiene límite)
- Cambiar el menú afecta toda la operación

✅ MICROSERVICIOS = Restaurante grande con especialistas
- Chef de pastas (servicio independiente)
- Chef de carnes (servicio independiente)
- Chef de postres (servicio independiente)
- Bartender (servicio independiente)

Ventajas:
- Si un chef falla, los demás siguen funcionando
- Cada área puede escalar independientemente
- Especialización (cada chef experto en lo suyo)
- Cambios en postres no afectan las pastas
```

### Definición Formal

> **Microservicios es un enfoque arquitectónico que:**
> 1. **Divide** la aplicación en servicios pequeños
> 2. **Cada servicio** implementa una capacidad de negocio específica
> 3. **Servicios autónomos** que pueden desplegarse independientemente
> 4. **Comunicación** mediante APIs ligeras (HTTP/REST, mensajería)
> 5. **Descentralización** de datos y decisiones
> 6. **Escalado independiente** de cada servicio

---

## 2. El Problema que Resuelven

### Monolito Tradicional (Problemas)

```java
// ❌ TODO EN UNA APLICACIÓN MONOLÍTICA
@SpringBootApplication
public class EcommerceApplication {
    // Módulo Users
    @Autowired UserService userService;
    @Autowired UserRepository userRepository;

    // Módulo Products
    @Autowired ProductService productService;
    @Autowired ProductRepository productRepository;

    // Módulo Orders
    @Autowired OrderService orderService;
    @Autowired OrderRepository orderRepository;

    // Módulo Payments
    @Autowired PaymentService paymentService;
    @Autowired PaymentRepository paymentRepository;

    // Módulo Shipping
    @Autowired ShippingService shippingService;
    @Autowired ShippingRepository shippingRepository;

    // TODO compartiendo:
    // - Misma base de datos
    // - Mismo servidor
    // - Mismo proceso
    // - Mismo despliegue
}
```

**Problemas del Monolito:**

```
❌ ESCALADO:
- Para escalar Orders, debo escalar TODO
- Desperdicio de recursos (escalas módulos que no necesitan)

❌ DESPLIEGUE:
- Un cambio pequeño en Payments = redesplegar TODO
- Despliegues de alto riesgo (todo o nada)
- Downtime completo durante deploys

❌ TECNOLOGÍA:
- Todos los módulos en el mismo lenguaje/framework
- Difícil adoptar nuevas tecnologías
- Acoplamiento tecnológico

❌ EQUIPOS:
- Todos los equipos trabajan en la misma codebase
- Conflictos de merge constantes
- Difícil coordinación

❌ FALLAS:
- Un bug en Shipping tumba toda la aplicación
- Memory leak en Orders afecta Users
- Fallo en cascada

❌ MANTENIMIENTO:
- Codebase enorme (millones de líneas)
- Tiempo de build largo (30+ minutos)
- Difícil entender el sistema completo
```

### Con Microservicios

```
┌─────────────────────────────────────────────┐
│         ARQUITECTURA DE MICROSERVICIOS      │
├─────────────────────────────────────────────┤
│                                             │
│  ┌────────────────┐    ┌────────────────┐  │
│  │ User Service   │    │ Product Service│  │
│  │ - Java/Spring  │    │ - Node.js      │  │
│  │ - PostgreSQL   │    │ - MongoDB      │  │
│  │ - Team A       │    │ - Team B       │  │
│  └────────────────┘    └────────────────┘  │
│                                             │
│  ┌────────────────┐    ┌────────────────┐  │
│  │ Order Service  │    │ Payment Service│  │
│  │ - Go           │    │ - Python       │  │
│  │ - MySQL        │    │ - PostgreSQL   │  │
│  │ - Team C       │    │ - Team D       │  │
│  └────────────────┘    └────────────────┘  │
│                                             │
│  ┌────────────────┐                         │
│  │ Shipping Svc   │                         │
│  │ - Java/Spring  │                         │
│  │ - PostgreSQL   │                         │
│  │ - Team E       │                         │
│  └────────────────┘                         │
│                                             │
└─────────────────────────────────────────────┘

Cada servicio:
✓ Tecnología independiente
✓ Base de datos propia
✓ Equipo independiente
✓ Despliegue independiente
✓ Escalado independiente
```

**Beneficios:**

```
✓ ESCALADO:
- Escalar solo Order Service (el que tiene carga)
- Eficiencia de recursos

✓ DESPLIEGUE:
- Cambio en Payments = solo redesplegar Payment Service
- Deploys de bajo riesgo
- Zero downtime (rolling updates)

✓ TECNOLOGÍA:
- Cada servicio puede usar la tecnología óptima
- Users en Java, Products en Node.js, Orders en Go
- Libertad tecnológica

✓ EQUIPOS:
- Cada equipo dueño de un servicio
- Autonomía
- Menos conflictos

✓ RESILIENCIA:
- Fallo en Shipping no afecta Users
- Aislamiento de fallos
- Degradación elegante

✓ MANTENIBILIDAD:
- Codebase pequeña por servicio
- Fácil entender un servicio
- Build rápido (minutos)
```

---

## 3. Características de los Microservicios

### 1. Componentización mediante Servicios

```
Componente = Unidad de software reemplazable independientemente

MONOLITO: Componentes = Librerías (JAR, DLL)
- Acopladas en proceso
- Reemplazo = rebuild + redeploy TODO

MICROSERVICIOS: Componentes = Servicios
- Desacoplados en procesos separados
- Reemplazo = redeploy solo ese servicio
```

### 2. Organizado alrededor de Capacidades de Negocio

```
❌ MONOLITO: Organizado por capas técnicas
src/
├── controllers/
│   ├── UserController
│   ├── ProductController
│   └── OrderController
├── services/
│   ├── UserService
│   ├── ProductService
│   └── OrderService
└── repositories/
    ├── UserRepository
    ├── ProductRepository
    └── OrderRepository

Problema: Cambio en "Orders" toca 3 capas

✅ MICROSERVICIOS: Organizado por dominio
services/
├── user-service/          (Todo sobre users)
│   ├── controllers/
│   ├── services/
│   └── repositories/
│
├── product-service/       (Todo sobre products)
│   ├── controllers/
│   ├── services/
│   └── repositories/
│
└── order-service/         (Todo sobre orders)
    ├── controllers/
    ├── services/
    └── repositories/

Beneficio: Cambio en "Orders" solo afecta order-service
```

### 3. Productos, no Proyectos

```
PROYECTOS (tradicional):
- Equipo construye, entrega y se va
- "Throw over the wall" a operaciones
- Desconexión entre dev y ops

PRODUCTOS (microservicios):
- Equipo dueño del servicio de punta a punta
- "You build it, you run it" (Amazon)
- DevOps culture
- Responsabilidad completa: dev + deploy + monitoreo + soporte
```

### 4. Smart Endpoints, Dumb Pipes

```
MONOLITO:
- Comunicación in-process (métodos)
- No necesita red

MICROSERVICIOS:
- Comunicación vía red
- Protocolos simples (HTTP/REST, mensajes)
- Lógica en los servicios, no en el middleware

❌ MAL: ESB (Enterprise Service Bus) con lógica
- Transformaciones en el bus
- Orquestación centralizada
- Vendor lock-in

✅ BIEN: HTTP/REST o mensajería simple
- RESTful HTTP
- AMQP (RabbitMQ)
- Kafka
- gRPC
```

### 5. Descentralización

#### a) Gobernanza Descentralizada

```
MONOLITO:
- Un lenguaje, un framework
- Estándares centralizados
- Comité de arquitectura decide todo

MICROSERVICIOS:
- Cada equipo elige tecnología
- Libertad con responsabilidad
- Estándares donde tiene sentido (observabilidad, seguridad)

Ejemplo:
- User Service: Java + Spring Boot
- Product Service: Node.js + Express
- Order Service: Go
- Payment Service: Python + FastAPI
```

#### b) Gestión de Datos Descentralizada

```
MONOLITO:
┌─────────────────────────────────┐
│      Aplicación Monolítica      │
└────────────┬────────────────────┘
             │
             ↓
    ┌────────────────┐
    │  Una BD única  │
    │   PostgreSQL   │
    └────────────────┘

MICROSERVICIOS:
┌──────────┐  ┌──────────┐  ┌──────────┐
│  User    │  │ Product  │  │  Order   │
│ Service  │  │ Service  │  │ Service  │
└────┬─────┘  └────┬─────┘  └────┬─────┘
     │             │             │
     ↓             ↓             ↓
┌─────────┐  ┌─────────┐  ┌─────────┐
│Users DB │  │Products │  │Orders   │
│Postgres │  │ MongoDB │  │  MySQL  │
└─────────┘  └─────────┘  └─────────┘

Cada servicio con su propia base de datos
```

### 6. Infraestructura Automatizada

```
MICROSERVICIOS requieren:
✓ CI/CD (Integración y Despliegue Continuo)
✓ Automated Testing
✓ Infrastructure as Code (Terraform, CloudFormation)
✓ Containerización (Docker)
✓ Orquestación (Kubernetes)
✓ Monitoreo automatizado

Sin automatización, gestionar 10+ servicios es imposible
```

### 7. Diseñado para Fallos

```
MONOLITO:
- Asume que todo funciona
- Fallo = todo cae

MICROSERVICIOS:
- Asume que los servicios fallarán
- Diseñado para degradación elegante
- Circuit breakers
- Timeouts
- Retries
- Fallbacks

Ejemplo:
Si Payment Service falla:
✓ Order Service usa fallback
✓ "Pago pendiente, te notificaremos"
✓ Resto del sistema funciona
```

### 8. Diseño Evolutivo

```
MONOLITO:
- Difícil cambiar arquitectura
- Big bang rewrites
- Alto riesgo

MICROSERVICIOS:
- Reemplazar servicios individuales
- Evolución incremental
- Strangler Fig pattern
- Bajo riesgo

Ejemplo:
- V1: Order Service en Java
- V2: Order Service en Go (mejor performance)
- Migración gradual servicio por servicio
```

---

## 4. Arquitectura de Microservicios

### Componentes Principales

```
┌─────────────────────────────────────────────────────┐
│            ARQUITECTURA TÍPICA                      │
├─────────────────────────────────────────────────────┤
│                                                     │
│  ┌─────────────────────────────────────────────┐   │
│  │         API GATEWAY                         │   │
│  │  - Routing                                  │   │
│  │  - Authentication                           │   │
│  │  - Rate Limiting                            │   │
│  └──────────────┬──────────────────────────────┘   │
│                 │                                   │
│     ┌───────────┼───────────┐                      │
│     │           │           │                      │
│     ↓           ↓           ↓                      │
│  ┌─────┐    ┌─────┐    ┌─────┐                    │
│  │User │    │Product   │Order│                    │
│  │Svc  │    │ Svc │    │ Svc │                    │
│  └──┬──┘    └──┬──┘    └──┬──┘                    │
│     │          │          │                        │
│     ↓          ↓          ↓                        │
│  ┌───┐      ┌───┐      ┌───┐                      │
│  │DB │      │DB │      │DB │                      │
│  └───┘      └───┘      └───┘                      │
│                                                     │
│  ┌─────────────────────────────────────────────┐   │
│  │      SERVICE REGISTRY (Discovery)           │   │
│  │      (Consul, Eureka, etcd)                 │   │
│  └─────────────────────────────────────────────┘   │
│                                                     │
│  ┌─────────────────────────────────────────────┐   │
│  │      MESSAGE BROKER (Async)                 │   │
│  │      (Kafka, RabbitMQ, AWS SQS)             │   │
│  └─────────────────────────────────────────────┘   │
│                                                     │
└─────────────────────────────────────────────────────┘
```

### Estructura de un Microservicio

```
order-service/
├── src/
│   ├── main/
│   │   ├── java/com/example/order/
│   │   │   ├── controller/
│   │   │   │   └── OrderController.java
│   │   │   │
│   │   │   ├── service/
│   │   │   │   └── OrderService.java
│   │   │   │
│   │   │   ├── repository/
│   │   │   │   └── OrderRepository.java
│   │   │   │
│   │   │   ├── model/
│   │   │   │   ├── Order.java
│   │   │   │   └── OrderItem.java
│   │   │   │
│   │   │   ├── dto/
│   │   │   │   ├── OrderRequest.java
│   │   │   │   └── OrderResponse.java
│   │   │   │
│   │   │   ├── client/          # Clientes a otros servicios
│   │   │   │   ├── ProductServiceClient.java
│   │   │   │   └── PaymentServiceClient.java
│   │   │   │
│   │   │   ├── messaging/       # Event publishers/consumers
│   │   │   │   ├── OrderEventPublisher.java
│   │   │   │   └── PaymentEventConsumer.java
│   │   │   │
│   │   │   └── config/
│   │   │       ├── DatabaseConfig.java
│   │   │       └── SecurityConfig.java
│   │   │
│   │   └── resources/
│   │       ├── application.yml
│   │       └── schema.sql
│   │
│   └── test/
│       └── java/com/example/order/
│           ├── controller/
│           └── service/
│
├── Dockerfile                    # Containerización
├── docker-compose.yml
├── k8s/                         # Kubernetes manifests
│   ├── deployment.yaml
│   ├── service.yaml
│   └── ingress.yaml
│
├── pom.xml                      # Dependencies
└── README.md
```

---

## 5. Patrones de Comunicación

### 5.1 Comunicación Síncrona (Request-Response)

#### REST/HTTP

```java
// ✅ Order Service llama a Product Service (REST)
@Service
public class OrderService {

    @Autowired
    private RestTemplate restTemplate;

    private static final String PRODUCT_SERVICE_URL = "http://product-service";

    public Order createOrder(CreateOrderRequest request) {
        // 1. Llamada síncrona a Product Service
        Product product = restTemplate.getForObject(
            PRODUCT_SERVICE_URL + "/products/" + request.getProductId(),
            Product.class
        );

        if (product == null) {
            throw new ProductNotFoundException();
        }

        // 2. Validar stock
        if (product.getStock() < request.getQuantity()) {
            throw new InsufficientStockException();
        }

        // 3. Crear orden
        Order order = new Order();
        order.setProductId(product.getId());
        order.setQuantity(request.getQuantity());
        order.setTotalPrice(product.getPrice() * request.getQuantity());

        return orderRepository.save(order);
    }
}
```

**Ventajas:**
```
✓ Simple de entender
✓ Respuesta inmediata
✓ Fácil debugging
```

**Desventajas:**
```
❌ Acoplamiento temporal (ambos servicios deben estar disponibles)
❌ Latencia (espera respuesta)
❌ Cascada de fallos
```

#### gRPC

```protobuf
// product.proto
syntax = "proto3";

service ProductService {
  rpc GetProduct(GetProductRequest) returns (Product);
  rpc CheckStock(CheckStockRequest) returns (StockResponse);
}

message GetProductRequest {
  string product_id = 1;
}

message Product {
  string id = 1;
  string name = 2;
  double price = 3;
  int32 stock = 4;
}
```

```java
// Cliente gRPC
@Service
public class OrderService {

    @Autowired
    private ProductServiceGrpc.ProductServiceBlockingStub productClient;

    public Order createOrder(CreateOrderRequest request) {
        // Llamada gRPC (más rápida que REST)
        GetProductRequest grpcRequest = GetProductRequest.newBuilder()
            .setProductId(request.getProductId())
            .build();

        Product product = productClient.getProduct(grpcRequest);

        // Continuar con lógica...
    }
}
```

**Ventajas:**
```
✓ Más rápido que REST (Protocol Buffers binario)
✓ Streaming bidireccional
✓ Contrato fuertemente tipado
```

---

### 5.2 Comunicación Asíncrona (Event-Driven)

#### Message Queue (RabbitMQ, AWS SQS)

```java
// ✅ Order Service publica evento
@Service
public class OrderService {

    @Autowired
    private RabbitTemplate rabbitTemplate;

    public Order createOrder(CreateOrderRequest request) {
        // 1. Crear orden
        Order order = new Order();
        order.setProductId(request.getProductId());
        order.setQuantity(request.getQuantity());
        orderRepository.save(order);

        // 2. Publicar evento (asíncrono)
        OrderCreatedEvent event = new OrderCreatedEvent(
            order.getId(),
            order.getProductId(),
            order.getQuantity()
        );

        rabbitTemplate.convertAndSend("order.exchange", "order.created", event);

        return order;
    }
}

// Payment Service escucha evento
@Service
public class PaymentEventListener {

    @RabbitListener(queues = "payment.order.created")
    public void handleOrderCreated(OrderCreatedEvent event) {
        // Procesar pago de forma asíncrona
        System.out.println("Processing payment for order: " + event.getOrderId());

        // Lógica de pago...
    }
}

// Inventory Service también escucha
@Service
public class InventoryEventListener {

    @RabbitListener(queues = "inventory.order.created")
    public void handleOrderCreated(OrderCreatedEvent event) {
        // Reservar inventario de forma asíncrona
        System.out.println("Reserving inventory for order: " + event.getOrderId());

        // Lógica de inventario...
    }
}
```

**Ventajas:**
```
✓ Desacoplamiento temporal (servicios no necesitan estar disponibles al mismo tiempo)
✓ Escalabilidad (procesamiento asíncrono)
✓ Resiliencia (mensajes persisten en cola)
✓ Multiple subscribers (fanout)
```

**Desventajas:**
```
❌ Complejidad (eventual consistency)
❌ Debugging más difícil (flujo asíncrono)
❌ Necesita infraestructura (message broker)
```

#### Event Streaming (Kafka)

```java
// Producer (Order Service)
@Service
public class OrderEventProducer {

    @Autowired
    private KafkaTemplate<String, OrderEvent> kafkaTemplate;

    public void publishOrderCreated(Order order) {
        OrderCreatedEvent event = new OrderCreatedEvent(order);

        kafkaTemplate.send("order-events", event);
    }
}

// Consumer (Payment Service)
@Service
public class PaymentEventConsumer {

    @KafkaListener(topics = "order-events", groupId = "payment-service")
    public void consume(OrderCreatedEvent event) {
        // Procesar evento
        processPayment(event);
    }
}

// Consumer (Email Service)
@Service
public class EmailEventConsumer {

    @KafkaListener(topics = "order-events", groupId = "email-service")
    public void consume(OrderCreatedEvent event) {
        // Enviar confirmación por email
        sendOrderConfirmation(event);
    }
}
```

**Ventajas sobre Message Queue:**
```
✓ Event log (histórico completo de eventos)
✓ Replay (reprocessar eventos)
✓ Event Sourcing
✓ Alto throughput
```

---

### 5.3 Patrón: API Composition

```java
// ✅ API Gateway compone datos de múltiples servicios
@RestController
@RequestMapping("/api/orders")
public class OrderCompositionController {

    @Autowired
    private OrderServiceClient orderClient;

    @Autowired
    private ProductServiceClient productClient;

    @Autowired
    private UserServiceClient userClient;

    @GetMapping("/{orderId}/details")
    public OrderDetailsResponse getOrderDetails(@PathVariable String orderId) {
        // 1. Llamar a Order Service
        Order order = orderClient.getOrder(orderId);

        // 2. Llamar a Product Service (para detalles del producto)
        Product product = productClient.getProduct(order.getProductId());

        // 3. Llamar a User Service (para datos del usuario)
        User user = userClient.getUser(order.getUserId());

        // 4. Componer respuesta
        return OrderDetailsResponse.builder()
            .order(order)
            .product(product)
            .user(user)
            .build();
    }
}
```

**Problema:**
```
❌ Múltiples llamadas síncronas (latencia)
❌ Si un servicio falla, toda la request falla
```

**Solución: Llamadas en paralelo**

```java
@GetMapping("/{orderId}/details")
public OrderDetailsResponse getOrderDetails(@PathVariable String orderId) {
    // Ejecutar en paralelo usando CompletableFuture
    CompletableFuture<Order> orderFuture = CompletableFuture.supplyAsync(
        () -> orderClient.getOrder(orderId)
    );

    CompletableFuture<Product> productFuture = orderFuture.thenComposeAsync(
        order -> CompletableFuture.supplyAsync(
            () -> productClient.getProduct(order.getProductId())
        )
    );

    CompletableFuture<User> userFuture = orderFuture.thenComposeAsync(
        order -> CompletableFuture.supplyAsync(
            () -> userClient.getUser(order.getUserId())
        )
    );

    // Esperar todos
    CompletableFuture.allOf(orderFuture, productFuture, userFuture).join();

    return OrderDetailsResponse.builder()
        .order(orderFuture.join())
        .product(productFuture.join())
        .user(userFuture.join())
        .build();
}
```

---

## 6. Patrones de Datos

### 6.1 Database per Service

```
REGLA DE ORO:
Cada microservicio tiene su PROPIA base de datos
Otros servicios NO pueden acceder directamente

┌──────────────┐      ┌──────────────┐
│ Order Service│      │Product Service│
└──────┬───────┘      └──────┬────────┘
       │                     │
       │ SOLO Order          │ SOLO Product
       │ puede acceder       │ puede acceder
       ↓                     ↓
  ┌─────────┐           ┌─────────┐
  │Orders DB│           │Products │
  └─────────┘           │   DB    │
                        └─────────┘

✓ Cada servicio elige su tecnología de BD
✓ Cambios en esquema sin afectar otros servicios
✓ Escalado independiente
```

**Implementación:**

```java
// Order Service - PostgreSQL
@Entity
@Table(name = "orders")
public class Order {
    @Id
    private String id;
    private String productId;  // ← Referencia por ID (no JOIN)
    private int quantity;
    private BigDecimal totalPrice;
}

// Product Service - MongoDB
@Document(collection = "products")
public class Product {
    @Id
    private String id;
    private String name;
    private BigDecimal price;
    private int stock;
}

// ❌ NO HACER: JOIN entre Orders y Products
// Están en bases de datos diferentes!

// ✅ HACER: Obtener datos llamando al servicio
Order order = orderRepository.findById(orderId);
Product product = productServiceClient.getProduct(order.getProductId());
```

**Desventajas:**
```
❌ No hay JOINs entre servicios
❌ Transacciones distribuidas complejas
❌ Duplicación de datos (denormalización)
❌ Eventual consistency
```

---

### 6.2 Saga Pattern (Transacciones Distribuidas)

**Problema:**
```
¿Cómo hacer una transacción que afecta múltiples servicios?

Ejemplo: Crear una orden
1. Order Service: Crear orden
2. Payment Service: Procesar pago
3. Inventory Service: Reservar stock
4. Shipping Service: Programar envío

Si el paso 3 falla, ¿cómo revertir los pasos 1 y 2?
```

**Solución: Saga Pattern**

#### a) Choreography-based Saga (Eventos)

```java
// 1. Order Service crea orden y publica evento
@Service
public class OrderService {

    public Order createOrder(CreateOrderRequest request) {
        Order order = new Order();
        order.setStatus(OrderStatus.PENDING);
        orderRepository.save(order);

        // Publicar evento
        eventPublisher.publish(new OrderCreatedEvent(order.getId()));

        return order;
    }

    // Si el pago falla, compensar
    @EventListener
    public void onPaymentFailed(PaymentFailedEvent event) {
        Order order = orderRepository.findById(event.getOrderId());
        order.setStatus(OrderStatus.CANCELLED);
        orderRepository.save(order);

        eventPublisher.publish(new OrderCancelledEvent(order.getId()));
    }
}

// 2. Payment Service escucha y procesa pago
@Service
public class PaymentService {

    @EventListener
    public void onOrderCreated(OrderCreatedEvent event) {
        try {
            processPayment(event.getOrderId());
            eventPublisher.publish(new PaymentSuccessEvent(event.getOrderId()));
        } catch (PaymentException e) {
            eventPublisher.publish(new PaymentFailedEvent(event.getOrderId()));
        }
    }
}

// 3. Inventory Service escucha y reserva stock
@Service
public class InventoryService {

    @EventListener
    public void onPaymentSuccess(PaymentSuccessEvent event) {
        try {
            reserveStock(event.getOrderId());
            eventPublisher.publish(new StockReservedEvent(event.getOrderId()));
        } catch (InsufficientStockException e) {
            eventPublisher.publish(new StockReservationFailedEvent(event.getOrderId()));
            // Esto triggerea compensación en Payment Service
        }
    }

    // Compensación si la orden se cancela
    @EventListener
    public void onOrderCancelled(OrderCancelledEvent event) {
        releaseStock(event.getOrderId());
    }
}
```

**Flujo:**
```
HAPPY PATH:
Order Created → Payment Success → Stock Reserved → Shipping Scheduled

SAD PATH (Payment falla):
Order Created → Payment Failed → Order Cancelled

SAD PATH (Stock falla):
Order Created → Payment Success → Stock Failed → Refund Payment → Order Cancelled
```

#### b) Orchestration-based Saga (Orquestador)

```java
// Orchestrator Service coordina la saga
@Service
public class OrderSagaOrchestrator {

    @Autowired
    private PaymentServiceClient paymentClient;

    @Autowired
    private InventoryServiceClient inventoryClient;

    @Autowired
    private ShippingServiceClient shippingClient;

    public void executeOrderSaga(Order order) {
        try {
            // Paso 1: Procesar pago
            PaymentResult payment = paymentClient.processPayment(order.getId());

            if (!payment.isSuccess()) {
                cancelOrder(order);
                return;
            }

            // Paso 2: Reservar stock
            try {
                inventoryClient.reserveStock(order.getProductId(), order.getQuantity());
            } catch (InsufficientStockException e) {
                // Compensación: revertir pago
                paymentClient.refundPayment(payment.getId());
                cancelOrder(order);
                return;
            }

            // Paso 3: Programar envío
            try {
                shippingClient.scheduleShipment(order.getId());
            } catch (ShippingException e) {
                // Compensación: revertir stock y pago
                inventoryClient.releaseStock(order.getProductId(), order.getQuantity());
                paymentClient.refundPayment(payment.getId());
                cancelOrder(order);
                return;
            }

            // Éxito
            order.setStatus(OrderStatus.CONFIRMED);
            orderRepository.save(order);

        } catch (Exception e) {
            // Compensación global
            compensate(order);
        }
    }

    private void compensate(Order order) {
        // Revertir todos los cambios
    }
}
```

**Comparación:**

```
CHOREOGRAPHY (Eventos):
✓ Desacoplamiento
✓ No hay punto único de fallo
❌ Difícil seguir el flujo
❌ Complejidad distribuida

ORCHESTRATION (Orquestador):
✓ Fácil seguir el flujo (lógica centralizada)
✓ Fácil debugging
❌ Acoplamiento al orquestador
❌ Punto único de fallo
```

---

### 6.3 CQRS (Command Query Responsibility Segregation)

```
Separar operaciones de ESCRITURA (Commands) de LECTURA (Queries)

┌─────────────────────────────────────────┐
│            CQRS PATTERN                 │
├─────────────────────────────────────────┤
│                                         │
│  WRITE SIDE                READ SIDE    │
│  (Comandos)                (Queries)    │
│                                         │
│  ┌──────────┐             ┌──────────┐ │
│  │Commands  │             │ Queries  │ │
│  │Service   │             │ Service  │ │
│  └────┬─────┘             └────┬─────┘ │
│       │                        │       │
│       ↓                        ↓       │
│  ┌─────────┐              ┌─────────┐ │
│  │Write DB │              │ Read DB │ │
│  │(Normal) │─────sync────►│(Optimiz)│ │
│  └─────────┘              └─────────┘ │
│                                        │
└────────────────────────────────────────┘
```

**Implementación:**

```java
// WRITE SIDE (Commands)
@Service
public class OrderCommandService {

    @Autowired
    private OrderWriteRepository orderWriteRepository;

    @Autowired
    private EventPublisher eventPublisher;

    public Order createOrder(CreateOrderCommand command) {
        // Escribir en BD normalizada
        Order order = new Order();
        order.setProductId(command.getProductId());
        order.setQuantity(command.getQuantity());

        orderWriteRepository.save(order);

        // Publicar evento para sincronizar Read Side
        eventPublisher.publish(new OrderCreatedEvent(order));

        return order;
    }
}

// READ SIDE (Queries)
@Service
public class OrderQueryService {

    @Autowired
    private OrderReadRepository orderReadRepository;

    public OrderDetailsView getOrderDetails(String orderId) {
        // Leer de BD desnormalizada (optimizada para lectura)
        return orderReadRepository.findOrderDetailsById(orderId);
    }

    public List<OrderListView> getAllOrders() {
        // Vista desnormalizada con JOINs pre-calculados
        return orderReadRepository.findAllOrdersList();
    }
}

// Projection (sincroniza Write → Read)
@Service
public class OrderProjection {

    @Autowired
    private OrderReadRepository orderReadRepository;

    @EventListener
    public void onOrderCreated(OrderCreatedEvent event) {
        // Crear vista desnormalizada para lectura
        OrderDetailsView view = new OrderDetailsView();
        view.setOrderId(event.getOrderId());
        view.setProductName(event.getProductName()); // Desnormalizado
        view.setCustomerName(event.getCustomerName()); // Desnormalizado

        orderReadRepository.save(view);
    }
}
```

**Ventajas:**
```
✓ Escalado independiente (READ vs WRITE)
✓ Optimización específica (READ para queries complejas)
✓ Separation of concerns
```

**Desventajas:**
```
❌ Complejidad (dos modelos)
❌ Eventual consistency
❌ Sincronización
```

---

### 6.4 Event Sourcing

```
En lugar de guardar el ESTADO actual, guardar TODOS LOS EVENTOS

TRADICIONAL (state-based):
┌─────────────┐
│ Order       │
├─────────────┤
│ id: 123     │
│ status: PAID│  ← Solo estado actual
│ total: $100 │
└─────────────┘

EVENT SOURCING (event-based):
┌──────────────────────────────┐
│ Event Store                  │
├──────────────────────────────┤
│ OrderCreated(id:123)         │
│ OrderItemAdded(product:1)    │
│ OrderItemAdded(product:2)    │
│ OrderSubmitted()             │
│ PaymentProcessed(amount:100) │ ← Histórico completo
└──────────────────────────────┘

Estado actual = replay de todos los eventos
```

**Implementación:**

```java
// Event Store
public interface EventStore {
    void save(DomainEvent event);
    List<DomainEvent> getEvents(String aggregateId);
}

// Aggregate (Order)
public class Order {
    private String id;
    private OrderStatus status;
    private List<OrderItem> items;
    private List<DomainEvent> uncommittedEvents = new ArrayList<>();

    // No setters públicos, solo comandos

    public void create(String orderId, String customerId) {
        // Crear evento
        OrderCreatedEvent event = new OrderCreatedEvent(orderId, customerId);

        // Aplicar evento
        apply(event);

        // Registrar para persistir
        uncommittedEvents.add(event);
    }

    public void addItem(String productId, int quantity) {
        OrderItemAddedEvent event = new OrderItemAddedEvent(id, productId, quantity);
        apply(event);
        uncommittedEvents.add(event);
    }

    public void submit() {
        if (items.isEmpty()) {
            throw new EmptyOrderException();
        }

        OrderSubmittedEvent event = new OrderSubmittedEvent(id);
        apply(event);
        uncommittedEvents.add(event);
    }

    // Aplicar eventos (reconstruir estado)
    private void apply(DomainEvent event) {
        if (event instanceof OrderCreatedEvent) {
            OrderCreatedEvent e = (OrderCreatedEvent) event;
            this.id = e.getOrderId();
            this.status = OrderStatus.PENDING;
            this.items = new ArrayList<>();
        } else if (event instanceof OrderItemAddedEvent) {
            OrderItemAddedEvent e = (OrderItemAddedEvent) event;
            this.items.add(new OrderItem(e.getProductId(), e.getQuantity()));
        } else if (event instanceof OrderSubmittedEvent) {
            this.status = OrderStatus.SUBMITTED;
        }
    }

    // Reconstruir desde eventos (hydration)
    public static Order fromEvents(List<DomainEvent> events) {
        Order order = new Order();
        for (DomainEvent event : events) {
            order.apply(event);
        }
        return order;
    }

    public List<DomainEvent> getUncommittedEvents() {
        return uncommittedEvents;
    }
}

// Repository
@Service
public class OrderRepository {

    @Autowired
    private EventStore eventStore;

    public void save(Order order) {
        // Guardar eventos, no estado
        for (DomainEvent event : order.getUncommittedEvents()) {
            eventStore.save(event);
        }
    }

    public Order findById(String orderId) {
        // Reconstruir desde eventos
        List<DomainEvent> events = eventStore.getEvents(orderId);
        return Order.fromEvents(events);
    }
}
```

**Ventajas:**
```
✓ Auditoría completa (histórico total)
✓ Debugging (replay eventos)
✓ Temporal queries ("¿cómo estaba el pedido el 1 de enero?")
✓ Event-driven architecture natural
```

**Desventajas:**
```
❌ Complejidad alta
❌ Performance (reconstruir estado = replay todos los eventos)
❌ Event schema evolution
❌ Requiere snapshots para performance
```

---

## 7. Service Discovery y API Gateway

### 7.1 Service Discovery

**Problema:**
```
En microservicios, los servicios se despliegan dinámicamente:
- IPs cambian
- Instancias se agregan/eliminan
- Escalado automático

¿Cómo encuentra Order Service a Payment Service?
```

**Solución: Service Registry**

```
┌─────────────────────────────────────────┐
│       SERVICE REGISTRY                  │
│       (Consul, Eureka, etcd)            │
│                                         │
│  ┌───────────────────────────────────┐  │
│  │ Service Name      |  Instances    │  │
│  ├───────────────────────────────────┤  │
│  │ order-service     | 10.0.1.5:8080│  │
│  │                   | 10.0.1.6:8080│  │
│  │ payment-service   | 10.0.2.3:8080│  │
│  │ product-service   | 10.0.3.1:8080│  │
│  │                   | 10.0.3.2:8080│  │
│  │                   | 10.0.3.3:8080│  │
│  └───────────────────────────────────┘  │
└─────────────────────────────────────────┘

Servicios:
1. Se registran al iniciar
2. Envían heartbeats
3. Se dan de baja al cerrar
```

**Implementación con Spring Cloud Eureka:**

```java
// Eureka Server
@SpringBootApplication
@EnableEurekaServer
public class EurekaServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(EurekaServerApplication.class, args);
    }
}

// application.yml (Eureka Server)
server:
  port: 8761

eureka:
  client:
    register-with-eureka: false
    fetch-registry: false
```

```java
// Order Service (Eureka Client)
@SpringBootApplication
@EnableDiscoveryClient
public class OrderServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(OrderServiceApplication.class, args);
    }
}

// application.yml (Order Service)
spring:
  application:
    name: order-service

eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/
  instance:
    prefer-ip-address: true
```

```java
// Consumir otro servicio (con load balancing)
@Service
public class OrderService {

    @Autowired
    private RestTemplate restTemplate;  // Con @LoadBalanced

    public void createOrder(CreateOrderRequest request) {
        // Eureka resuelve "payment-service" a IPs reales
        // Y hace load balancing automáticamente
        PaymentResponse payment = restTemplate.postForObject(
            "http://payment-service/payments",  // ← Nombre del servicio
            paymentRequest,
            PaymentResponse.class
        );
    }
}

@Configuration
public class RestTemplateConfig {

    @Bean
    @LoadBalanced  // ← Importante: habilita service discovery
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

---

### 7.2 API Gateway

**Problema:**
```
Clientes (web, mobile) necesitan:
- Llamar a múltiples microservicios
- Diferentes protocolos (HTTP, gRPC, WebSocket)
- Autenticación/Autorización
- Rate limiting
- Logging/Monitoreo

¿El cliente debe conocer todos los servicios?
❌ Complejidad para el cliente
❌ Cambios en servicios afectan clientes
❌ Múltiples round-trips
```

**Solución: API Gateway**

```
┌─────────────────────────────────────────┐
│          CLIENTES                       │
│  (Web, Mobile, Third-party)             │
└──────────────┬──────────────────────────┘
               │
               │ Single Entry Point
               ↓
┌──────────────────────────────────────────┐
│         API GATEWAY                      │
│  - Routing                               │
│  - Authentication/Authorization          │
│  - Rate Limiting                         │
│  - Request/Response Transformation       │
│  - Caching                               │
│  - Load Balancing                        │
│  - Circuit Breaking                      │
│  - Logging/Monitoring                    │
└──────────────┬───────────────────────────┘
               │
       ┌───────┼───────┐
       │       │       │
       ↓       ↓       ↓
   ┌─────┐ ┌─────┐ ┌─────┐
   │User │ │Product Order│
   │Svc  │ │ Svc │ │ Svc │
   └─────┘ └─────┘ └─────┘
```

**Implementación con Spring Cloud Gateway:**

```java
@SpringBootApplication
public class ApiGatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(ApiGatewayApplication.class, args);
    }
}

// application.yml
spring:
  cloud:
    gateway:
      routes:
        # Ruta para User Service
        - id: user-service
          uri: lb://user-service  # lb = load balanced via Eureka
          predicates:
            - Path=/api/users/**
          filters:
            - RewritePath=/api/users/(?<segment>.*), /${segment}
            - AddRequestHeader=X-Request-Source, API-Gateway

        # Ruta para Product Service
        - id: product-service
          uri: lb://product-service
          predicates:
            - Path=/api/products/**
          filters:
            - RewritePath=/api/products/(?<segment>.*), /${segment}

        # Ruta para Order Service (con autenticación)
        - id: order-service
          uri: lb://order-service
          predicates:
            - Path=/api/orders/**
          filters:
            - RewritePath=/api/orders/(?<segment>.*), /${segment}
            - AuthenticationFilter  # Custom filter

      # Configuración global
      default-filters:
        - name: Retry
          args:
            retries: 3
            statuses: BAD_GATEWAY
        - name: CircuitBreaker
          args:
            name: defaultCircuitBreaker
            fallbackUri: forward:/fallback
```

```java
// Filtro de autenticación personalizado
@Component
public class AuthenticationFilter implements GatewayFilter {

    @Autowired
    private JwtTokenProvider tokenProvider;

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        ServerHttpRequest request = exchange.getRequest();

        // Verificar header Authorization
        if (!request.getHeaders().containsKey("Authorization")) {
            return unauthorized(exchange);
        }

        String token = request.getHeaders().getFirst("Authorization");

        // Validar JWT
        if (!tokenProvider.validateToken(token)) {
            return unauthorized(exchange);
        }

        // Extraer info del usuario y agregarla a headers
        String userId = tokenProvider.getUserId(token);
        ServerHttpRequest modifiedRequest = request.mutate()
            .header("X-User-Id", userId)
            .build();

        return chain.filter(exchange.mutate().request(modifiedRequest).build());
    }

    private Mono<Void> unauthorized(ServerWebExchange exchange) {
        exchange.getResponse().setStatusCode(HttpStatus.UNAUTHORIZED);
        return exchange.getResponse().setComplete();
    }
}
```

**Ventajas:**
```
✓ Punto único de entrada
✓ Simplifica clientes
✓ Centraliza cross-cutting concerns (auth, logging, etc.)
✓ Versionado de APIs
✓ A/B testing, canary deployments
```

**Desventajas:**
```
❌ Punto único de fallo (mitigar con HA)
❌ Puede convertirse en bottleneck
❌ Complejidad adicional
```

---

## 8. Resiliencia y Tolerancia a Fallos

### 8.1 Circuit Breaker Pattern

**Problema:**
```
Order Service llama a Payment Service
Payment Service está caído

Sin circuit breaker:
- Order Service espera timeout (30s)
- Múltiples requests esperando
- Recursos agotados
- Cascada de fallos
```

**Solución: Circuit Breaker**

```
ESTADOS:

CLOSED (normal):
┌─────────┐      ┌─────────┐
│ Order   │─────►│ Payment │
│ Service │◄─────│ Service │
└─────────┘      └─────────┘
Requests pasan normalmente

OPEN (servicio caído):
┌─────────┐      ┌─────────┐
│ Order   │  X   │ Payment │
│ Service │      │ Service │
└─────────┘      └─────────┘
Requests fallan rápido (sin intentar)

HALF-OPEN (probando):
┌─────────┐      ┌─────────┐
│ Order   │─────►│ Payment │
│ Service │      │ Service │
└─────────┘      └─────────┘
Permite algunos requests de prueba
```

**Implementación con Resilience4j:**

```java
@Service
public class OrderService {

    @Autowired
    private PaymentServiceClient paymentClient;

    @CircuitBreaker(name = "paymentService", fallbackMethod = "paymentFallback")
    @Retry(name = "paymentService")
    @TimeLimiter(name = "paymentService")
    public Order createOrder(CreateOrderRequest request) {
        // Llamada a Payment Service
        PaymentResponse payment = paymentClient.processPayment(request);

        // Continuar con orden...
        Order order = new Order();
        order.setPaymentId(payment.getId());
        return orderRepository.save(order);
    }

    // Fallback cuando Payment Service falla
    public Order paymentFallback(CreateOrderRequest request, Exception ex) {
        // Degradación elegante
        Order order = new Order();
        order.setStatus(OrderStatus.PAYMENT_PENDING);
        order.setNote("Payment service unavailable. Will retry later.");
        return orderRepository.save(order);
    }
}
```

```yaml
# application.yml
resilience4j:
  circuitbreaker:
    instances:
      paymentService:
        register-health-indicator: true
        sliding-window-size: 10
        minimum-number-of-calls: 5
        permitted-number-of-calls-in-half-open-state: 3
        automatic-transition-from-open-to-half-open-enabled: true
        wait-duration-in-open-state: 10s
        failure-rate-threshold: 50
        slow-call-rate-threshold: 100
        slow-call-duration-threshold: 2s

  retry:
    instances:
      paymentService:
        max-attempts: 3
        wait-duration: 1s

  timelimiter:
    instances:
      paymentService:
        timeout-duration: 5s
```

---

### 8.2 Bulkhead Pattern

**Problema:**
```
Un servicio lento consume todos los threads
Otros servicios no pueden ejecutarse
```

**Solución: Bulkhead (Compartimentos estancos)**

```
SIN BULKHEAD:
┌────────────────────────────┐
│ Thread Pool (100 threads)  │
│                            │
│ ████████████████████       │ ← Payment Service (lento)
│ ███                        │ ← Product Service (bloqueado)
│                            │ ← User Service (bloqueado)
└────────────────────────────┘

CON BULKHEAD:
┌────────────────────────────┐
│ Thread Pools separados     │
│                            │
│ ████████ Payment (50)      │ ← Solo Payment afectado
│ ███      Product (30)      │ ← Funcionando normal
│ █        User (20)         │ ← Funcionando normal
└────────────────────────────┘
```

```java
@Service
public class OrderService {

    @Bulkhead(name = "paymentService", fallbackMethod = "paymentFallback")
    public PaymentResponse processPayment(PaymentRequest request) {
        return paymentClient.processPayment(request);
    }

    @Bulkhead(name = "inventoryService", fallbackMethod = "inventoryFallback")
    public InventoryResponse checkInventory(String productId) {
        return inventoryClient.checkStock(productId);
    }
}
```

```yaml
resilience4j:
  bulkhead:
    instances:
      paymentService:
        max-concurrent-calls: 10
        max-wait-duration: 1s

      inventoryService:
        max-concurrent-calls: 5
        max-wait-duration: 500ms
```

---

### 8.3 Timeout Pattern

```java
@Service
public class OrderService {

    @Autowired
    private RestTemplate restTemplate;

    public Product getProduct(String productId) {
        // Configurar timeout
        restTemplate.getInterceptors().add((request, body, execution) -> {
            // Timeout de 2 segundos
            return execution.execute(request, body);
        });

        try {
            return restTemplate.getForObject(
                "http://product-service/products/" + productId,
                Product.class
            );
        } catch (ResourceAccessException e) {
            // Timeout ocurrió
            throw new ServiceTimeoutException("Product service timed out");
        }
    }
}
```

---

### 8.4 Retry Pattern

```java
@Service
public class OrderService {

    @Retry(name = "productService", fallbackMethod = "getProductFallback")
    public Product getProduct(String productId) {
        return productClient.getProduct(productId);
    }

    // Fallback después de agotar reintentos
    public Product getProductFallback(String productId, Exception ex) {
        // Retornar producto desde caché
        return productCache.get(productId);
    }
}
```

```yaml
resilience4j:
  retry:
    instances:
      productService:
        max-attempts: 3
        wait-duration: 1s
        exponential-backoff-multiplier: 2  # 1s, 2s, 4s
        retry-exceptions:
          - java.net.ConnectException
          - java.io.IOException
```

---

## 9. Despliegue y DevOps

### 9.1 Containerización (Docker)

```dockerfile
# Dockerfile para Order Service
FROM openjdk:17-jdk-slim

# Información
LABEL maintainer="team@example.com"
LABEL service="order-service"

# Crear directorio de trabajo
WORKDIR /app

# Copiar JAR
COPY target/order-service-1.0.0.jar app.jar

# Exponer puerto
EXPOSE 8080

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=60s --retries=3 \
  CMD curl -f http://localhost:8080/actuator/health || exit 1

# Ejecutar aplicación
ENTRYPOINT ["java", "-jar", "app.jar"]
```

```yaml
# docker-compose.yml (desarrollo local)
version: '3.8'

services:
  # Service Registry
  eureka-server:
    image: eureka-server:latest
    ports:
      - "8761:8761"
    networks:
      - microservices-network

  # Order Service
  order-service:
    build: ./order-service
    ports:
      - "8081:8080"
    environment:
      - SPRING_PROFILES_ACTIVE=docker
      - EUREKA_CLIENT_SERVICEURL_DEFAULTZONE=http://eureka-server:8761/eureka/
      - SPRING_DATASOURCE_URL=jdbc:postgresql://order-db:5432/orders
    depends_on:
      - eureka-server
      - order-db
    networks:
      - microservices-network

  order-db:
    image: postgres:15
    environment:
      - POSTGRES_DB=orders
      - POSTGRES_USER=orderuser
      - POSTGRES_PASSWORD=orderpass
    volumes:
      - order-data:/var/lib/postgresql/data
    networks:
      - microservices-network

  # Product Service
  product-service:
    build: ./product-service
    ports:
      - "8082:8080"
    environment:
      - SPRING_PROFILES_ACTIVE=docker
      - EUREKA_CLIENT_SERVICEURL_DEFAULTZONE=http://eureka-server:8761/eureka/
    depends_on:
      - eureka-server
      - product-db
    networks:
      - microservices-network

  product-db:
    image: mongo:6
    environment:
      - MONGO_INITDB_ROOT_USERNAME=root
      - MONGO_INITDB_ROOT_PASSWORD=mongpass
    volumes:
      - product-data:/data/db
    networks:
      - microservices-network

  # Payment Service
  payment-service:
    build: ./payment-service
    ports:
      - "8083:8080"
    environment:
      - SPRING_PROFILES_ACTIVE=docker
      - EUREKA_CLIENT_SERVICEURL_DEFAULTZONE=http://eureka-server:8761/eureka/
    depends_on:
      - eureka-server
    networks:
      - microservices-network

  # API Gateway
  api-gateway:
    build: ./api-gateway
    ports:
      - "8080:8080"
    environment:
      - SPRING_PROFILES_ACTIVE=docker
      - EUREKA_CLIENT_SERVICEURL_DEFAULTZONE=http://eureka-server:8761/eureka/
    depends_on:
      - eureka-server
      - order-service
      - product-service
      - payment-service
    networks:
      - microservices-network

networks:
  microservices-network:
    driver: bridge

volumes:
  order-data:
  product-data:
```

---

### 9.2 Orquestación (Kubernetes)

```yaml
# order-service-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: order-service
  labels:
    app: order-service
spec:
  replicas: 3  # 3 instancias
  selector:
    matchLabels:
      app: order-service
  template:
    metadata:
      labels:
        app: order-service
    spec:
      containers:
      - name: order-service
        image: myregistry/order-service:1.0.0
        ports:
        - containerPort: 8080
        env:
        - name: SPRING_PROFILES_ACTIVE
          value: "kubernetes"
        - name: SPRING_DATASOURCE_URL
          value: "jdbc:postgresql://order-db:5432/orders"
        - name: SPRING_DATASOURCE_USERNAME
          valueFrom:
            secretKeyRef:
              name: order-db-secret
              key: username
        - name: SPRING_DATASOURCE_PASSWORD
          valueFrom:
            secretKeyRef:
              name: order-db-secret
              key: password
        resources:
          requests:
            memory: "512Mi"
            cpu: "500m"
          limits:
            memory: "1Gi"
            cpu: "1000m"
        livenessProbe:
          httpGet:
            path: /actuator/health/liveness
            port: 8080
          initialDelaySeconds: 60
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /actuator/health/readiness
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 5

---
# order-service-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: order-service
spec:
  selector:
    app: order-service
  ports:
  - protocol: TCP
    port: 8080
    targetPort: 8080
  type: ClusterIP

---
# order-service-hpa.yaml (Horizontal Pod Autoscaler)
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: order-service-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: order-service
  minReplicas: 3
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
```

---

### 9.3 CI/CD Pipeline

```yaml
# .github/workflows/order-service-ci-cd.yml
name: Order Service CI/CD

on:
  push:
    branches: [ main, develop ]
    paths:
      - 'order-service/**'
  pull_request:
    branches: [ main ]

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Set up JDK 17
        uses: actions/setup-java@v3
        with:
          java-version: '17'
          distribution: 'temurin'

      - name: Cache Maven packages
        uses: actions/cache@v3
        with:
          path: ~/.m2
          key: ${{ runner.os }}-m2-${{ hashFiles('**/pom.xml') }}

      - name: Build with Maven
        working-directory: ./order-service
        run: mvn clean package -DskipTests

      - name: Run Unit Tests
        working-directory: ./order-service
        run: mvn test

      - name: Run Integration Tests
        working-directory: ./order-service
        run: mvn verify

      - name: SonarQube Scan
        working-directory: ./order-service
        run: mvn sonar:sonar -Dsonar.projectKey=order-service

      - name: Build Docker Image
        run: docker build -t myregistry/order-service:${{ github.sha }} ./order-service

      - name: Push Docker Image
        if: github.ref == 'refs/heads/main'
        run: |
          echo ${{ secrets.DOCKER_PASSWORD }} | docker login -u ${{ secrets.DOCKER_USERNAME }} --password-stdin
          docker push myregistry/order-service:${{ github.sha }}
          docker tag myregistry/order-service:${{ github.sha }} myregistry/order-service:latest
          docker push myregistry/order-service:latest

  deploy-to-staging:
    needs: build-and-test
    if: github.ref == 'refs/heads/develop'
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to Staging (Kubernetes)
        run: |
          kubectl set image deployment/order-service \
            order-service=myregistry/order-service:${{ github.sha }} \
            -n staging

  deploy-to-production:
    needs: build-and-test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to Production (Kubernetes - Canary)
        run: |
          # Deploy canary (10% traffic)
          kubectl set image deployment/order-service-canary \
            order-service=myregistry/order-service:${{ github.sha }} \
            -n production

      - name: Wait for health checks
        run: sleep 300  # 5 minutos

      - name: Full deployment if canary successful
        run: |
          kubectl set image deployment/order-service \
            order-service=myregistry/order-service:${{ github.sha }} \
            -n production
```

---

## 10. Ejemplo Práctico Completo

### Escenario: E-commerce con Microservicios

**Servicios:**
1. User Service
2. Product Service
3. Order Service
4. Payment Service
5. Inventory Service
6. Notification Service

### Flujo: Crear una Orden

```
┌─────────┐
│ Cliente │
└────┬────┘
     │
     │ 1. POST /api/orders
     ↓
┌──────────────┐
│ API Gateway  │
└──────┬───────┘
       │
       │ 2. Forward to Order Service
       ↓
┌──────────────────┐
│  Order Service   │──────┐
└──────────────────┘      │
       │                  │ 3. Get Product Info
       │                  ↓
       │            ┌──────────────────┐
       │            │ Product Service  │
       │            └──────────────────┘
       │
       │ 4. Publish OrderCreated Event
       ↓
┌──────────────────┐
│  Message Broker  │
│    (Kafka)       │
└────┬─────┬───┬───┘
     │     │   │
     │     │   │ 5. Consume Events
     ↓     ↓   ↓
┌─────┐ ┌────┐ ┌──────┐
│Pay  │ │Inv.│ │Notif.│
│Svc  │ │Svc │ │ Svc  │
└─────┘ └────┘ └──────┘
```

### Implementación

#### 1. Order Service

```java
@RestController
@RequestMapping("/orders")
public class OrderController {

    @Autowired
    private OrderService orderService;

    @PostMapping
    public ResponseEntity<OrderResponse> createOrder(@RequestBody CreateOrderRequest request) {
        Order order = orderService.createOrder(request);
        return ResponseEntity.status(HttpStatus.CREATED).body(toResponse(order));
    }
}

@Service
public class OrderService {

    @Autowired
    private OrderRepository orderRepository;

    @Autowired
    private ProductServiceClient productClient;

    @Autowired
    private KafkaTemplate<String, OrderEvent> kafkaTemplate;

    @Transactional
    public Order createOrder(CreateOrderRequest request) {
        // 1. Obtener info del producto (llamada síncrona)
        Product product = productClient.getProduct(request.getProductId());

        if (product == null) {
            throw new ProductNotFoundException();
        }

        // 2. Crear orden
        Order order = new Order();
        order.setUserId(request.getUserId());
        order.setProductId(product.getId());
        order.setQuantity(request.getQuantity());
        order.setTotalPrice(product.getPrice().multiply(BigDecimal.valueOf(request.getQuantity())));
        order.setStatus(OrderStatus.PENDING);

        order = orderRepository.save(order);

        // 3. Publicar evento (asíncrono)
        OrderCreatedEvent event = new OrderCreatedEvent(
            order.getId(),
            order.getUserId(),
            order.getProductId(),
            order.getQuantity(),
            order.getTotalPrice()
        );

        kafkaTemplate.send("order-events", event);

        return order;
    }
}

// Cliente a Product Service (Feign)
@FeignClient(name = "product-service")
public interface ProductServiceClient {

    @GetMapping("/products/{id}")
    Product getProduct(@PathVariable String id);
}
```

#### 2. Product Service

```java
@RestController
@RequestMapping("/products")
public class ProductController {

    @Autowired
    private ProductRepository productRepository;

    @GetMapping("/{id}")
    public ResponseEntity<Product> getProduct(@PathVariable String id) {
        Product product = productRepository.findById(id)
            .orElseThrow(() -> new ProductNotFoundException(id));

        return ResponseEntity.ok(product);
    }
}
```

#### 3. Payment Service (Event Consumer)

```java
@Service
public class PaymentEventConsumer {

    @Autowired
    private PaymentService paymentService;

    @KafkaListener(topics = "order-events", groupId = "payment-service")
    public void consumeOrderCreated(OrderCreatedEvent event) {
        // Procesar pago de forma asíncrona
        try {
            Payment payment = paymentService.processPayment(event);

            // Publicar evento de pago exitoso
            PaymentSuccessEvent successEvent = new PaymentSuccessEvent(
                event.getOrderId(),
                payment.getId(),
                payment.getAmount()
            );

            kafkaTemplate.send("payment-events", successEvent);

        } catch (PaymentException e) {
            // Publicar evento de pago fallido
            PaymentFailedEvent failedEvent = new PaymentFailedEvent(
                event.getOrderId(),
                e.getMessage()
            );

            kafkaTemplate.send("payment-events", failedEvent);
        }
    }
}
```

#### 4. Inventory Service (Event Consumer)

```java
@Service
public class InventoryEventConsumer {

    @Autowired
    private InventoryService inventoryService;

    @KafkaListener(topics = "order-events", groupId = "inventory-service")
    public void consumeOrderCreated(OrderCreatedEvent event) {
        // Reservar stock
        try {
            inventoryService.reserveStock(event.getProductId(), event.getQuantity());

            // Publicar evento
            StockReservedEvent stockEvent = new StockReservedEvent(
                event.getOrderId(),
                event.getProductId(),
                event.getQuantity()
            );

            kafkaTemplate.send("inventory-events", stockEvent);

        } catch (InsufficientStockException e) {
            // Publicar evento de fallo
            StockReservationFailedEvent failedEvent = new StockReservationFailedEvent(
                event.getOrderId(),
                event.getProductId(),
                e.getMessage()
            );

            kafkaTemplate.send("inventory-events", failedEvent);
        }
    }
}
```

#### 5. Notification Service

```java
@Service
public class NotificationEventConsumer {

    @Autowired
    private EmailService emailService;

    @KafkaListener(topics = "order-events", groupId = "notification-service")
    public void consumeOrderCreated(OrderCreatedEvent event) {
        // Enviar email de confirmación
        emailService.sendOrderConfirmation(event.getUserId(), event.getOrderId());
    }

    @KafkaListener(topics = "payment-events", groupId = "notification-service")
    public void consumePaymentSuccess(PaymentSuccessEvent event) {
        // Enviar email de pago exitoso
        emailService.sendPaymentConfirmation(event.getOrderId());
    }
}
```

---

## 11. Microservicios vs Monolitos

### Comparación

| Aspecto | Monolito | Microservicios |
|---------|----------|----------------|
| **Complejidad** | ✅ Simple al inicio | ❌ Complejo desde el inicio |
| **Desarrollo** | ✅ Rápido al inicio | ❌ Más lento (coordinación) |
| **Testing** | ✅ Fácil (todo en un lugar) | ❌ Difícil (integración) |
| **Despliegue** | ❌ Todo o nada | ✅ Independiente |
| **Escalado** | ❌ Vertical, todo junto | ✅ Horizontal, independiente |
| **Tecnología** | ❌ Una sola stack | ✅ Libertad tecnológica |
| **Fallas** | ❌ Cascada de fallos | ✅ Aislamiento de fallos |
| **Equipos** | ❌ Todos en misma codebase | ✅ Equipos autónomos |
| **Performance** | ✅ In-process (rápido) | ❌ Network calls (lento) |
| **Data Consistency** | ✅ ACID transactions | ❌ Eventual consistency |
| **Debugging** | ✅ Fácil (stack traces) | ❌ Difícil (distribuido) |
| **Infraestructura** | ✅ Simple (1 servidor) | ❌ Compleja (K8s, etc.) |
| **Monitoreo** | ✅ Simple | ❌ Complejo (múltiples servicios) |
| **Costo Inicial** | ✅ Bajo | ❌ Alto |
| **Costo a Escala** | ❌ Alto | ✅ Optimizable |

---

### Cuándo Elegir Cada Uno

```
MONOLITO cuando:
✓ Startup pequeño/MVP
✓ Equipo pequeño (< 5 developers)
✓ Dominio simple/bien definido
✓ Bajo tráfico
✓ Tiempo al mercado crítico
✓ Recursos limitados

MICROSERVICIOS cuando:
✓ Empresa establecida/escala
✓ Equipos grandes (múltiples equipos)
✓ Dominio complejo
✓ Alto tráfico/necesidad de escalar
✓ Necesidad de tecnologías diferentes
✓ Despliegues frecuentes
✓ Equipos distribuidos geográficamente
```

---

## 12. Cuándo Usar (y Cuándo No)

### ✅ USA Microservicios Cuando:

```
✓ Sistema grande y complejo
✓ Múltiples equipos trabajando en paralelo
✓ Necesidad de escalar partes específicas
✓ Diferentes tecnologías para diferentes problemas
✓ Despliegues frecuentes e independientes
✓ Alta disponibilidad crítica
✓ Organización madura (DevOps culture)
✓ Dominios de negocio bien definidos
```

**Ejemplos:**
```
✓ Netflix (alto tráfico, diferentes dominios)
✓ Amazon (múltiples equipos, alta escala)
✓ Uber (diferentes servicios: rides, payments, maps)
✓ Airbnb (listings, bookings, payments, reviews)
```

### ❌ NO Uses Microservicios Cuando:

```
✗ Startup temprano/MVP
✗ Equipo pequeño (< 10 personas)
✗ Sin experiencia en sistemas distribuidos
✗ Infraestructura limitada
✗ Sin cultura DevOps
✗ Dominio simple
✗ Bajo tráfico
✗ Presupuesto limitado
```

**Ejemplos:**
```
✗ Blog personal
✗ Landing page corporativa
✗ CRUD simple de gestión interna
✗ Prototipo para validar idea
✗ Aplicación con < 1000 usuarios
```

### 🤔 Evalúa con Checklist

```markdown
Responde SÍ/NO:

COMPLEJIDAD:
1. [ ] ¿El sistema tiene > 10 módulos distintos?
2. [ ] ¿Los módulos tienen ciclos de vida diferentes?
3. [ ] ¿Diferentes módulos necesitan diferentes tecnologías?

ESCALA:
4. [ ] ¿Necesitas escalar partes específicas independientemente?
5. [ ] ¿El tráfico es > 10,000 requests/segundo?
6. [ ] ¿Necesitas alta disponibilidad (99.99%+)?

ORGANIZACIÓN:
7. [ ] ¿Tienes > 10 developers?
8. [ ] ¿Múltiples equipos trabajando en paralelo?
9. [ ] ¿Necesitas despliegues independientes por equipo?

INFRAESTRUCTURA:
10. [ ] ¿Tienes experiencia con DevOps?
11. [ ] ¿Puedes invertir en Kubernetes/Cloud?
12. [ ] ¿Tienes monitoreo/observabilidad sofisticado?

RESULTADO:
- 10-12 SÍ: Microservicios definitivamente
- 7-9 SÍ: Considera microservicios
- 4-6 SÍ: Empieza con monolito modular
- 0-3 SÍ: Monolito es mejor opción
```

---

## 13. Migración de Monolito a Microservicios

### 13.1 Estrategia: Strangler Fig Pattern

```
NO reescribir todo de golpe (Big Bang)
SÍ migrar incrementalmente (Strangler Fig)

┌────────────────────────────────────┐
│         MONOLITO                   │
│  ┌──────────────────────────────┐  │
│  │ Users | Products | Orders    │  │
│  │ Payments | Shipping | Billing│  │
│  └──────────────────────────────┘  │
└────────────────────────────────────┘

        ↓ FASE 1: Extraer Orders

┌────────────────────────────────────┐
│         MONOLITO                   │
│  ┌──────────────────────────────┐  │
│  │ Users | Products              │  │
│  │ Payments | Shipping | Billing│  │
│  └──────────────────────────────┘  │
└────────────────────────────────────┘
              +
        ┌──────────┐
        │  Orders  │
        │ Service  │
        └──────────┘

        ↓ FASE 2: Extraer Payments

┌────────────────────────────────────┐
│         MONOLITO                   │
│  ┌──────────────────────────────┐  │
│  │ Users | Products              │  │
│  │ Shipping | Billing            │  │
│  └──────────────────────────────┘  │
└────────────────────────────────────┘
              +
┌──────────┐  ┌──────────┐
│  Orders  │  │ Payments │
│ Service  │  │ Service  │
└──────────┘  └──────────┘

        ↓ FASE N: Monolito vacío

        ┌──────────┐
        │API Gateway│
        └─────┬─────┘
              │
    ┌─────────┼─────────┐
    │         │         │
┌────┴──┐ ┌───┴───┐ ┌──┴────┐
│Orders │ │Payment│ │Product│
│Service│ │Service│ │Service│
└───────┘ └───────┘ └───────┘
```

### 13.2 Pasos de Migración

#### Paso 1: Identificar Bounded Contexts

```
Analizar el monolito:
1. Identificar módulos/dominios
2. Analizar dependencias entre módulos
3. Buscar seams (costuras naturales)
4. Priorizar por:
   - Independencia
   - Cambios frecuentes
   - Necesidad de escalar
```

#### Paso 2: Extraer el Primer Servicio

```java
// ANTES: Todo en el monolito
@Service
public class OrderService {
    @Autowired
    private OrderRepository orderRepository;

    @Autowired
    private PaymentService paymentService;  // En el monolito

    @Autowired
    private InventoryService inventoryService;  // En el monolito

    public Order createOrder(CreateOrderRequest request) {
        // Lógica...
        paymentService.processPayment(...);
        inventoryService.reserveStock(...);
    }
}

// DESPUÉS: Orders como microservicio separado
@Service
public class OrderService {
    @Autowired
    private OrderRepository orderRepository;

    // Ahora son llamadas a servicios remotos
    @Autowired
    private PaymentServiceClient paymentClient;

    @Autowired
    private InventoryServiceClient inventoryClient;

    public Order createOrder(CreateOrderRequest request) {
        // Lógica...
        paymentClient.processPayment(...);  // HTTP call
        inventoryClient.reserveStock(...);  // HTTP call
    }
}
```

#### Paso 3: Implementar Anti-Corruption Layer

```java
// ACL: Protege el nuevo servicio del monolito viejo
@Component
public class MonolithOrderAdapter {

    @Autowired
    private RestTemplate restTemplate;

    private static final String MONOLITH_URL = "http://legacy-monolith";

    public LegacyOrder getLegacyOrder(String orderId) {
        // Llamar al monolito
        LegacyOrderResponse response = restTemplate.getForObject(
            MONOLITH_URL + "/api/legacy/orders/" + orderId,
            LegacyOrderResponse.class
        );

        // Traducir del modelo viejo al nuevo
        return toLegacyOrder(response);
    }

    private LegacyOrder toLegacyOrder(LegacyOrderResponse response) {
        // Mapeo complejo entre modelos
        // Protege nuestro dominio limpio del modelo legacy
    }
}
```

#### Paso 4: Duplicar Datos (Temporal)

```
Durante la transición, puede ser necesario duplicar datos:

┌────────────────┐         ┌────────────────┐
│   MONOLITO     │         │ Order Service  │
│                │         │                │
│ ┌────────────┐ │         │ ┌────────────┐ │
│ │ Orders     │ │◄───sync─┤ │ Orders     │ │
│ │ (Legacy)   │ │         │ │ (New)      │ │
│ └────────────┘ │         │ └────────────┘ │
└────────────────┘         └────────────────┘

Sincronización bidireccional mientras migramos usuarios
```

#### Paso 5: Migrar Tráfico Gradualmente

```
CANARY DEPLOYMENT:

Semana 1: 5% tráfico → nuevo Order Service
          95% tráfico → monolito

Semana 2: 20% → nuevo servicio
          80% → monolito

Semana 4: 50/50

Semana 8: 100% → nuevo servicio
          0% → monolito (apagar)
```

#### Paso 6: Desacoplar Base de Datos

```
ANTES:
┌────────────────────────────────┐
│        MONOLITO                │
└────────────┬───────────────────┘
             │
             ↓
     ┌───────────────┐
     │  Database     │
     │  (Shared)     │
     └───────────────┘

TRANSICIÓN:
┌──────────┐         ┌──────────┐
│ Monolito │         │  Orders  │
│          │         │ Service  │
└────┬─────┘         └────┬─────┘
     │                    │
     ↓                    ↓
┌─────────┐         ┌─────────┐
│Legacy DB│◄───────►│Orders DB│
└─────────┘  sync   └─────────┘

DESPUÉS:
┌──────────┐         ┌──────────┐
│ Monolito │         │  Orders  │
│          │         │ Service  │
└────┬─────┘         └────┬─────┘
     │                    │
     ↓                    ↓
┌─────────┐         ┌─────────┐
│Legacy DB│         │Orders DB│
└─────────┘         └─────────┘
(separated)
```

---

## 14. Errores Comunes

### Error #1: Microservicios Demasiado Pequeños (Nano-services)

```java
// ❌ MAL: Un servicio por cada entidad
user-service/
  - getUser()
  - createUser()

email-service/
  - sendEmail()

phone-service/
  - validatePhone()

address-service/
  - validateAddress()

// Problema: 100+ microservicios para gestionar
// Complejidad innecesaria

// ✅ BIEN: Servicios por dominio de negocio
customer-service/
  - User management
  - Contact information (email, phone)
  - Address management
```

### Error #2: Compartir Base de Datos

```
❌ MAL:
┌──────────┐  ┌──────────┐  ┌──────────┐
│  Order   │  │ Payment  │  │Inventory │
│ Service  │  │ Service  │  │ Service  │
└────┬─────┘  └────┬─────┘  └────┬─────┘
     │             │             │
     └─────────────┼─────────────┘
                   ↓
            ┌─────────────┐
            │  Shared DB  │
            └─────────────┘

Problemas:
- Acoplamiento de esquema
- Cambios afectan todos los servicios
- No hay boundaries claros
- Imposible escalar independientemente

✅ BIEN:
┌──────────┐  ┌──────────┐  ┌──────────┐
│  Order   │  │ Payment  │  │Inventory │
│ Service  │  │ Service  │  │ Service  │
└────┬─────┘  └────┬─────┘  └────┬─────┘
     │             │             │
     ↓             ↓             ↓
┌─────────┐  ┌─────────┐  ┌─────────┐
│Orders DB│  │Payments │  │Inventory│
│         │  │   DB    │  │   DB    │
└─────────┘  └─────────┘  └─────────┘
```

### Error #3: Transacciones Distribuidas con 2PC

```java
// ❌ MAL: Two-Phase Commit (complejo, lento, frágil)
@Transactional
public void createOrder(OrderRequest request) {
    // Fase 1: Preparar
    orderService.prepareOrder(request);
    paymentService.preparePayment(request);
    inventoryService.prepareReservation(request);

    // Fase 2: Commit
    orderService.commit();
    paymentService.commit();
    inventoryService.commit();
}

// Problemas:
// - Bloqueos largos
// - Un servicio caído bloquea todo
// - Complejidad

// ✅ BIEN: Saga Pattern (eventual consistency)
public void createOrder(OrderRequest request) {
    Order order = orderService.createOrder(request);

    // Eventos compensables
    eventBus.publish(new OrderCreated(order.getId()));
    // Payment Service escucha y procesa
    // Inventory Service escucha y reserva
    // Si algo falla, eventos de compensación
}
```

### Error #4: Chatty Communication

```
❌ MAL: Muchas llamadas pequeñas
GET /orders/123
  → GET /users/456       (1 call)
  → GET /products/789    (1 call)
  → GET /payments/321    (1 call)
  → GET /shipping/654    (1 call)

Total: 5 network calls (lento)

✅ BIEN: API Composition o BFF
GET /orders/123/details
  → Order Service compone todo
  → Retorna objeto completo en 1 call

O usar GraphQL para que el cliente pida exactamente lo que necesita
```

### Error #5: Sin Circuit Breakers

```java
// ❌ MAL: Sin protección contra fallos
public Order getOrder(String orderId) {
    Order order = orderRepository.findById(orderId);

    // Payment Service está caído
    // Esperamos 30 segundos timeout
    // Múltiples threads esperando
    // Thread pool agotado
    Payment payment = paymentClient.getPayment(order.getPaymentId());

    return order;
}

// ✅ BIEN: Con Circuit Breaker
@CircuitBreaker(name = "paymentService", fallbackMethod = "getOrderFallback")
public Order getOrder(String orderId) {
    Order order = orderRepository.findById(orderId);

    // Si Payment Service está caído, falla rápido
    Payment payment = paymentClient.getPayment(order.getPaymentId());

    return order;
}

public Order getOrderFallback(String orderId, Exception ex) {
    Order order = orderRepository.findById(orderId);
    order.setPaymentStatus("PENDING");  // Degradación elegante
    return order;
}
```

### Error #6: Ignorar Observabilidad

```
❌ MAL: Sin monitoreo distribuido
- Logs dispersos en cada servicio
- Sin correlación entre servicios
- Debugging imposible

✅ BIEN: Observabilidad desde el inicio
- Distributed Tracing (Zipkin, Jaeger)
- Centralized Logging (ELK, Splunk)
- Metrics (Prometheus, Grafana)
- Correlation IDs en todas las requests
```

---

## 15. Observabilidad y Monitoreo

### 15.1 Los 3 Pilares de Observabilidad

#### 1. Logs (Registros)

```java
// Logging estructurado
@Slf4j
@Service
public class OrderService {

    public Order createOrder(CreateOrderRequest request) {
        String correlationId = MDC.get("correlationId");

        log.info("Creating order - correlationId={}, userId={}, productId={}",
                 correlationId, request.getUserId(), request.getProductId());

        try {
            Order order = // ... crear orden

            log.info("Order created successfully - correlationId={}, orderId={}",
                     correlationId, order.getId());

            return order;

        } catch (Exception e) {
            log.error("Failed to create order - correlationId={}, error={}",
                      correlationId, e.getMessage(), e);
            throw e;
        }
    }
}
```

**Centralización con ELK Stack:**
```
┌──────────┐  ┌──────────┐  ┌──────────┐
│  Order   │  │ Payment  │  │ Product  │
│ Service  │  │ Service  │  │ Service  │
└────┬─────┘  └────┬─────┘  └────┬─────┘
     │             │             │
     │ (logs)      │ (logs)      │ (logs)
     ↓             ↓             ↓
┌────────────────────────────────────────┐
│           Logstash/Fluentd             │
└────────────────┬───────────────────────┘
                 ↓
         ┌───────────────┐
         │ Elasticsearch │
         └───────┬───────┘
                 ↓
         ┌───────────────┐
         │    Kibana     │
         │ (Visualización)│
         └───────────────┘
```

#### 2. Metrics (Métricas)

```java
// Métricas con Micrometer
@Service
public class OrderService {

    private final Counter orderCreatedCounter;
    private final Timer orderCreationTimer;

    public OrderService(MeterRegistry registry) {
        this.orderCreatedCounter = registry.counter("orders.created");
        this.orderCreationTimer = registry.timer("orders.creation.time");
    }

    public Order createOrder(CreateOrderRequest request) {
        return orderCreationTimer.record(() -> {
            Order order = // ... crear orden

            orderCreatedCounter.increment();

            return order;
        });
    }
}
```

**Prometheus + Grafana:**
```yaml
# prometheus.yml
scrape_configs:
  - job_name: 'order-service'
    static_configs:
      - targets: ['order-service:8080']

  - job_name: 'payment-service'
    static_configs:
      - targets: ['payment-service:8080']

  - job_name: 'product-service'
    static_configs:
      - targets: ['product-service:8080']
```

**Dashboard de Grafana:**
```
┌─────────────────────────────────────────┐
│         ORDER SERVICE DASHBOARD         │
├─────────────────────────────────────────┤
│                                         │
│  Requests/sec:  ██████ 1,234           │
│  Error Rate:    ██ 2.3%                │
│  Latency p95:   ██████████ 250ms       │
│  CPU Usage:     ████████ 65%           │
│  Memory Usage:  ██████ 45%             │
│                                         │
│  [Graph: Request Rate over time]       │
│  [Graph: Error Rate over time]         │
│  [Graph: Latency distribution]         │
│                                         │
└─────────────────────────────────────────┘
```

#### 3. Traces (Trazas Distribuidas)

```java
// Trazabilidad con Spring Cloud Sleuth + Zipkin
@RestController
public class OrderController {

    @Autowired
    private OrderService orderService;

    @PostMapping("/orders")
    public Order createOrder(@RequestBody CreateOrderRequest request) {
        // Sleuth automáticamente agrega trace ID y span ID
        return orderService.createOrder(request);
    }
}

@Service
public class OrderService {

    @Autowired
    private ProductServiceClient productClient;

    @Autowired
    private PaymentServiceClient paymentClient;

    public Order createOrder(CreateOrderRequest request) {
        // Span 1: Get product
        Product product = productClient.getProduct(request.getProductId());

        // Span 2: Process payment
        Payment payment = paymentClient.processPayment(request);

        // Span 3: Save order
        Order order = orderRepository.save(new Order(...));

        return order;
    }
}
```

**Zipkin UI:**
```
┌─────────────────────────────────────────────────────────┐
│                DISTRIBUTED TRACE                        │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  Trace ID: 7f8a9b2c-3d4e-5f6g-7h8i-9j0k1l2m3n4o        │
│  Total Duration: 345ms                                  │
│                                                         │
│  ┌─ API Gateway (50ms) ────────────────────────────┐   │
│  │  ┌─ Order Service (250ms) ──────────────────┐   │   │
│  │  │  ┌─ Product Service (80ms) ────────┐     │   │   │
│  │  │  └──────────────────────────────────┘     │   │   │
│  │  │  ┌─ Payment Service (120ms) ──────────┐  │   │   │
│  │  │  └─────────────────────────────────────┘  │   │   │
│  │  │  ┌─ DB Query (30ms) ──┐                   │   │   │
│  │  │  └────────────────────┘                   │   │   │
│  │  └──────────────────────────────────────────┘   │   │
│  └──────────────────────────────────────────────────┘   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### 15.2 Health Checks

```java
// Spring Boot Actuator
@Component
public class DatabaseHealthIndicator implements HealthIndicator {

    @Autowired
    private DataSource dataSource;

    @Override
    public Health health() {
        try (Connection conn = dataSource.getConnection()) {
            if (conn.isValid(2)) {
                return Health.up()
                    .withDetail("database", "PostgreSQL")
                    .withDetail("status", "Connected")
                    .build();
            }
        } catch (SQLException e) {
            return Health.down()
                .withDetail("error", e.getMessage())
                .build();
        }

        return Health.down().build();
    }
}

@Component
public class PaymentServiceHealthIndicator implements HealthIndicator {

    @Autowired
    private PaymentServiceClient paymentClient;

    @Override
    public Health health() {
        try {
            paymentClient.healthCheck();
            return Health.up().build();
        } catch (Exception e) {
            return Health.down()
                .withDetail("error", e.getMessage())
                .build();
        }
    }
}
```

```yaml
# application.yml
management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics,prometheus
  endpoint:
    health:
      show-details: always
      probes:
        enabled: true
  health:
    livenessState:
      enabled: true
    readinessState:
      enabled: true
```

**Endpoints:**
```
GET /actuator/health
{
  "status": "UP",
  "components": {
    "db": {
      "status": "UP",
      "details": {
        "database": "PostgreSQL",
        "status": "Connected"
      }
    },
    "paymentService": {
      "status": "UP"
    },
    "diskSpace": {
      "status": "UP",
      "details": {
        "total": 500000000000,
        "free": 300000000000
      }
    }
  }
}

GET /actuator/health/liveness
{
  "status": "UP"
}

GET /actuator/health/readiness
{
  "status": "UP"
}
```

---

## 16. Preguntas Frecuentes

### ¿Cuántos microservicios debería tener?

**No hay número mágico:**

```
Startup pequeño (5 devs):
- 2-5 microservicios
- Enfoque en simplicidad

Empresa mediana (20 devs):
- 5-15 microservicios
- Un servicio por equipo

Empresa grande (100+ devs):
- 20-100+ microservicios
- Múltiples servicios por dominio

Regla: Empieza con pocos, divide cuando sea necesario
```

### ¿Microservicios = Contenedores/Docker?

**No necesariamente:**

```
Microservicios ≠ Docker/Kubernetes

Puedes tener:
✓ Microservicios sin contenedores (JARs desplegados en VMs)
✓ Monolito en contenedores

Pero:
Los contenedores FACILITAN microservicios
- Empaquetado consistente
- Aislamiento
- Escalado
- Orquestación

Recomendado: Microservicios + Docker + Kubernetes
```

### ¿Cómo manejar autenticación?

**Opciones:**

#### 1. API Gateway maneja auth

```
┌─────────┐
│ Cliente │
└────┬────┘
     │ (JWT token)
     ↓
┌──────────────┐
│ API Gateway  │ ← Verifica JWT
└──────┬───────┘
       │ (User ID en header)
       ↓
   ┌────────┐
   │Services│ ← Confían en Gateway
   └────────┘
```

#### 2. Cada servicio verifica token

```
┌─────────┐
│ Cliente │
└────┬────┘
     │ (JWT token)
     ↓
┌──────────────┐
│ API Gateway  │ ← Solo rutea
└──────┬───────┘
       │ (JWT token)
       ↓
   ┌────────┐
   │Services│ ← Cada uno verifica JWT
   └────────┘
```

#### 3. Token introspection service

```
┌─────────┐
│ Cliente │
└────┬────┘
     │ (Opaque token)
     ↓
┌──────────────┐      ┌─────────────┐
│ API Gateway  │─────►│Auth Service │
└──────┬───────┘      └─────────────┘
       │                   ↑
       │ (User ID)         │ (validate)
       ↓                   │
   ┌────────┐──────────────┘
   │Services│
   └────────┘
```

### ¿Cómo versionar APIs?

**Estrategias:**

```java
// 1. URL Versioning
@GetMapping("/v1/orders")
@GetMapping("/v2/orders")

// 2. Header Versioning
@GetMapping("/orders")
headers = "X-API-Version=1"

// 3. Accept Header
@GetMapping("/orders")
produces = "application/vnd.company.order-v1+json"

// Recomendación: URL versioning (más simple)
```

### ¿Cómo probar microservicios?

**Pirámide de Testing:**

```
           ┌─────────┐
          /  E2E (5%)  \
         /_____________\
        /  Integration  \
       /    Tests (15%)  \
      /___________________\
     /   Unit Tests (80%)  \
    /_______________________\

Unit Tests:
- Testear lógica de negocio
- Mocks para dependencias
- Rápidos

Integration Tests:
- Testear integración con DB
- Testear comunicación entre servicios
- Contract testing (Pact)

E2E Tests:
- Testear flujos completos
- Pocos (lentos y frágiles)
- Críticos para negocio
```

### ¿Dónde poner la lógica de negocio?

**Regla:**

```
Lógica de negocio = EN LOS MICROSERVICIOS

API Gateway:
❌ NO lógica de negocio
✓ Solo routing, auth, rate limiting

Cada microservicio:
✓ Lógica de negocio de su dominio
✓ Validaciones
✓ Reglas de negocio
```

---

## Resumen Final

### La Esencia de los Microservicios

**Microservicios NO son:**
- ❌ Una bala de plata
- ❌ Obligatorios para todos los proyectos
- ❌ Solo sobre tecnología (Docker, K8s)
- ❌ Más fáciles que monolitos

**Microservicios SON:**
- ✅ Un trade-off (complejidad vs escalabilidad)
- ✅ Para problemas de escala y organización
- ✅ Sobre autonomía de equipos
- ✅ Requieren madurez organizacional

### Checklist de Microservicios

```
ANTES de implementar microservicios:

REQUISITOS:
□ ¿Dominio complejo que justifica separación?
□ ¿Equipos múltiples trabajando en paralelo?
□ ¿Necesidad real de escalar partes independientemente?

CAPACIDADES:
□ ¿Experiencia con sistemas distribuidos?
□ ¿Cultura DevOps madura?
□ ¿Infraestructura (Kubernetes, cloud)?
□ ¿Monitoreo/observabilidad sofisticado?

ORGANIZACIÓN:
□ ¿> 10 developers?
□ ¿Equipos autónomos?
□ ¿Presupuesto para infraestructura compleja?

Si NO a la mayoría: Empieza con MONOLITO MODULAR
Si SÍ a la mayoría: Microservicios es viable
```

### Los 10 Mandamientos de Microservicios

```
1. UN SERVICIO, UNA RESPONSABILIDAD
   Cada servicio tiene un propósito claro

2. BASE DE DATOS POR SERVICIO
   No compartir bases de datos

3. COMUNICACIÓN ASÍNCRONA CUANDO SEA POSIBLE
   Reduce acoplamiento temporal

4. DISEÑAR PARA FALLOS
   Circuit breakers, timeouts, retries, fallbacks

5. AUTOMATIZAR TODO
   CI/CD, testing, deployment, monitoring

6. DESCENTRALIZAR
   Datos, decisiones, tecnología

7. OBSERVABILIDAD DESDE DÍA 1
   Logs, metrics, traces

8. VERSIONAR APIS
   Compatibilidad hacia atrás

9. SECURITY EN CADA CAPA
   Auth, encryption, network policies

10. EMPEZAR SIMPLE, CRECER GRADUALMENTE
    No 50 microservicios desde día 1
```

---

## Recursos Adicionales

### Libros Esenciales

```
📚 "Building Microservices" - Sam Newman (2021, 2nd Ed)
   → La biblia de microservicios

📚 "Microservices Patterns" - Chris Richardson (2018)
   → Patrones prácticos (Saga, CQRS, Event Sourcing)

📚 "Release It!" - Michael Nygard (2018, 2nd Ed)
   → Resiliencia, circuit breakers, stability patterns

📚 "Designing Data-Intensive Applications" - Martin Kleppmann
   → Fundamentos de sistemas distribuidos

📚 "Monolith to Microservices" - Sam Newman (2019)
   → Estrategias de migración
```

### Cursos y Videos

```
🎥 "Microservices Architecture" - Udemy (Chris Richardson)
🎥 "Microservices with Spring Boot" - Baeldung
🎥 Conference talks - microservices.io
```

### Herramientas y Frameworks

```
FRAMEWORKS:
- Spring Boot + Spring Cloud (Java)
- Micronaut (Java)
- Quarkus (Java)
- Express (Node.js)
- FastAPI (Python)
- Go Kit (Go)

SERVICE MESH:
- Istio
- Linkerd
- Consul Connect

API GATEWAY:
- Kong
- Spring Cloud Gateway
- Netflix Zuul
- AWS API Gateway

MESSAGE BROKERS:
- Kafka
- RabbitMQ
- AWS SQS/SNS
- Google Pub/Sub

ORCHESTRATION:
- Kubernetes
- Docker Swarm
- Nomad

OBSERVABILITY:
- Prometheus + Grafana (metrics)
- ELK Stack (logs)
- Jaeger/Zipkin (traces)
- Datadog (all-in-one)
```

### Sitios Web y Comunidad

```
🌐 microservices.io - Patrones y mejores prácticas
🌐 12factor.net - Principios de apps cloud-native
🌐 CNCF (Cloud Native Computing Foundation)
```

---

## Conclusión

### El Viaje de Microservicios

> **"Microservicios es un viaje, no un destino"** - Sam Newman

```
NO empieces con 50 microservicios

Empieza con:
1. Monolito bien diseñado (modular)
2. Cuando crezcas, extrae servicios gradualmente
3. Aprende de cada extracción
4. Refina tu approach

Microservicios es una HERRAMIENTA
No una OBLIGACIÓN
```

### La Pregunta Clave

```
Antes de adoptar microservicios, pregúntate:

"¿Tengo un problema que microservicios RESUELVE?"

SI tu problema es:
✓ Escala extrema
✓ Equipos grandes
✓ Despliegues frecuentes
✓ Dominios complejos

ENTONCES microservicios puede ser la respuesta

SI tu problema es:
✗ "Quiero aprender tecnología nueva"
✗ "Todos lo están haciendo"
✗ "Suena cool"

ENTONCES probablemente NO necesitas microservicios
```

### Palabras Finales

**El mejor sistema es el que:**
- ✅ Resuelve tu problema específico
- ✅ Tu equipo puede mantener
- ✅ Escala con tu negocio
- ✅ No es más complejo de lo necesario

**Microservicios puede ayudarte a lograrlo.**
**Pero solo si estás preparado para la complejidad que traen.**

---

**Happy Microservices Architecture!** 🎯

---
**Última actualización:** Diciembre 2025
**Versión:** 1.0
**Autor:** Illescas, Michael Jonathan
**Licencia:** Uso libre para aprendizaje y aplicación profesional
