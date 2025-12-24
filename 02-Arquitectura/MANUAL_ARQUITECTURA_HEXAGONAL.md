# Manual de Arquitectura Hexagonal
## La Guía Definitiva para Principiantes

> **Aprende a diseñar software flexible, testeable y mantenible**
>
> Este manual explica la Arquitectura Hexagonal (también llamada Ports & Adapters) de forma simple y práctica, sin asumir conocimientos previos.

---

## 📚 Índice

1. [¿Qué es la Arquitectura Hexagonal?](#1-qué-es-la-arquitectura-hexagonal)
2. [El Problema que Resuelve](#2-el-problema-que-resuelve)
3. [Conceptos Fundamentales](#3-conceptos-fundamentales)
4. [Estructura en Capas](#4-estructura-en-capas)
5. [Puertos y Adaptadores](#5-puertos-y-adaptadores)
6. [Ejemplo Práctico Paso a Paso](#6-ejemplo-práctico-paso-a-paso)
7. [Reglas de Dependencia](#7-reglas-de-dependencia)
8. [Ventajas y Desventajas](#8-ventajas-y-desventajas)
9. [Cuándo Usar (y Cuándo No)](#9-cuándo-usar-y-cuándo-no)
10. [Errores Comunes](#10-errores-comunes)
11. [Migración Gradual](#11-migración-gradual)
12. [Preguntas Frecuentes](#12-preguntas-frecuentes)

---

## 1. ¿Qué es la Arquitectura Hexagonal?

### Definición Simple

**Arquitectura Hexagonal** es un patrón de diseño que separa la **lógica de negocio** de los **detalles técnicos** (base de datos, frameworks, APIs externas).

```
┌─────────────────────────────────────────────┐
│          AFUERA (detalles técnicos)         │
│   Base de datos | APIs | UI | Frameworks    │
├─────────────────────────────────────────────┤
│          ADENTRO (lógica de negocio)        │
│   Reglas | Cálculos | Validaciones          │
└─────────────────────────────────────────────┘
```

### ¿Por qué se llama "Hexagonal"?

**NO** porque tenga 6 lados obligatoriamente.

El hexágono es solo una **representación visual** que muestra que:
- El centro = tu lógica de negocio (dominio)
- Los lados = interfaces para conectar con el mundo exterior

```
         Base de Datos
              ↓
    UI → [HEXÁGONO] → API Externa
              ↓
         Email Service
```

Puede tener 3, 5, 10 "lados" según necesites. El número no importa.

### Otros Nombres

La Arquitectura Hexagonal también se conoce como:
- **Ports & Adapters** (Puertos y Adaptadores) ← Nombre más descriptivo
- **Onion Architecture** (Arquitectura de Cebolla)
- Parte de **Clean Architecture**

Todos comparten la misma idea: **aislar la lógica de negocio**.

---

## 2. El Problema que Resuelve

### El Código "Tradicional" (Problemático)

Imagina un sistema de biblioteca que registra préstamos de libros.

**Código típico sin arquitectura hexagonal:**

```javascript
// ❌ TODO MEZCLADO
class BookLoanController {
  async lendBook(request, response) {
    // 1. Leer datos de la request HTTP (detalle técnico)
    const userId = request.body.userId;
    const bookId = request.body.bookId;

    // 2. Acceso directo a la base de datos (detalle técnico)
    const db = await mysql.connect('localhost:3306');
    const book = await db.query('SELECT * FROM books WHERE id = ?', bookId);

    // 3. Lógica de negocio mezclada
    if (!book.available) {
      return response.status(400).json({ error: 'Book not available' });
    }

    // 4. Más acceso directo a BD
    await db.query('INSERT INTO loans (user_id, book_id, date) VALUES (?, ?, ?)',
                   [userId, bookId, new Date()]);

    // 5. Enviar email (detalle técnico)
    await sendGridClient.send({
      to: user.email,
      subject: 'Book loaned',
      body: '...'
    });

    // 6. Respuesta HTTP
    return response.status(200).json({ message: 'Success' });
  }
}
```

### Problemas de este Código

```
❌ Lógica de negocio (validar disponibilidad) mezclada con detalles técnicos
❌ Imposible testear sin una base de datos real
❌ Acoplado a HTTP, MySQL, SendGrid
❌ Cambiar de MySQL a PostgreSQL = tocar lógica de negocio
❌ Cambiar de SendGrid a Mailchimp = tocar lógica de negocio
❌ No puedes reutilizar la lógica desde otro lugar (ej: CLI, job programado)
```

### La Solución: Arquitectura Hexagonal

**Separar el "QUÉ" del "CÓMO":**

```
QUÉ (Lógica de Negocio):
- Un usuario puede pedir prestado un libro si está disponible
- Se registra la fecha del préstamo
- Se notifica al usuario

CÓMO (Detalles Técnicos):
- Datos guardados en MySQL (podría ser PostgreSQL, MongoDB, archivos...)
- Notificación vía SendGrid (podría ser Mailchimp, SMS, Slack...)
- Expuesto vía API REST (podría ser GraphQL, gRPC, CLI...)
```

**La lógica de negocio NO debe conocer el CÓMO.**

---

## 3. Conceptos Fundamentales

### 3.1 El Hexágono (Capas)

```
┌────────────────────────────────────────────────┐
│         CAPA EXTERNA (Infrastructure)          │
│   Controllers | Database | Email | HTTP        │
├────────────────────────────────────────────────┤
│         CAPA INTERMEDIA (Application)          │
│   Casos de Uso | Orquestación                  │
├────────────────────────────────────────────────┤
│         CAPA INTERNA (Domain)                  │
│   Entidades | Reglas de Negocio               │
└────────────────────────────────────────────────┘

Regla de Oro:
Dependencias apuntan HACIA ADENTRO
External → Application → Domain
Domain NO conoce External
```

### 3.2 Los 3 Componentes Principales

#### A) **DOMINIO** (Núcleo)

**Qué es:**
- Tu lógica de negocio pura
- Entidades con sus reglas
- Sin dependencias externas

**Ejemplo:**
```java
// Entidad de dominio
class Book {
  private String id;
  private String title;
  private boolean available;

  // Lógica de negocio pura (sin base de datos, sin HTTP)
  public void markAsLoaned() {
    if (!this.available) {
      throw new BookNotAvailableException("Cannot loan unavailable book");
    }
    this.available = false;
  }

  public boolean canBeLoanedTo(User user) {
    return this.available && !user.hasMaxLoans();
  }
}
```

**Características del Dominio:**
```
✓ NO importa clases de frameworks (Spring, Express, etc.)
✓ NO importa clases de BD (JDBC, Mongoose, etc.)
✓ NO sabe que existe HTTP, JSON, APIs
✓ Solo contiene lógica de negocio pura
✓ Fácil de testear (no necesita BD ni servidor)
```

#### B) **PUERTOS** (Interfaces)

**Qué son:**
Contratos (interfaces) que definen cómo el dominio interactúa con el exterior.

**Tipos:**

1. **Puertos de Entrada (Inbound/Primary/Driving Ports)**
   - El mundo exterior llama al dominio
   - Ejemplo: "Préstame este libro"

```java
// Puerto de entrada (interface)
interface LendBookUseCase {
  LoanResult lendBook(String userId, String bookId);
}
```

2. **Puertos de Salida (Outbound/Secondary/Driven Ports)**
   - El dominio necesita algo del exterior
   - Ejemplo: "Guarda este préstamo", "Envía este email"

```java
// Puertos de salida (interfaces)
interface BookRepository {
  Book findById(String id);
  void save(Book book);
}

interface NotificationService {
  void sendLoanConfirmation(User user, Book book);
}
```

**Características de los Puertos:**
```
✓ Son interfaces (contratos)
✓ Definidos EN el dominio
✓ Implementados FUERA del dominio
✓ Permiten cambiar implementaciones fácilmente
```

#### C) **ADAPTADORES** (Implementaciones)

**Qué son:**
Implementaciones concretas de los puertos que conectan con tecnologías específicas.

**Tipos:**

1. **Adaptadores de Entrada (Inbound/Primary)**
   - Reciben requests del exterior y llaman al dominio
   - Ejemplos: REST Controllers, GraphQL Resolvers, CLI Commands, Message Consumers

```java
// Adaptador de entrada: REST Controller
@RestController
class BookController {
  private final LendBookUseCase lendBookUseCase;

  @PostMapping("/loans")
  ResponseEntity<?> lendBook(@RequestBody LoanRequest request) {
    // Adapta HTTP → Dominio
    LoanResult result = lendBookUseCase.lendBook(
      request.getUserId(),
      request.getBookId()
    );

    // Adapta Dominio → HTTP
    return ResponseEntity.ok(toDTO(result));
  }
}
```

2. **Adaptadores de Salida (Outbound/Secondary)**
   - Implementan puertos de salida con tecnologías concretas
   - Ejemplos: Database Repositories, Email Clients, HTTP Clients

```java
// Adaptador de salida: Base de datos
@Repository
class MySQLBookRepository implements BookRepository {
  private final JdbcTemplate jdbc;

  @Override
  public Book findById(String id) {
    // Adapta BD → Dominio
    BookEntity entity = jdbc.queryForObject(
      "SELECT * FROM books WHERE id = ?",
      new Object[]{id},
      BookEntity.class
    );
    return toDomain(entity);
  }

  @Override
  public void save(Book book) {
    // Adapta Dominio → BD
    jdbc.update(
      "UPDATE books SET available = ? WHERE id = ?",
      book.isAvailable(),
      book.getId()
    );
  }
}
```

```java
// Adaptador de salida: Email
class SendGridNotificationService implements NotificationService {
  private final SendGridClient client;

  @Override
  public void sendLoanConfirmation(User user, Book book) {
    // Adapta Dominio → SendGrid API
    client.send(new Email(
      to: user.getEmail(),
      subject: "Book Loaned",
      body: "You loaned: " + book.getTitle()
    ));
  }
}
```

**Características de los Adaptadores:**
```
✓ Implementan interfaces (puertos)
✓ Contienen código técnico (SQL, HTTP, JSON, etc.)
✓ Traducen entre dominio y tecnología externa
✓ Fáciles de reemplazar (cambiar MySQL por PostgreSQL)
```

---

## 4. Estructura en Capas

### Vista Conceptual

```
┌──────────────────────────────────────────────────────┐
│                  INFRASTRUCTURE                       │
│  (Adaptadores: Controllers, Repos, Email, etc.)      │
│                                                       │
│  ┌────────────────────────────────────────────────┐  │
│  │            APPLICATION                          │  │
│  │   (Casos de Uso: orquestación, coordinación)   │  │
│  │                                                 │  │
│  │  ┌──────────────────────────────────────────┐  │  │
│  │  │          DOMAIN                          │  │  │
│  │  │   (Entidades, Reglas de Negocio)        │  │  │
│  │  └──────────────────────────────────────────┘  │  │
│  └────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────┘

Flujo de dependencia: Infrastructure → Application → Domain
Flujo de ejecución:   External request → Application → Domain → Application → External response
```

### Estructura de Directorios Típica

```
src/
├── domain/                          # CAPA 1: Núcleo de negocio
│   ├── model/                       # Entidades
│   │   ├── Book.java
│   │   ├── Loan.java
│   │   └── User.java
│   │
│   ├── service/                     # Servicios de dominio (lógica compleja)
│   │   └── LoanPolicyService.java
│   │
│   └── exception/                   # Excepciones de negocio
│       ├── BookNotAvailableException.java
│       └── MaxLoansExceededException.java
│
├── application/                     # CAPA 2: Casos de uso
│   ├── ports/
│   │   ├── in/                      # Puertos de entrada
│   │   │   ├── LendBookUseCase.java
│   │   │   └── ReturnBookUseCase.java
│   │   │
│   │   └── out/                     # Puertos de salida
│   │       ├── BookRepository.java
│   │       ├── LoanRepository.java
│   │       └── NotificationService.java
│   │
│   └── service/                     # Implementación de casos de uso
│       ├── LendBookService.java
│       └── ReturnBookService.java
│
└── infrastructure/                  # CAPA 3: Detalles técnicos
    ├── web/                        # Adaptadores de entrada: HTTP
    │   ├── controller/
    │   │   ├── BookController.java
    │   │   └── LoanController.java
    │   │
    │   └── dto/                    # DTOs (Data Transfer Objects)
    │       ├── LoanRequest.java
    │       └── LoanResponse.java
    │
    ├── persistence/                # Adaptadores de salida: BD
    │   ├── entity/                 # Entidades JPA/ORM
    │   │   ├── BookEntity.java
    │   │   └── LoanEntity.java
    │   │
    │   ├── repository/             # Implementaciones
    │   │   ├── MySQLBookRepository.java
    │   │   └── MySQLLoanRepository.java
    │   │
    │   └── mapper/                 # Conversores Domain ↔ Entity
    │       ├── BookMapper.java
    │       └── LoanMapper.java
    │
    ├── notification/               # Adaptadores de salida: Email
    │   └── SendGridNotificationService.java
    │
    └── config/                     # Configuración (Spring, etc.)
        └── BeanConfiguration.java
```

---

## 5. Puertos y Adaptadores

### Analogía del Mundo Real: Enchufes

```
Tu Laptop (Dominio)
    ↓
Tiene un puerto USB-C (Puerto)
    ↓
Puedes conectar diferentes adaptadores (Adaptadores):
- Adaptador USB-C a HDMI → TV
- Adaptador USB-C a USB-A → Teclado viejo
- Adaptador USB-C a Ethernet → Internet cableado

Tu laptop NO sabe qué hay del otro lado.
Solo conoce el puerto USB-C.
Los adaptadores traducen entre USB-C y el mundo exterior.
```

### En Software

```
Tu Dominio (Lógica de negocio)
    ↓
Tiene puertos (Interfaces)
    ↓
Puedes conectar diferentes adaptadores:
- Adaptador REST → API HTTP
- Adaptador GraphQL → API GraphQL
- Adaptador CLI → Línea de comandos
- Adaptador Message Queue → Kafka, RabbitMQ

Tu dominio NO sabe qué tecnología se usa.
Solo conoce las interfaces.
Los adaptadores traducen entre dominio y tecnología.
```

### Ejemplo Completo

#### 1. **Dominio** (sin dependencias)

```java
// domain/model/Book.java
public class Book {
  private final String id;
  private final String title;
  private boolean available;

  public Book(String id, String title, boolean available) {
    this.id = id;
    this.title = title;
    this.available = available;
  }

  // Lógica de negocio pura
  public void markAsLoaned() {
    if (!this.available) {
      throw new BookNotAvailableException();
    }
    this.available = false;
  }

  public void markAsReturned() {
    this.available = true;
  }

  // Getters
  public String getId() { return id; }
  public String getTitle() { return title; }
  public boolean isAvailable() { return available; }
}
```

#### 2. **Puertos** (interfaces)

```java
// application/ports/in/LendBookUseCase.java
public interface LendBookUseCase {
  LoanResult execute(LendBookCommand command);
}

// application/ports/in/LendBookCommand.java
public class LendBookCommand {
  private final String userId;
  private final String bookId;

  public LendBookCommand(String userId, String bookId) {
    this.userId = userId;
    this.bookId = bookId;
  }

  public String getUserId() { return userId; }
  public String getBookId() { return bookId; }
}

// application/ports/out/BookRepository.java
public interface BookRepository {
  Optional<Book> findById(String id);
  void save(Book book);
}

// application/ports/out/NotificationService.java
public interface NotificationService {
  void notifyLoan(String userEmail, String bookTitle);
}
```

#### 3. **Aplicación** (casos de uso)

```java
// application/service/LendBookService.java
public class LendBookService implements LendBookUseCase {

  private final BookRepository bookRepository;
  private final NotificationService notificationService;

  public LendBookService(BookRepository bookRepository,
                         NotificationService notificationService) {
    this.bookRepository = bookRepository;
    this.notificationService = notificationService;
  }

  @Override
  public LoanResult execute(LendBookCommand command) {
    // 1. Obtener libro (usa puerto)
    Book book = bookRepository.findById(command.getBookId())
        .orElseThrow(() -> new BookNotFoundException());

    // 2. Ejecutar lógica de dominio
    book.markAsLoaned();  // Puede lanzar BookNotAvailableException

    // 3. Persistir (usa puerto)
    bookRepository.save(book);

    // 4. Notificar (usa puerto)
    notificationService.notifyLoan(command.getUserId(), book.getTitle());

    // 5. Retornar resultado
    return new LoanResult(true, "Book loaned successfully");
  }
}
```

#### 4. **Adaptadores de Salida** (implementaciones)

```java
// infrastructure/persistence/MySQLBookRepository.java
@Repository
public class MySQLBookRepository implements BookRepository {

  private final JdbcTemplate jdbcTemplate;
  private final BookMapper mapper;

  @Override
  public Optional<Book> findById(String id) {
    String sql = "SELECT * FROM books WHERE id = ?";

    try {
      BookEntity entity = jdbcTemplate.queryForObject(sql,
                            new Object[]{id},
                            BookEntity.class);

      // Convertir Entity → Domain
      return Optional.of(mapper.toDomain(entity));
    } catch (EmptyResultDataAccessException e) {
      return Optional.empty();
    }
  }

  @Override
  public void save(Book book) {
    String sql = "UPDATE books SET available = ? WHERE id = ?";
    jdbcTemplate.update(sql, book.isAvailable(), book.getId());
  }
}

// infrastructure/notification/EmailNotificationService.java
public class EmailNotificationService implements NotificationService {

  private final EmailClient emailClient;  // SendGrid, Mailchimp, etc.

  @Override
  public void notifyLoan(String userEmail, String bookTitle) {
    emailClient.sendEmail(
      to: userEmail,
      subject: "Book Loaned",
      body: "You have loaned: " + bookTitle
    );
  }
}
```

#### 5. **Adaptadores de Entrada** (controllers)

```java
// infrastructure/web/controller/BookController.java
@RestController
@RequestMapping("/api/books")
public class BookController {

  private final LendBookUseCase lendBookUseCase;

  public BookController(LendBookUseCase lendBookUseCase) {
    this.lendBookUseCase = lendBookUseCase;
  }

  @PostMapping("/loans")
  public ResponseEntity<LoanResponse> lendBook(@RequestBody LoanRequest request) {
    // Adaptar HTTP → Domain
    LendBookCommand command = new LendBookCommand(
      request.getUserId(),
      request.getBookId()
    );

    // Ejecutar caso de uso
    LoanResult result = lendBookUseCase.execute(command);

    // Adaptar Domain → HTTP
    LoanResponse response = new LoanResponse(
      result.isSuccess(),
      result.getMessage()
    );

    return ResponseEntity.ok(response);
  }
}
```

#### 6. **Configuración** (inyección de dependencias)

```java
// infrastructure/config/BeanConfiguration.java
@Configuration
public class BeanConfiguration {

  @Bean
  public LendBookUseCase lendBookUseCase(
      BookRepository bookRepository,
      NotificationService notificationService) {

    return new LendBookService(bookRepository, notificationService);
  }

  @Bean
  public BookRepository bookRepository(JdbcTemplate jdbcTemplate) {
    return new MySQLBookRepository(jdbcTemplate, new BookMapper());
  }

  @Bean
  public NotificationService notificationService() {
    return new EmailNotificationService(new SendGridClient());
  }
}
```

---

## 6. Ejemplo Práctico Paso a Paso

### Escenario: Sistema de Reservas de Hotel

**Requisito:** Permitir que usuarios reserven habitaciones.

### Paso 1: Identificar la Lógica de Negocio

```
Reglas:
1. Una habitación solo puede reservarse si está disponible
2. Una reserva tiene fecha inicio y fecha fin
3. No se permiten reservas con fechas solapadas
4. Se debe confirmar la reserva por email
```

### Paso 2: Modelar el Dominio

```java
// domain/model/Room.java
public class Room {
  private final String id;
  private final int number;
  private final RoomType type;
  private boolean available;

  public boolean isAvailableForPeriod(LocalDate start, LocalDate end) {
    // Lógica de negocio: verificar disponibilidad
    return this.available;
  }

  public void markAsReserved() {
    if (!this.available) {
      throw new RoomNotAvailableException();
    }
    this.available = false;
  }
}

// domain/model/Reservation.java
public class Reservation {
  private final String id;
  private final String roomId;
  private final String userId;
  private final LocalDate checkIn;
  private final LocalDate checkOut;
  private ReservationStatus status;

  public Reservation(String roomId, String userId,
                     LocalDate checkIn, LocalDate checkOut) {

    // Validación de dominio
    if (checkIn.isAfter(checkOut)) {
      throw new InvalidDateRangeException();
    }

    if (checkIn.isBefore(LocalDate.now())) {
      throw new PastDateException();
    }

    this.id = UUID.randomUUID().toString();
    this.roomId = roomId;
    this.userId = userId;
    this.checkIn = checkIn;
    this.checkOut = checkOut;
    this.status = ReservationStatus.PENDING;
  }

  public void confirm() {
    this.status = ReservationStatus.CONFIRMED;
  }
}
```

### Paso 3: Definir Puertos

```java
// application/ports/in/CreateReservationUseCase.java
public interface CreateReservationUseCase {
  ReservationResult execute(CreateReservationCommand command);
}

// application/ports/out/RoomRepository.java
public interface RoomRepository {
  Optional<Room> findById(String id);
  boolean isAvailableForPeriod(String roomId, LocalDate start, LocalDate end);
  void save(Room room);
}

// application/ports/out/ReservationRepository.java
public interface ReservationRepository {
  void save(Reservation reservation);
}

// application/ports/out/EmailService.java
public interface EmailService {
  void sendReservationConfirmation(String email, Reservation reservation);
}
```

### Paso 4: Implementar Caso de Uso

```java
// application/service/CreateReservationService.java
public class CreateReservationService implements CreateReservationUseCase {

  private final RoomRepository roomRepository;
  private final ReservationRepository reservationRepository;
  private final EmailService emailService;

  @Override
  public ReservationResult execute(CreateReservationCommand command) {
    // 1. Verificar disponibilidad
    Room room = roomRepository.findById(command.getRoomId())
        .orElseThrow(() -> new RoomNotFoundException());

    boolean available = roomRepository.isAvailableForPeriod(
      command.getRoomId(),
      command.getCheckIn(),
      command.getCheckOut()
    );

    if (!available) {
      throw new RoomNotAvailableException();
    }

    // 2. Crear reserva (lógica de dominio)
    Reservation reservation = new Reservation(
      command.getRoomId(),
      command.getUserId(),
      command.getCheckIn(),
      command.getCheckOut()
    );

    // 3. Marcar habitación como reservada
    room.markAsReserved();

    // 4. Persistir
    reservationRepository.save(reservation);
    roomRepository.save(room);

    // 5. Confirmar y notificar
    reservation.confirm();
    emailService.sendReservationConfirmation(
      command.getUserEmail(),
      reservation
    );

    return new ReservationResult(reservation.getId(), true);
  }
}
```

### Paso 5: Implementar Adaptadores

```java
// infrastructure/persistence/JpaRoomRepository.java
@Repository
public class JpaRoomRepository implements RoomRepository {

  @PersistenceContext
  private EntityManager entityManager;

  @Override
  public Optional<Room> findById(String id) {
    RoomEntity entity = entityManager.find(RoomEntity.class, id);
    return Optional.ofNullable(entity)
                   .map(this::toDomain);
  }

  @Override
  public boolean isAvailableForPeriod(String roomId, LocalDate start, LocalDate end) {
    String jpql = """
      SELECT COUNT(r) = 0 FROM ReservationEntity r
      WHERE r.roomId = :roomId
        AND r.status = 'CONFIRMED'
        AND (
          (r.checkIn <= :start AND r.checkOut > :start) OR
          (r.checkIn < :end AND r.checkOut >= :end) OR
          (r.checkIn >= :start AND r.checkOut <= :end)
        )
    """;

    return entityManager.createQuery(jpql, Boolean.class)
        .setParameter("roomId", roomId)
        .setParameter("start", start)
        .setParameter("end", end)
        .getSingleResult();
  }

  @Override
  public void save(Room room) {
    RoomEntity entity = toEntity(room);
    entityManager.merge(entity);
  }

  private Room toDomain(RoomEntity entity) {
    return new Room(entity.getId(), entity.getNumber(),
                    entity.getType(), entity.isAvailable());
  }

  private RoomEntity toEntity(Room room) {
    RoomEntity entity = new RoomEntity();
    entity.setId(room.getId());
    entity.setNumber(room.getNumber());
    entity.setAvailable(room.isAvailable());
    return entity;
  }
}

// infrastructure/email/SmtpEmailService.java
public class SmtpEmailService implements EmailService {

  private final JavaMailSender mailSender;

  @Override
  public void sendReservationConfirmation(String email, Reservation reservation) {
    MimeMessage message = mailSender.createMimeMessage();

    message.setTo(email);
    message.setSubject("Reservation Confirmed");
    message.setText(
      "Your reservation is confirmed!\n" +
      "Room: " + reservation.getRoomId() + "\n" +
      "Check-in: " + reservation.getCheckIn() + "\n" +
      "Check-out: " + reservation.getCheckOut()
    );

    mailSender.send(message);
  }
}
```

### Paso 6: Exponer vía API REST

```java
// infrastructure/web/controller/ReservationController.java
@RestController
@RequestMapping("/api/reservations")
public class ReservationController {

  private final CreateReservationUseCase createReservation;

  @PostMapping
  public ResponseEntity<ReservationResponse> createReservation(
      @RequestBody ReservationRequest request) {

    // Adaptar HTTP → Domain
    CreateReservationCommand command = new CreateReservationCommand(
      request.getRoomId(),
      request.getUserId(),
      request.getUserEmail(),
      request.getCheckIn(),
      request.getCheckOut()
    );

    try {
      // Ejecutar caso de uso
      ReservationResult result = createReservation.execute(command);

      // Adaptar Domain → HTTP
      return ResponseEntity.status(HttpStatus.CREATED)
          .body(new ReservationResponse(result.getReservationId()));

    } catch (RoomNotFoundException e) {
      return ResponseEntity.notFound().build();
    } catch (RoomNotAvailableException e) {
      return ResponseEntity.status(HttpStatus.CONFLICT)
          .body(new ReservationResponse("Room not available"));
    }
  }
}
```

### Beneficios Obtenidos

```
✅ Lógica de negocio (Room, Reservation) totalmente independiente
✅ Fácil testear sin BD ni email real:
   - Mock RoomRepository
   - Mock EmailService
   - Test unitario de CreateReservationService

✅ Cambiar de MySQL a PostgreSQL:
   - Solo cambiar JpaRoomRepository
   - Lógica de negocio intacta

✅ Cambiar de SMTP a SendGrid:
   - Solo cambiar SmtpEmailService
   - Lógica de negocio intacta

✅ Exponer también vía GraphQL o CLI:
   - Crear nuevo adaptador (GraphQLResolver, CliCommand)
   - Reutilizar mismo CreateReservationUseCase
```

---

## 7. Reglas de Dependencia

### La Regla de Oro

```
Las dependencias SIEMPRE apuntan hacia adentro:
Infrastructure → Application → Domain

NUNCA al revés:
Domain NO puede depender de Application
Domain NO puede depender de Infrastructure
Application NO puede depender de Infrastructure
```

### Ejemplo Visual

```
✅ CORRECTO:
Controller (Infra)
    ↓ depende de
UseCase (Application)
    ↓ depende de
Entity (Domain)

❌ INCORRECTO:
Entity (Domain)
    ↓ depende de
JPA Annotations (Infrastructure)  ← ¡NO!
```

### Cómo Verificar

**Pregunta:**
Si elimino la carpeta `infrastructure`, ¿el código de `domain` y `application` compila?

**Respuesta esperada:** SÍ

```java
// ❌ MAL: Domain depende de Infrastructure
// domain/model/Book.java
@Entity  // ← Anotación de JPA (infrastructure)
public class Book {
  @Id  // ← JPA
  private String id;
}

// ✅ BIEN: Domain sin dependencias
// domain/model/Book.java
public class Book {
  private String id;  // Sin anotaciones
}

// infrastructure/persistence/entity/BookEntity.java
@Entity  // ← JPA en infrastructure
public class BookEntity {
  @Id
  private String id;
}
```

### Inversión de Dependencias

**Problema:** ¿Cómo hace el dominio para llamar a infrastructure si no puede depender de él?

**Solución:** Inversión de dependencias (Dependency Inversion Principle)

```
Dominio define la interface (puerto)
Infrastructure implementa esa interface (adaptador)
```

**Ejemplo:**

```java
// domain/ports/out/BookRepository.java (DOMINIO define)
public interface BookRepository {
  Book findById(String id);
}

// infrastructure/persistence/JpaBookRepository.java (INFRASTRUCTURE implementa)
public class JpaBookRepository implements BookRepository {
  @Override
  public Book findById(String id) {
    // Lógica con JPA
  }
}

// application/service/LendBookService.java (APPLICATION usa)
public class LendBookService {
  private final BookRepository repository;  // Depende de interface

  public void lendBook(String bookId) {
    Book book = repository.findById(bookId);  // Llama a la interface
  }
}
```

**Flujo:**
```
1. Application dice: "Necesito un BookRepository"
2. Infrastructure proporciona: "Aquí está JpaBookRepository"
3. Application usa la interface sin saber que es JPA
```

Esto es **inyección de dependencias** (Dependency Injection).

---

## 8. Ventajas y Desventajas

### ✅ Ventajas

#### 1. **Testabilidad**
```java
// Test unitario SIN base de datos, SIN frameworks
@Test
void shouldLendBookWhenAvailable() {
  // Mocks (no necesitas BD real)
  BookRepository mockRepo = mock(BookRepository.class);
  EmailService mockEmail = mock(EmailService.class);

  when(mockRepo.findById("123"))
    .thenReturn(Optional.of(new Book("123", "Title", true)));

  // Test del caso de uso
  LendBookService service = new LendBookService(mockRepo, mockEmail);
  LoanResult result = service.execute(new LendBookCommand("user1", "123"));

  assertTrue(result.isSuccess());
  verify(mockRepo).save(any(Book.class));
  verify(mockEmail).notifyLoan(anyString(), anyString());
}
```

#### 2. **Flexibilidad Tecnológica**
```
Hoy: MySQL
Mañana: PostgreSQL
→ Solo cambias JpaBookRepository

Hoy: Email SMTP
Mañana: SendGrid API
→ Solo cambias EmailService

Lógica de negocio: INTACTA
```

#### 3. **Mantenibilidad**
```
Bugs de negocio → Arreglas en domain/
Bugs de BD → Arreglas en infrastructure/persistence/
Bugs de API → Arreglas en infrastructure/web/

Separación clara de responsabilidades
```

#### 4. **Reutilización**
```
Mismo caso de uso (LendBookUseCase) usado por:
- REST API
- GraphQL API
- Línea de comandos (CLI)
- Job programado (Scheduler)
- Mensajes (Kafka consumer)
```

#### 5. **Evolución Gradual**
```
Puedes empezar con:
- SQLite local
- Email fake (logs)
- HTTP simple

Y evolucionar a:
- PostgreSQL en AWS RDS
- SendGrid
- GraphQL + REST

Sin tocar la lógica de negocio
```

### ❌ Desventajas

#### 1. **Más Código Inicial**
```
Sin arquitectura hexagonal:
- 1 archivo: BookController.java (todo mezclado)

Con arquitectura hexagonal:
- domain/model/Book.java
- application/ports/in/LendBookUseCase.java
- application/ports/out/BookRepository.java
- application/service/LendBookService.java
- infrastructure/web/controller/BookController.java
- infrastructure/persistence/JpaBookRepository.java
- infrastructure/persistence/entity/BookEntity.java
- infrastructure/persistence/mapper/BookMapper.java

~8 archivos vs 1
```

**Cuándo es problema:**
MVPs ultra-rápidos, prototipos descartables

**Cuándo NO es problema:**
Aplicaciones que durarán años

#### 2. **Curva de Aprendizaje**
```
Conceptos a aprender:
- Puertos y adaptadores
- Inversión de dependencias
- Capas y sus responsabilidades
- Inyección de dependencias
```

**Mitigación:**
Documentación, ejemplos, pair programming

#### 3. **Over-engineering en Proyectos Simples**
```
CRUD simple de 3 tablas:
→ Hexagonal puede ser overkill
→ Un simple MVC puede bastar
```

**Regla:**
Si tu "lógica de negocio" es solo `INSERT`, `UPDATE`, `DELETE`...
...probablemente no necesitas arquitectura hexagonal.

#### 4. **Mapeo Entre Capas**
```
HTTP Request → DTO → Command → Domain Entity → DB Entity

Mucho mapeo:
- toDTO()
- toCommand()
- toDomain()
- toEntity()
```

**Solución:**
- Librerías de mapeo (MapStruct, ModelMapper)
- O aceptar el trade-off (claridad vs boilerplate)

---

## 9. Cuándo Usar (y Cuándo No)

### ✅ USA Arquitectura Hexagonal Cuando:

```
✓ Aplicación de larga duración (> 2 años)
✓ Lógica de negocio compleja (no solo CRUD)
✓ Múltiples puntos de entrada (REST + GraphQL + CLI + jobs)
✓ Necesitas alta testabilidad
✓ El equipo tiene experiencia con patrones arquitectónicos
✓ Anticipas cambios tecnológicos (migrar BD, cambiar APIs)
✓ La mantenibilidad es crítica
✓ Equipos medianos a grandes (> 3 developers)
```

### ❌ NO Uses Arquitectura Hexagonal Cuando:

```
✗ MVP de 2 semanas
✗ Prototipo descartable
✗ CRUD ultra-simple (3 tablas, sin lógica)
✗ Aplicación de uso único (script)
✗ Equipo sin experiencia (aprendiendo a programar)
✗ Presión extrema de tiempo
✗ No hay lógica de negocio real
```

### 🤔 Evalúa Caso por Caso:

```
¿Tu aplicación tiene reglas de negocio complejas?
  → Sí: considera hexagonal
  → No: MVC simple puede bastar

¿Necesitas testear sin dependencias externas?
  → Sí: hexagonal ayuda mucho
  → No: tal vez no lo necesites

¿La aplicación crecerá y evolucionará?
  → Sí: inversión inicial vale la pena
  → No: KISS (Keep It Simple)

¿El equipo entiende estos conceptos?
  → Sí: adelante
  → No: invierte en formación primero
```

### Decisión Práctica

```markdown
# Checklist de Decisión

Responde SÍ/NO:

1. [ ] ¿La aplicación tendrá vida útil > 1 año?
2. [ ] ¿Hay lógica de negocio más allá de CRUD básico?
3. [ ] ¿El equipo tiene > 2 developers?
4. [ ] ¿Es crítico testear sin BD/APIs reales?
5. [ ] ¿Podrías necesitar cambiar tecnologías (BD, frameworks)?
6. [ ] ¿Necesitas múltiples puntos de entrada (API, CLI, etc.)?

**Resultado:**
- 5-6 SÍ: Definitivamente usa hexagonal
- 3-4 SÍ: Probablemente beneficioso
- 1-2 SÍ: Evalúa alternativas más simples
- 0 SÍ: No uses hexagonal, KISS
```

---

## 10. Errores Comunes

### Error #1: Anotaciones de Framework en Dominio

```java
// ❌ MAL
// domain/model/Book.java
@Entity  // JPA (framework)
public class Book {
  @Id
  @GeneratedValue
  private Long id;  // Tipo de BD (Long)
}

// ✅ BIEN
// domain/model/Book.java
public class Book {
  private String id;  // Tipo de dominio (String, UUID, etc.)
}

// infrastructure/persistence/entity/BookEntity.java
@Entity
public class BookEntity {
  @Id
  @GeneratedValue
  private Long id;
}
```

**Por qué es error:**
El dominio NO debe conocer JPA, Hibernate, u otros frameworks.

---

### Error #2: Lógica de Negocio en Controllers

```java
// ❌ MAL
@RestController
public class BookController {
  @PostMapping("/loans")
  public ResponseEntity<?> lendBook(@RequestBody LoanRequest request) {
    Book book = bookRepo.findById(request.getBookId());

    // ❌ Lógica de negocio en el controller
    if (!book.isAvailable()) {
      return ResponseEntity.badRequest().body("Not available");
    }

    book.setAvailable(false);
    bookRepo.save(book);

    return ResponseEntity.ok("Success");
  }
}

// ✅ BIEN
@RestController
public class BookController {
  private final LendBookUseCase lendBookUseCase;

  @PostMapping("/loans")
  public ResponseEntity<?> lendBook(@RequestBody LoanRequest request) {
    // Controller solo adapta, NO contiene lógica de negocio
    LendBookCommand command = toCommand(request);
    LoanResult result = lendBookUseCase.execute(command);
    return ResponseEntity.ok(toResponse(result));
  }
}
```

---

### Error #3: Dominio Dependiendo de Puertos de Salida

```java
// ❌ MAL
// domain/model/Book.java
public class Book {
  private BookRepository repository;  // ❌ Dominio no debe tener repository

  public void save() {
    repository.save(this);  // ❌ Entidad no debe saber persistirse
  }
}

// ✅ BIEN
// domain/model/Book.java
public class Book {
  // Sin repository, sin métodos save()
  // Solo lógica de negocio pura

  public void markAsLoaned() {
    if (!this.available) {
      throw new BookNotAvailableException();
    }
    this.available = false;
  }
}

// application/service/LendBookService.java
public class LendBookService {
  private BookRepository repository;

  public void lendBook(String bookId) {
    Book book = repository.findById(bookId);
    book.markAsLoaned();  // Lógica de dominio
    repository.save(book);  // Persistencia en application
  }
}
```

---

### Error #4: Entidades Anémicas

```java
// ❌ MAL: Entidad anémica (solo getters/setters, sin lógica)
public class Book {
  private String id;
  private boolean available;

  public String getId() { return id; }
  public void setId(String id) { this.id = id; }
  public boolean isAvailable() { return available; }
  public void setAvailable(boolean available) { this.available = available; }
}

// ❌ Lógica termina en el servicio
public class LendBookService {
  public void lendBook(String bookId) {
    Book book = repository.findById(bookId);

    // ❌ Lógica fuera de la entidad
    if (!book.isAvailable()) {
      throw new BookNotAvailableException();
    }
    book.setAvailable(false);

    repository.save(book);
  }
}

// ✅ BIEN: Entidad rica (con comportamiento)
public class Book {
  private String id;
  private boolean available;

  // Comportamiento de dominio
  public void markAsLoaned() {
    if (!this.available) {
      throw new BookNotAvailableException();
    }
    this.available = false;
  }

  public String getId() { return id; }
  public boolean isAvailable() { return available; }
}

// ✅ Servicio delgado (orquestación)
public class LendBookService {
  public void lendBook(String bookId) {
    Book book = repository.findById(bookId);
    book.markAsLoaned();  // ✅ Entidad se encarga de su lógica
    repository.save(book);
  }
}
```

---

### Error #5: Demasiadas Abstracciones Innecesarias

```java
// ❌ OVERKILL
interface BookFinder {
  Book find(String id);
}

interface BookPersister {
  void persist(Book book);
}

interface BookAvailabilityChecker {
  boolean check(Book book);
}

// ✅ EQUILIBRADO
interface BookRepository {
  Book findById(String id);
  void save(Book book);
}
```

**Regla:**
No sobre-abstraigas. Si una interface tiene un solo método y una sola implementación que nunca cambiará, tal vez no necesitas la interface.

---

### Error #6: Compartir DTOs entre Capas

```java
// ❌ MAL: Usar mismo DTO en todas las capas
// shared/dto/BookDTO.java
public class BookDTO {
  private String id;
  private String title;
  private boolean available;
}

// infrastructure/web/controller/
uses BookDTO

// application/service/
uses BookDTO

// domain/model/
uses BookDTO  // ❌ Dominio acoplado al DTO

// ✅ BIEN: Cada capa tiene sus propios objetos
// infrastructure/web/dto/BookResponse.java
public class BookResponse {
  private String id;
  private String title;
}

// application/ports/in/LendBookCommand.java
public class LendBookCommand {
  private String bookId;
  private String userId;
}

// domain/model/Book.java
public class Book {
  private String id;
  private String title;
  private boolean available;
}
```

---

## 11. Migración Gradual

### ¿Tienes una Aplicación Existente Sin Arquitectura?

**No reescribas todo.** Migra gradualmente módulo por módulo.

### Estrategia de Migración

#### Paso 1: Identificar Módulos

```
Aplicación actual:
- 20 controllers mezclados
- Lógica en controllers
- Acceso directo a BD

Identificar dominios:
1. Usuarios
2. Productos
3. Pedidos
4. Facturación
```

#### Paso 2: Elegir un Módulo Piloto

```
Criterios:
✓ Módulo pequeño (bajo riesgo)
✓ Lógica de negocio clara
✓ Fácil de testear
✓ No tan crítico (si falla, no es catastrófico)

Ejemplo: Módulo de "Notificaciones"
```

#### Paso 3: Refactorizar el Módulo Piloto

```
Antes:
src/
└── controllers/
    └── NotificationController.java  (todo mezclado)

Después:
src/
├── notification/  (nuevo módulo hexagonal)
│   ├── domain/
│   ├── application/
│   └── infrastructure/
└── controllers/  (otros controllers sin cambios)
    ├── UserController.java
    └── OrderController.java
```

#### Paso 4: Validar

```
✓ Tests pasan
✓ Funcionalidad intacta
✓ Equipo entiende la nueva estructura
```

#### Paso 5: Repetir con Otros Módulos

```
Migrar de a uno:
1. ✅ Notificaciones (semana 1)
2. ✅ Productos (semana 3)
3. ✅ Pedidos (semana 5)
4. 🔄 Usuarios (en progreso)
5. ⏳ Facturación (pendiente)
```

### Convivencia de Estilos

```
Es OK tener:
src/
├── notification/     (hexagonal)
├── product/          (hexagonal)
└── legacy/           (estilo viejo)
    └── controllers/

Gradualmente, legacy/ se vacía
```

**No necesitas 100% pureza arquitectónica desde día 1.**

---

## 12. Preguntas Frecuentes

### ¿Arquitectura Hexagonal = Clean Architecture?

**Casi lo mismo, con matices:**

```
Arquitectura Hexagonal (Alistair Cockburn, 2005):
- Énfasis en puertos y adaptadores
- Visualización como hexágono

Clean Architecture (Robert C. Martin, 2012):
- Énfasis en capas concéntricas (dominio al centro)
- Incluye más capas (Entities, Use Cases, Interface Adapters, Frameworks)

Similitud: Ambas buscan aislar el dominio de detalles técnicos
Diferencia: Clean Architecture es más granular
```

**En la práctica:** Son intercambiables.

---

### ¿Es lo mismo que DDD (Domain-Driven Design)?

**No, pero se complementan:**

```
DDD:
- Metodología de diseño
- Ubiquitous Language, Bounded Contexts, Aggregates
- Cómo modelar el dominio

Arquitectura Hexagonal:
- Patrón arquitectónico
- Cómo estructurar el código
- Separación de capas

Juntos:
DDD define QUÉ va en el dominio
Hexagonal define DÓNDE va el código
```

---

### ¿Necesito usar un framework de inyección de dependencias?

**No es obligatorio, pero ayuda:**

```
Sin framework:
public static void main(String[] args) {
  BookRepository repo = new MySQLBookRepository(new JdbcTemplate(...));
  EmailService email = new SmtpEmailService(new JavaMailSender(...));
  LendBookUseCase useCase = new LendBookService(repo, email);
  BookController controller = new BookController(useCase);
}

Con framework (Spring):
@Configuration
public class AppConfig {
  @Bean
  public LendBookUseCase lendBookUseCase(BookRepository repo, EmailService email) {
    return new LendBookService(repo, email);
  }
}
```

**Recomendación:**
Frameworks simplifican inyección, úsalos si el proyecto lo justifica.

---

### ¿Puedo tener múltiples adaptadores para el mismo puerto?

**Sí, ese es el objetivo:**

```
interface EmailService {  // Puerto
  void send(String to, String subject, String body);
}

// Adaptadores:
class SmtpEmailService implements EmailService { ... }
class SendGridEmailService implements EmailService { ... }
class FakeEmailService implements EmailService { ... }  // Para tests
class LogEmailService implements EmailService { ... }   // Para dev

// Eliges cuál usar:
@Configuration
public class AppConfig {
  @Bean
  public EmailService emailService() {
    if (env.isProd()) return new SendGridEmailService();
    if (env.isDev()) return new LogEmailService();
    return new FakeEmailService();
  }
}
```

---

### ¿Dónde van las transacciones de BD?

**En la capa de aplicación (use cases):**

```java
// ✅ BIEN
@Service
@Transactional  // En application service
public class LendBookService implements LendBookUseCase {

  public LoanResult execute(LendBookCommand command) {
    Book book = bookRepository.findById(command.getBookId());
    book.markAsLoaned();
    bookRepository.save(book);

    Loan loan = new Loan(command.getUserId(), command.getBookId());
    loanRepository.save(loan);

    // Todo en una transacción
    return LoanResult.success();
  }
}
```

**Regla:**
Transacciones = coordinación entre operaciones = responsabilidad de application layer.

---

### ¿Puedo usar herencia entre entidades de dominio y entidades JPA?

**NO recomendado:**

```java
// ❌ MAL
// domain/model/Book.java
public class Book {
  private String id;
  private String title;
}

// infrastructure/persistence/entity/BookEntity.java
@Entity
public class BookEntity extends Book {  // ❌ Herencia
  @Id
  private Long id;
}
```

**Por qué:**
- Acopla dominio con JPA
- Confusión entre modelo de dominio y modelo de persistencia
- Dificulta mantener separación limpia

**✅ Usa composición + mapeo:**

```java
// domain/model/Book.java
public class Book {
  private String id;
  private String title;
}

// infrastructure/persistence/entity/BookEntity.java
@Entity
public class BookEntity {
  @Id
  private Long id;
  private String title;
}

// Mapper
Book toDomain(BookEntity entity) {
  return new Book(entity.getId().toString(), entity.getTitle());
}

BookEntity toEntity(Book domain) {
  BookEntity entity = new BookEntity();
  entity.setId(Long.parseLong(domain.getId()));
  entity.setTitle(domain.getTitle());
  return entity;
}
```

---

### ¿Cómo manejo errores globales (excepciones)?

**Dos enfoques:**

**1. Excepciones de dominio + Handler global:**

```java
// domain/exception/BookNotFoundException.java
public class BookNotFoundException extends DomainException {
  public BookNotFoundException(String id) {
    super("Book not found: " + id);
  }
}

// infrastructure/web/GlobalExceptionHandler.java
@ControllerAdvice
public class GlobalExceptionHandler {

  @ExceptionHandler(BookNotFoundException.class)
  public ResponseEntity<?> handleBookNotFound(BookNotFoundException e) {
    return ResponseEntity.status(HttpStatus.NOT_FOUND)
        .body(new ErrorResponse(e.getMessage()));
  }

  @ExceptionHandler(BookNotAvailableException.class)
  public ResponseEntity<?> handleBookNotAvailable(BookNotAvailableException e) {
    return ResponseEntity.status(HttpStatus.CONFLICT)
        .body(new ErrorResponse(e.getMessage()));
  }
}
```

**2. Result pattern (sin excepciones):**

```java
// application/ports/in/LoanResult.java
public class LoanResult {
  private final boolean success;
  private final String errorMessage;
  private final String loanId;

  public static LoanResult success(String loanId) {
    return new LoanResult(true, null, loanId);
  }

  public static LoanResult failure(String error) {
    return new LoanResult(false, error, null);
  }
}

// application/service/LendBookService.java
public LoanResult execute(LendBookCommand command) {
  Optional<Book> book = bookRepository.findById(command.getBookId());

  if (book.isEmpty()) {
    return LoanResult.failure("Book not found");
  }

  if (!book.get().isAvailable()) {
    return LoanResult.failure("Book not available");
  }

  // ... lógica de préstamo

  return LoanResult.success(loanId);
}
```

**Recomendación:**
Usa excepciones para casos excepcionales (errores inesperados).
Usa Result pattern para casos de negocio esperados (validaciones).

---

## Resumen Final

### Los 5 Pilares de Arquitectura Hexagonal

```
1. DOMINIO AL CENTRO
   - Lógica de negocio pura
   - Sin dependencias externas
   - Fácil de testear

2. PUERTOS (Interfaces)
   - Contratos entre capas
   - Definidos en dominio/application
   - Implementados en infrastructure

3. ADAPTADORES (Implementaciones)
   - Traducen entre dominio y tecnología
   - Fáciles de reemplazar
   - Contienen detalles técnicos

4. INVERSIÓN DE DEPENDENCIAS
   - Dependencias apuntan hacia adentro
   - Domain NO conoce Infrastructure
   - Infrastructure implementa interfaces del Domain

5. SEPARACIÓN DE RESPONSABILIDADES
   - Domain: lógica de negocio
   - Application: orquestación
   - Infrastructure: detalles técnicos
```

---

### Checklist de Implementación

```
Al implementar una nueva feature:

Dominio:
□ ¿Las entidades tienen lógica de negocio (no solo getters/setters)?
□ ¿Las entidades NO tienen anotaciones de frameworks?
□ ¿Las validaciones de negocio están en el dominio?

Puertos:
□ ¿Los puertos están definidos como interfaces?
□ ¿Los puertos están en application/ports/?
□ ¿Los nombres describen la intención (UseCase, Repository)?

Aplicación:
□ ¿Los casos de uso orquestan sin lógica de negocio compleja?
□ ¿Los servicios dependen de interfaces, no de implementaciones?

Adaptadores:
□ ¿Los adaptadores implementan interfaces (puertos)?
□ ¿Los adaptadores están en infrastructure/?
□ ¿El mapeo Domain ↔ External está aislado?

Dependencias:
□ ¿Domain NO importa nada de infrastructure?
□ ¿Application NO importa nada de infrastructure?
□ ¿Infrastructure SÍ importa de application y domain?

Testing:
□ ¿Puedes testear el dominio sin mocks?
□ ¿Puedes testear application con mocks simples?
□ ¿Los tests NO requieren BD/API real?
```

---

## Recursos Adicionales

### Libros Recomendados

```
- "Hexagonal Architecture Explained" - Juan Manuel Garrido de Paz
- "Clean Architecture" - Robert C. Martin
- "Domain-Driven Design" - Eric Evans
- "Get Your Hands Dirty on Clean Architecture" - Tom Hombergs
```

### Artículos

```
- Alistair Cockburn (inventor):
  https://alistair.cockburn.us/hexagonal-architecture/

- Netflix Tech Blog (ejemplos reales)
- Martin Fowler's Blog
```

### Ejemplos de Código

```
GitHub:
- Busca "hexagonal architecture example"
- Busca "clean architecture template"
- Filtra por tu lenguaje favorito
```

---

## Conclusión

**Arquitectura Hexagonal NO es:**
- ❌ Una bala de plata
- ❌ Obligatoria en todos los proyectos
- ❌ Complicada si entiendes los fundamentos

**Arquitectura Hexagonal ES:**
- ✅ Una forma de aislar lógica de negocio
- ✅ Una inversión en mantenibilidad
- ✅ Especialmente valiosa en proyectos complejos y de larga duración
- ✅ Facilitadora de tests y cambios tecnológicos

**Pregunta clave antes de usarla:**

> ¿Mi aplicación tiene lógica de negocio que justifica esta separación?

Si la respuesta es **SÍ** → Adelante con hexagonal.
Si la respuesta es **NO** → Un MVC simple puede ser suficiente.

---

**¿Dudas? ¿Algo no quedó claro?**

Este manual es un recurso vivo. Si encuentras algo confuso, mejóralo.
La arquitectura debe servir al equipo, no al revés.

**Happy Hexagonal Coding!** 🎯

---

**Última actualización:** Diciembre 2025
**Versión:** 1.0
**Licencia:** Uso libre para aprendizaje y aplicación profesional
