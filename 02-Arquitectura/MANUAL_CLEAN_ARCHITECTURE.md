# Manual de Clean Architecture
## La Guía Definitiva para Principiantes

> **Aprende a diseñar software limpio, independiente y mantenible**
>
> Este manual explica Clean Architecture (Arquitectura Limpia) de forma simple y práctica, sin asumir conocimientos previos.

---

## 📚 Índice

1. [¿Qué es Clean Architecture?](#1-qué-es-clean-architecture)
2. [El Problema que Resuelve](#2-el-problema-que-resuelve)
3. [Los Principios Fundamentales](#3-los-principios-fundamentales)
4. [Las 4 Capas de Clean Architecture](#4-las-4-capas-de-clean-architecture)
5. [La Regla de Dependencia](#5-la-regla-de-dependencia)
6. [El Flujo de Control](#6-el-flujo-de-control)
7. [Ejemplo Práctico Completo](#7-ejemplo-práctico-completo)
8. [Comparación con Otros Patrones](#8-comparación-con-otros-patrones)
9. [Ventajas y Desventajas](#9-ventajas-y-desventajas)
10. [Cuándo Usar (y Cuándo No)](#10-cuándo-usar-y-cuándo-no)
11. [Errores Comunes](#11-errores-comunes)
12. [Implementación Práctica](#12-implementación-práctica)
13. [Preguntas Frecuentes](#13-preguntas-frecuentes)

---

## 1. ¿Qué es Clean Architecture?

### Definición Simple

**Clean Architecture** es un patrón de diseño arquitectónico propuesto por **Robert C. Martin (Uncle Bob)** que organiza el código en **capas concéntricas**, donde las capas internas contienen la lógica de negocio y las capas externas contienen los detalles técnicos.

```
┌─────────────────────────────────────────────┐
│    FRAMEWORKS & DRIVERS (capa externa)      │  ← Base de datos, UI, Web
│  ┌───────────────────────────────────────┐  │
│  │  INTERFACE ADAPTERS (adaptadores)     │  │  ← Controllers, Presenters
│  │  ┌─────────────────────────────────┐  │  │
│  │  │  USE CASES (casos de uso)       │  │  │  ← Lógica de aplicación
│  │  │  ┌───────────────────────────┐  │  │  │
│  │  │  │  ENTITIES (entidades)     │  │  │  │  ← Lógica de negocio
│  │  │  │        CORE               │  │  │  │
│  │  │  └───────────────────────────┘  │  │  │
│  │  └─────────────────────────────────┘  │  │
│  └───────────────────────────────────────┘  │
└─────────────────────────────────────────────┘

        Dependencias apuntan →→→ hacia el centro
```

### Objetivo Principal

**Independencia:**

```
Tu lógica de negocio debe ser independiente de:
❌ Frameworks (Spring, Django, Rails)
❌ Base de datos (MySQL, PostgreSQL, MongoDB)
❌ UI (Web, Mobile, Desktop)
❌ APIs externas (servicios de terceros)
❌ Cualquier detalle técnico
```

### El Círculo Concéntrico

Imagina el sistema como círculos concéntricos, como las capas de una cebolla:

```
    ┌────────────────────────────┐
    │   Frameworks & Drivers     │  ← Lo más externo (cambia fácilmente)
    │  ┌──────────────────────┐  │
    │  │  Interface Adapters  │  │  ← Traducción entre capas
    │  │  ┌────────────────┐  │  │
    │  │  │   Use Cases    │  │  │  ← Reglas de aplicación
    │  │  │  ┌──────────┐  │  │  │
    │  │  │  │ Entities │  │  │  │  ← Reglas de negocio (núcleo)
    │  │  │  └──────────┘  │  │  │
    │  │  └────────────────┘  │  │
    │  └──────────────────────┘  │
    └────────────────────────────┘

Mientras más al centro, más estable y menos cambia
Mientras más afuera, más volátil y más cambia
```

---

## 2. El Problema que Resuelve

### Software Tradicional (Acoplado)

Imagina un sistema de gestión de biblioteca sin Clean Architecture:

```javascript
// ❌ TODO ACOPLADO Y MEZCLADO
class BookController {
  constructor() {
    // Acoplado a Express (framework)
    this.app = express();

    // Acoplado a MySQL (base de datos)
    this.db = mysql.createConnection({
      host: 'localhost',
      user: 'root',
      database: 'library'
    });
  }

  async borrowBook(req, res) {
    // 1. Lógica de presentación (HTTP)
    const userId = req.body.userId;
    const bookId = req.body.bookId;

    // 2. Acceso directo a BD (SQL específico)
    const book = await this.db.query(
      'SELECT * FROM books WHERE id = ?',
      [bookId]
    );

    // 3. Lógica de negocio mezclada
    if (book.status !== 'AVAILABLE') {
      return res.status(400).json({ error: 'Book not available' });
    }

    // 4. Más SQL directo
    await this.db.query(
      'UPDATE books SET status = ? WHERE id = ?',
      ['BORROWED', bookId]
    );

    await this.db.query(
      'INSERT INTO loans (user_id, book_id, date) VALUES (?, ?, ?)',
      [userId, bookId, new Date()]
    );

    // 5. Acoplado a servicio de email específico
    await sendgrid.send({
      to: user.email,
      subject: 'Book borrowed',
      body: 'You borrowed: ' + book.title
    });

    // 6. Respuesta HTTP
    res.json({ success: true });
  }
}
```

### Problemas

```
❌ Imposible testear sin Express + MySQL + SendGrid
❌ Cambiar de MySQL a PostgreSQL = reescribir todo
❌ Cambiar de Express a Fastify = reescribir todo
❌ Cambiar de SendGrid a Mailchimp = modificar lógica de negocio
❌ No puedes reutilizar la lógica en CLI, mobile app, etc.
❌ La lógica de negocio está dispersa y oculta
❌ Imposible entender qué hace sin leer todo el código técnico
```

### La Solución: Clean Architecture

```
Separar en capas concéntricas:

CENTRO (Entities):
- Reglas de negocio fundamentales
- Ejemplo: "Un libro tiene un estado: disponible, prestado, reservado"

CAPA 2 (Use Cases):
- Reglas de aplicación
- Ejemplo: "Para prestar un libro, debe estar disponible y el usuario no debe tener multas"

CAPA 3 (Interface Adapters):
- Traducción entre use cases y mundo exterior
- Ejemplo: Controllers que convierten HTTP a casos de uso

CAPA 4 (Frameworks & Drivers):
- Detalles técnicos
- Ejemplo: Express, MySQL, SendGrid

Cada capa solo conoce la capa inmediatamente interior
```

---

## 3. Los Principios Fundamentales

### Principio 1: La Regla de Dependencia

```
Las dependencias del código fuente SIEMPRE apuntan hacia adentro

Frameworks & Drivers → Interface Adapters → Use Cases → Entities

                    ❌ NUNCA al revés

Entities NO conocen Use Cases
Use Cases NO conocen Interface Adapters
Interface Adapters NO conocen Frameworks
```

**Visualización:**

```
┌──────────────┐
│  Framework   │  ──→  depende de  ──→  ┌─────────────┐
└──────────────┘                        │  Entities   │
                                        │   (core)    │
┌──────────────┐                        └─────────────┘
│  Controller  │  ──→  depende de  ──→        ↑
└──────────────┘                              │
                                         NO depende
                                              │
                                        ┌─────────────┐
                                        │  Framework  │
                                        └─────────────┘
```

### Principio 2: Separación de Concerns (Responsabilidades)

Cada capa tiene UNA responsabilidad:

```
Entities:
✓ Lógica de negocio pura y fundamental
✓ Reglas que existirían incluso sin aplicación
✗ NO sabe de casos de uso específicos
✗ NO sabe de base de datos, UI, frameworks

Use Cases:
✓ Lógica de aplicación específica
✓ Orquestación de entidades
✗ NO sabe de HTTP, SQL, JSON
✗ NO sabe de frameworks

Interface Adapters:
✓ Convertir datos entre use cases y mundo exterior
✓ Controllers, Presenters, Gateways
✗ NO contiene lógica de negocio

Frameworks & Drivers:
✓ Detalles técnicos
✓ BD, UI, dispositivos externos
✗ Mínima lógica posible
```

### Principio 3: Independencia de Frameworks

```
Los frameworks son herramientas, NO arquitectura

Tu arquitectura NO debe estar construida alrededor de:
❌ Spring Boot
❌ Django
❌ Rails
❌ React
❌ Angular

En cambio:
✓ Los frameworks son plugins en la capa externa
✓ Puedes reemplazarlos sin tocar el core
```

### Principio 4: Testeable

```
La lógica de negocio debe ser testeable sin:
❌ Base de datos
❌ Servidor web
❌ UI
❌ Cualquier elemento externo

Test de Entities: sin mocks
Test de Use Cases: solo mocks de interfaces
```

### Principio 5: Independencia de UI

```
La misma lógica de negocio puede ser usada por:
✓ Web app
✓ Mobile app
✓ Desktop app
✓ CLI
✓ API REST
✓ GraphQL
✓ gRPC

Sin cambiar el core
```

### Principio 6: Independencia de Base de Datos

```
La lógica de negocio no sabe si usas:
❌ MySQL
❌ PostgreSQL
❌ MongoDB
❌ Files
❌ Cloud storage

Puedes cambiar de BD sin tocar el core
```

---

## 4. Las 4 Capas de Clean Architecture

### Capa 1: ENTITIES (Entidades) - El Núcleo

**Qué es:**
- El círculo más interno
- Reglas de negocio de la empresa/dominio
- Objetos con métodos que implementan reglas fundamentales

**Características:**

```
✓ Pura lógica de negocio
✓ Sin dependencias externas (ni siquiera de otras capas)
✓ Reutilizable en cualquier aplicación de la empresa
✓ Lo más estable (raramente cambia)
✓ Lo más importante del sistema
```

**Ejemplo:**

```java
// Entities/Book.java
public class Book {
  private final String isbn;
  private final String title;
  private final String author;
  private BookStatus status;

  public Book(String isbn, String title, String author) {
    if (isbn == null || isbn.isEmpty()) {
      throw new InvalidBookException("ISBN cannot be empty");
    }
    this.isbn = isbn;
    this.title = title;
    this.author = author;
    this.status = BookStatus.AVAILABLE;
  }

  // Regla de negocio: Un libro solo puede prestarse si está disponible
  public void borrow() {
    if (this.status != BookStatus.AVAILABLE) {
      throw new BookNotAvailableException(
        "Cannot borrow a book that is " + this.status
      );
    }
    this.status = BookStatus.BORROWED;
  }

  // Regla de negocio: Un libro prestado puede renovarse si no está reservado
  public boolean canBeRenewed() {
    return this.status == BookStatus.BORROWED;
  }

  public void returnBook() {
    if (this.status != BookStatus.BORROWED) {
      throw new IllegalStateException("Book is not borrowed");
    }
    this.status = BookStatus.AVAILABLE;
  }

  // Getters
  public String getIsbn() { return isbn; }
  public String getTitle() { return title; }
  public String getAuthor() { return author; }
  public BookStatus getStatus() { return status; }
}

enum BookStatus {
  AVAILABLE, BORROWED, RESERVED, LOST
}
```

**Pregunta clave:** ¿Esta regla existiría incluso sin una aplicación informática?
- ✅ "Un libro prestado no puede prestarse de nuevo" → SÍ (regla fundamental)
- ❌ "Guardar en MySQL" → NO (detalle de implementación)

---

### Capa 2: USE CASES (Casos de Uso) - Lógica de Aplicación

**Qué es:**
- Reglas de negocio específicas de la aplicación
- Orquesta el flujo de datos hacia/desde las entidades
- Dirige las entidades para lograr objetivos

**Características:**

```
✓ Lógica de aplicación (no de negocio fundamental)
✓ Orquestación de múltiples entidades
✓ Define interfaces (puertos) que implementará la capa externa
✓ Independiente de HTTP, BD, UI
✓ Depende solo de Entities
```

**Ejemplo:**

```java
// UseCases/BorrowBook/BorrowBookUseCase.java
public interface BorrowBookUseCase {
  BorrowBookResponse execute(BorrowBookRequest request);
}

// UseCases/BorrowBook/BorrowBookRequest.java
public class BorrowBookRequest {
  private final String userId;
  private final String isbn;

  public BorrowBookRequest(String userId, String isbn) {
    this.userId = userId;
    this.isbn = isbn;
  }

  public String getUserId() { return userId; }
  public String getIsbn() { return isbn; }
}

// UseCases/BorrowBook/BorrowBookResponse.java
public class BorrowBookResponse {
  private final boolean success;
  private final String loanId;
  private final String message;

  public static BorrowBookResponse success(String loanId) {
    return new BorrowBookResponse(true, loanId, "Book borrowed successfully");
  }

  public static BorrowBookResponse failure(String message) {
    return new BorrowBookResponse(false, null, message);
  }

  // Constructor y getters
}

// UseCases/BorrowBook/BorrowBookInteractor.java
public class BorrowBookInteractor implements BorrowBookUseCase {

  // Interfaces definidas en esta capa (puertos)
  private final BookRepository bookRepository;
  private final UserRepository userRepository;
  private final LoanRepository loanRepository;
  private final NotificationGateway notificationGateway;

  public BorrowBookInteractor(
      BookRepository bookRepository,
      UserRepository userRepository,
      LoanRepository loanRepository,
      NotificationGateway notificationGateway) {
    this.bookRepository = bookRepository;
    this.userRepository = userRepository;
    this.loanRepository = loanRepository;
    this.notificationGateway = notificationGateway;
  }

  @Override
  public BorrowBookResponse execute(BorrowBookRequest request) {
    // 1. Obtener entidades
    Book book = bookRepository.findByIsbn(request.getIsbn())
        .orElseThrow(() -> new BookNotFoundException(request.getIsbn()));

    User user = userRepository.findById(request.getUserId())
        .orElseThrow(() -> new UserNotFoundException(request.getUserId()));

    // 2. Validar reglas de aplicación
    if (user.hasOverdueLoan()) {
      return BorrowBookResponse.failure("User has overdue loans");
    }

    if (user.getLoanCount() >= 5) {
      return BorrowBookResponse.failure("User has reached loan limit");
    }

    // 3. Ejecutar lógica de entidad
    try {
      book.borrow();  // Regla de negocio en la entidad
    } catch (BookNotAvailableException e) {
      return BorrowBookResponse.failure(e.getMessage());
    }

    // 4. Crear loan
    Loan loan = new Loan(user.getId(), book.getIsbn());

    // 5. Persistir cambios
    bookRepository.save(book);
    loanRepository.save(loan);

    // 6. Notificar
    notificationGateway.notifyBookBorrowed(user.getEmail(), book.getTitle());

    return BorrowBookResponse.success(loan.getId());
  }
}
```

**Interfaces (Puertos) definidos en Use Cases:**

```java
// UseCases/Ports/BookRepository.java
public interface BookRepository {
  Optional<Book> findByIsbn(String isbn);
  void save(Book book);
  List<Book> findAll();
}

// UseCases/Ports/NotificationGateway.java
public interface NotificationGateway {
  void notifyBookBorrowed(String userEmail, String bookTitle);
  void notifyBookOverdue(String userEmail, String bookTitle);
}
```

---

### Capa 3: INTERFACE ADAPTERS (Adaptadores de Interfaz)

**Qué es:**
- Convierte datos entre el formato de use cases y el formato externo
- Controllers, Presenters, Gateways
- Adaptadores que conectan el mundo exterior con los use cases

**Características:**

```
✓ Convierte formatos
✓ Controllers (entrada)
✓ Presenters (salida)
✓ Gateways (acceso a datos)
✓ Depende de Use Cases
✓ NO contiene lógica de negocio
```

**Ejemplo - Controller (Adaptador de Entrada):**

```java
// InterfaceAdapters/Controllers/BookController.java
@RestController
@RequestMapping("/api/books")
public class BookController {

  private final BorrowBookUseCase borrowBookUseCase;

  public BookController(BorrowBookUseCase borrowBookUseCase) {
    this.borrowBookUseCase = borrowBookUseCase;
  }

  @PostMapping("/borrow")
  public ResponseEntity<BorrowBookResponseDTO> borrowBook(
      @RequestBody BorrowBookRequestDTO requestDTO) {

    // Convertir HTTP DTO → Use Case Request
    BorrowBookRequest request = new BorrowBookRequest(
      requestDTO.getUserId(),
      requestDTO.getIsbn()
    );

    // Ejecutar caso de uso
    BorrowBookResponse response = borrowBookUseCase.execute(request);

    // Convertir Use Case Response → HTTP DTO
    BorrowBookResponseDTO responseDTO = new BorrowBookResponseDTO(
      response.isSuccess(),
      response.getLoanId(),
      response.getMessage()
    );

    // Determinar código HTTP
    HttpStatus status = response.isSuccess()
        ? HttpStatus.OK
        : HttpStatus.BAD_REQUEST;

    return ResponseEntity.status(status).body(responseDTO);
  }
}

// DTOs (Data Transfer Objects)
class BorrowBookRequestDTO {
  private String userId;
  private String isbn;
  // Getters y setters
}

class BorrowBookResponseDTO {
  private boolean success;
  private String loanId;
  private String message;
  // Constructor, getters y setters
}
```

**Ejemplo - Gateway (Adaptador de Salida):**

```java
// InterfaceAdapters/Gateways/DatabaseBookRepository.java
@Repository
public class DatabaseBookRepository implements BookRepository {

  private final JpaBookRepository jpaRepository;
  private final BookMapper mapper;

  @Override
  public Optional<Book> findByIsbn(String isbn) {
    // Convertir BD Entity → Domain Entity
    return jpaRepository.findByIsbn(isbn)
        .map(mapper::toDomain);
  }

  @Override
  public void save(Book book) {
    // Convertir Domain Entity → BD Entity
    BookEntity entity = mapper.toEntity(book);
    jpaRepository.save(entity);
  }
}

// InterfaceAdapters/Gateways/EmailNotificationGateway.java
public class EmailNotificationGateway implements NotificationGateway {

  private final EmailService emailService;  // Librería externa

  @Override
  public void notifyBookBorrowed(String userEmail, String bookTitle) {
    // Adaptar Domain → Email externo
    EmailMessage message = new EmailMessage();
    message.setTo(userEmail);
    message.setSubject("Book Borrowed");
    message.setBody("You have borrowed: " + bookTitle);

    emailService.send(message);
  }
}
```

---

### Capa 4: FRAMEWORKS & DRIVERS (Frameworks y Controladores)

**Qué es:**
- La capa más externa
- Detalles técnicos concretos
- Base de datos, frameworks web, dispositivos

**Características:**

```
✓ Código específico de frameworks
✓ Configuración
✓ Mínima lógica
✓ Fácilmente reemplazable
✓ Lo más volátil (cambia frecuentemente)
```

**Ejemplo:**

```java
// FrameworksAndDrivers/Database/JpaBookRepository.java
public interface JpaBookRepository extends JpaRepository<BookEntity, Long> {
  Optional<BookEntity> findByIsbn(String isbn);
}

// FrameworksAndDrivers/Database/Entities/BookEntity.java
@Entity
@Table(name = "books")
public class BookEntity {
  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  private Long id;

  @Column(unique = true, nullable = false)
  private String isbn;

  @Column(nullable = false)
  private String title;

  @Column(nullable = false)
  private String author;

  @Enumerated(EnumType.STRING)
  private BookStatus status;

  // Getters y setters
}

// FrameworksAndDrivers/Configuration/BeanConfiguration.java
@Configuration
public class BeanConfiguration {

  @Bean
  public BorrowBookUseCase borrowBookUseCase(
      BookRepository bookRepository,
      UserRepository userRepository,
      LoanRepository loanRepository,
      NotificationGateway notificationGateway) {

    return new BorrowBookInteractor(
      bookRepository,
      userRepository,
      loanRepository,
      notificationGateway
    );
  }

  @Bean
  public BookRepository bookRepository(
      JpaBookRepository jpaRepository,
      BookMapper mapper) {
    return new DatabaseBookRepository(jpaRepository, mapper);
  }
}
```

---

## 5. La Regla de Dependencia

### Visualización

```
┌──────────────────────────────────────────┐
│  Frameworks & Drivers (DB, UI, Web)     │  ← Depende de →
│  ┌────────────────────────────────────┐  │
│  │  Interface Adapters (Controllers)  │  │  ← Depende de →
│  │  ┌──────────────────────────────┐  │  │
│  │  │  Use Cases (Interactors)     │  │  │  ← Depende de →
│  │  │  ┌────────────────────────┐  │  │  │
│  │  │  │  Entities (Core)       │  │  │  │  ← NO depende de nada
│  │  │  └────────────────────────┘  │  │  │
│  │  └──────────────────────────────┘  │  │
│  └────────────────────────────────────┘  │
└──────────────────────────────────────────┘

Cada capa solo puede depender de la capa inmediatamente interior
```

### Verificación Práctica

**Pregunta de validación:**

```
Si elimino la carpeta FrameworksAndDrivers:
¿El código de InterfaceAdapters compila? → SÍ (solo usa interfaces)

Si elimino InterfaceAdapters:
¿El código de UseCases compila? → SÍ (define sus interfaces)

Si elimino UseCases:
¿El código de Entities compila? → SÍ (no depende de nada)
```

### Inversión de Dependencias

**¿Cómo hace Use Case para llamar a la BD si no puede depender de ella?**

**Respuesta:** Dependency Inversion Principle

```java
// Use Case define la interface
interface BookRepository {
  Optional<Book> findByIsbn(String isbn);
}

// Use Case depende de la interface
class BorrowBookInteractor {
  private final BookRepository repository;  // Interface, no implementación

  public void execute() {
    Book book = repository.findByIsbn("123");  // Llama a interface
  }
}

// Frameworks & Drivers implementa la interface
class DatabaseBookRepository implements BookRepository {
  @Override
  public Optional<Book> findByIsbn(String isbn) {
    // Código específico de BD
  }
}

// Configuración inyecta la implementación
BorrowBookUseCase useCase = new BorrowBookInteractor(
  new DatabaseBookRepository()  // Se inyecta la implementación concreta
);
```

**Dirección de dependencia:**

```
DatabaseBookRepository → implements → BookRepository ← depende de ← BorrowBookInteractor
(Frameworks)                          (Use Cases)                   (Use Cases)

DatabaseBookRepository depende de BookRepository
BorrowBookInteractor depende de BookRepository
DatabaseBookRepository NO depende de BorrowBookInteractor

✅ La flecha de dependencia apunta hacia adentro
```

---

## 6. El Flujo de Control

### Flujo de Ejecución vs Flujo de Dependencias

```
FLUJO DE EJECUCIÓN (runtime):
User → Controller → Use Case → Repository → Database

FLUJO DE DEPENDENCIAS (código):
Database → Repository ← Use Case ← Controller ← User

Son OPUESTOS (gracias a inversión de dependencias)
```

### Ejemplo Completo de Flujo

```
1. Usuario hace click en "Prestar libro" (UI)
   ↓
2. HTTP POST /api/books/borrow (Framework)
   ↓
3. BookController recibe request (Interface Adapter)
   - Convierte HTTP DTO → BorrowBookRequest
   ↓
4. BorrowBookInteractor.execute(request) (Use Case)
   - Llama bookRepository.findByIsbn() (interface)
   ↓
5. DatabaseBookRepository (Interface Adapter)
   - Convierte domain → entity
   - Llama jpaRepository.findByIsbn()
   ↓
6. JpaBookRepository (Framework & Driver)
   - Query SQL a la base de datos
   ↓
7. Database retorna datos
   ↓
8. DatabaseBookRepository convierte entity → domain
   ↓
9. BorrowBookInteractor recibe Book (Entity)
   - Ejecuta book.borrow() (lógica de negocio)
   - Guarda cambios via repository.save()
   - Notifica via notificationGateway.notify()
   - Retorna BorrowBookResponse
   ↓
10. BookController recibe response (Interface Adapter)
    - Convierte BorrowBookResponse → HTTP DTO
    ↓
11. HTTP Response 200 OK (Framework)
    ↓
12. Usuario ve "Libro prestado exitosamente" (UI)
```

**Cruce de fronteras:**

```
Layer Boundary (frontera de capa):
- Los datos que cruzan fronteras son simples DTOs
- NO objetos de dominio con comportamiento
- NO objetos de BD con anotaciones

Ejemplo:
Controller → Use Case: usa BorrowBookRequest (simple DTO)
Use Case → Repository: usa Book (entidad de dominio)
Repository → DB: usa BookEntity (objeto JPA)
```

---

## 7. Ejemplo Práctico Completo

### Escenario: Sistema de Cursos Online

**Requisito:** Permitir a estudiantes inscribirse en cursos.

**Reglas de negocio:**
1. Un curso tiene capacidad limitada
2. Un estudiante no puede inscribirse dos veces en el mismo curso
3. Se debe notificar al estudiante tras inscripción exitosa
4. Se debe registrar la fecha de inscripción

---

### Paso 1: Entities (Núcleo)

```java
// Entities/Course.java
public class Course {
  private final String id;
  private final String title;
  private final int capacity;
  private final List<String> enrolledStudentIds;

  public Course(String id, String title, int capacity) {
    this.id = id;
    this.title = title;
    this.capacity = capacity;
    this.enrolledStudentIds = new ArrayList<>();
  }

  // Regla de negocio: Verificar si hay cupo
  public boolean hasAvailableSlots() {
    return enrolledStudentIds.size() < capacity;
  }

  // Regla de negocio: Verificar si estudiante ya está inscrito
  public boolean isStudentEnrolled(String studentId) {
    return enrolledStudentIds.contains(studentId);
  }

  // Regla de negocio: Inscribir estudiante
  public void enrollStudent(String studentId) {
    if (!hasAvailableSlots()) {
      throw new CourseFullException("Course is full");
    }

    if (isStudentEnrolled(studentId)) {
      throw new AlreadyEnrolledException("Student already enrolled");
    }

    enrolledStudentIds.add(studentId);
  }

  // Getters
  public String getId() { return id; }
  public String getTitle() { return title; }
  public int getCapacity() { return capacity; }
  public int getEnrolledCount() { return enrolledStudentIds.size(); }
}

// Entities/Student.java
public class Student {
  private final String id;
  private final String name;
  private final String email;

  public Student(String id, String name, String email) {
    if (email == null || !email.contains("@")) {
      throw new InvalidEmailException("Invalid email");
    }
    this.id = id;
    this.name = name;
    this.email = email;
  }

  public String getId() { return id; }
  public String getName() { return name; }
  public String getEmail() { return email; }
}

// Entities/Enrollment.java
public class Enrollment {
  private final String id;
  private final String studentId;
  private final String courseId;
  private final LocalDateTime enrolledAt;

  public Enrollment(String studentId, String courseId) {
    this.id = UUID.randomUUID().toString();
    this.studentId = studentId;
    this.courseId = courseId;
    this.enrolledAt = LocalDateTime.now();
  }

  public String getId() { return id; }
  public String getStudentId() { return studentId; }
  public String getCourseId() { return courseId; }
  public LocalDateTime getEnrolledAt() { return enrolledAt; }
}
```

---

### Paso 2: Use Cases (Casos de Uso)

```java
// UseCases/EnrollStudent/EnrollStudentUseCase.java
public interface EnrollStudentUseCase {
  EnrollStudentOutput execute(EnrollStudentInput input);
}

// UseCases/EnrollStudent/EnrollStudentInput.java
public class EnrollStudentInput {
  private final String studentId;
  private final String courseId;

  public EnrollStudentInput(String studentId, String courseId) {
    this.studentId = studentId;
    this.courseId = courseId;
  }

  public String getStudentId() { return studentId; }
  public String getCourseId() { return courseId; }
}

// UseCases/EnrollStudent/EnrollStudentOutput.java
public class EnrollStudentOutput {
  private final boolean success;
  private final String enrollmentId;
  private final String message;

  public static EnrollStudentOutput success(String enrollmentId) {
    return new EnrollStudentOutput(true, enrollmentId, "Enrolled successfully");
  }

  public static EnrollStudentOutput failure(String message) {
    return new EnrollStudentOutput(false, null, message);
  }

  private EnrollStudentOutput(boolean success, String enrollmentId, String message) {
    this.success = success;
    this.enrollmentId = enrollmentId;
    this.message = message;
  }

  public boolean isSuccess() { return success; }
  public String getEnrollmentId() { return enrollmentId; }
  public String getMessage() { return message; }
}

// UseCases/EnrollStudent/EnrollStudentInteractor.java
public class EnrollStudentInteractor implements EnrollStudentUseCase {

  // Puertos (interfaces) que serán implementadas en capas externas
  private final CourseRepository courseRepository;
  private final StudentRepository studentRepository;
  private final EnrollmentRepository enrollmentRepository;
  private final NotificationService notificationService;

  public EnrollStudentInteractor(
      CourseRepository courseRepository,
      StudentRepository studentRepository,
      EnrollmentRepository enrollmentRepository,
      NotificationService notificationService) {
    this.courseRepository = courseRepository;
    this.studentRepository = studentRepository;
    this.enrollmentRepository = enrollmentRepository;
    this.notificationService = notificationService;
  }

  @Override
  public EnrollStudentOutput execute(EnrollStudentInput input) {
    // 1. Obtener entidades
    Course course = courseRepository.findById(input.getCourseId())
        .orElseThrow(() -> new CourseNotFoundException(input.getCourseId()));

    Student student = studentRepository.findById(input.getStudentId())
        .orElseThrow(() -> new StudentNotFoundException(input.getStudentId()));

    // 2. Ejecutar lógica de dominio (reglas de negocio)
    try {
      course.enrollStudent(student.getId());
    } catch (CourseFullException | AlreadyEnrolledException e) {
      return EnrollStudentOutput.failure(e.getMessage());
    }

    // 3. Crear enrollment
    Enrollment enrollment = new Enrollment(student.getId(), course.getId());

    // 4. Persistir cambios
    courseRepository.save(course);
    enrollmentRepository.save(enrollment);

    // 5. Notificar
    notificationService.notifyEnrollment(
      student.getEmail(),
      student.getName(),
      course.getTitle()
    );

    return EnrollStudentOutput.success(enrollment.getId());
  }
}

// UseCases/Ports/CourseRepository.java (INTERFACE)
public interface CourseRepository {
  Optional<Course> findById(String id);
  List<Course> findAll();
  void save(Course course);
}

// UseCases/Ports/NotificationService.java (INTERFACE)
public interface NotificationService {
  void notifyEnrollment(String email, String studentName, String courseTitle);
}
```

---

### Paso 3: Interface Adapters

```java
// InterfaceAdapters/Controllers/EnrollmentController.java
@RestController
@RequestMapping("/api/enrollments")
public class EnrollmentController {

  private final EnrollStudentUseCase enrollStudentUseCase;

  public EnrollmentController(EnrollStudentUseCase enrollStudentUseCase) {
    this.enrollStudentUseCase = enrollStudentUseCase;
  }

  @PostMapping
  public ResponseEntity<EnrollmentResponseDTO> enrollStudent(
      @RequestBody EnrollmentRequestDTO requestDTO) {

    // Convertir HTTP DTO → Use Case Input
    EnrollStudentInput input = new EnrollStudentInput(
      requestDTO.getStudentId(),
      requestDTO.getCourseId()
    );

    // Ejecutar caso de uso
    EnrollStudentOutput output = enrollStudentUseCase.execute(input);

    // Convertir Use Case Output → HTTP DTO
    EnrollmentResponseDTO responseDTO = new EnrollmentResponseDTO(
      output.isSuccess(),
      output.getEnrollmentId(),
      output.getMessage()
    );

    // Determinar código HTTP según resultado
    HttpStatus status = output.isSuccess()
        ? HttpStatus.CREATED
        : HttpStatus.BAD_REQUEST;

    return ResponseEntity.status(status).body(responseDTO);
  }
}

// InterfaceAdapters/Gateways/JpaCourseRepository.java
@Component
public class JpaCourseRepository implements CourseRepository {

  private final SpringDataCourseRepository springRepo;

  @Override
  public Optional<Course> findById(String id) {
    return springRepo.findById(Long.parseLong(id))
        .map(this::toDomain);
  }

  @Override
  public void save(Course course) {
    CourseEntity entity = toEntity(course);
    springRepo.save(entity);
  }

  // Mapeo Entity ↔ Domain
  private Course toDomain(CourseEntity entity) {
    Course course = new Course(
      entity.getId().toString(),
      entity.getTitle(),
      entity.getCapacity()
    );

    // Reconstruir estado de inscripciones
    entity.getEnrollments().forEach(enrollment -> {
      course.enrollStudent(enrollment.getStudentId());
    });

    return course;
  }

  private CourseEntity toEntity(Course course) {
    CourseEntity entity = new CourseEntity();
    entity.setId(Long.parseLong(course.getId()));
    entity.setTitle(course.getTitle());
    entity.setCapacity(course.getCapacity());
    return entity;
  }
}

// InterfaceAdapters/Gateways/EmailNotificationService.java
@Component
public class EmailNotificationService implements NotificationService {

  private final JavaMailSender mailSender;

  @Override
  public void notifyEnrollment(String email, String studentName, String courseTitle) {
    SimpleMailMessage message = new SimpleMailMessage();
    message.setTo(email);
    message.setSubject("Enrollment Confirmation");
    message.setText(
      String.format("Hello %s,\n\nYou have been enrolled in: %s",
        studentName, courseTitle)
    );

    mailSender.send(message);
  }
}
```

---

### Paso 4: Frameworks & Drivers

```java
// FrameworksAndDrivers/Database/SpringDataCourseRepository.java
public interface SpringDataCourseRepository extends JpaRepository<CourseEntity, Long> {
}

// FrameworksAndDrivers/Database/Entities/CourseEntity.java
@Entity
@Table(name = "courses")
public class CourseEntity {
  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  private Long id;

  @Column(nullable = false)
  private String title;

  @Column(nullable = false)
  private Integer capacity;

  @OneToMany(mappedBy = "course", cascade = CascadeType.ALL)
  private List<EnrollmentEntity> enrollments = new ArrayList<>();

  // Getters y setters
}

// FrameworksAndDrivers/Configuration/UseCaseConfiguration.java
@Configuration
public class UseCaseConfiguration {

  @Bean
  public EnrollStudentUseCase enrollStudentUseCase(
      CourseRepository courseRepository,
      StudentRepository studentRepository,
      EnrollmentRepository enrollmentRepository,
      NotificationService notificationService) {

    return new EnrollStudentInteractor(
      courseRepository,
      studentRepository,
      enrollmentRepository,
      notificationService
    );
  }
}
```

---

### Estructura de Directorios Completa

```
src/
├── Entities/                           # Capa 1: Núcleo de negocio
│   ├── Course.java
│   ├── Student.java
│   ├── Enrollment.java
│   └── exceptions/
│       ├── CourseFullException.java
│       └── AlreadyEnrolledException.java
│
├── UseCases/                           # Capa 2: Casos de uso
│   ├── EnrollStudent/
│   │   ├── EnrollStudentUseCase.java
│   │   ├── EnrollStudentInput.java
│   │   ├── EnrollStudentOutput.java
│   │   └── EnrollStudentInteractor.java
│   │
│   └── Ports/                          # Interfaces (puertos)
│       ├── CourseRepository.java
│       ├── StudentRepository.java
│       ├── EnrollmentRepository.java
│       └── NotificationService.java
│
├── InterfaceAdapters/                  # Capa 3: Adaptadores
│   ├── Controllers/
│   │   ├── EnrollmentController.java
│   │   └── DTOs/
│   │       ├── EnrollmentRequestDTO.java
│   │       └── EnrollmentResponseDTO.java
│   │
│   └── Gateways/
│       ├── JpaCourseRepository.java
│       ├── JpaStudentRepository.java
│       ├── JpaEnrollmentRepository.java
│       └── EmailNotificationService.java
│
└── FrameworksAndDrivers/               # Capa 4: Detalles técnicos
    ├── Database/
    │   ├── SpringDataCourseRepository.java
    │   └── Entities/
    │       ├── CourseEntity.java
    │       ├── StudentEntity.java
    │       └── EnrollmentEntity.java
    │
    └── Configuration/
        ├── UseCaseConfiguration.java
        ├── DatabaseConfiguration.java
        └── WebConfiguration.java
```

---

## 8. Comparación con Otros Patrones

### Clean Architecture vs Hexagonal Architecture

```
SIMILITUDES:
✓ Ambas separan lógica de negocio de detalles técnicos
✓ Dependencias apuntan hacia el núcleo
✓ Usan interfaces (puertos)
✓ Testeable sin frameworks

DIFERENCIAS:

Clean Architecture:
- 4 capas explícitas (Entities, Use Cases, Adapters, Frameworks)
- Énfasis en "círculos concéntricos"
- Más prescriptiva en la organización

Hexagonal Architecture:
- 2 áreas (Core + Infrastructure)
- Énfasis en "puertos y adaptadores"
- Más flexible en la organización interna

RELACIÓN:
Clean Architecture es una evolución/refinamiento de Hexagonal Architecture
```

### Clean Architecture vs Layered Architecture (MVC tradicional)

```
LAYERED ARCHITECTURE (N-Tier):
┌─────────────────┐
│  Presentation   │  (UI, Controllers)
├─────────────────┤
│    Business     │  (Services, Logic)
├─────────────────┤
│  Persistence    │  (DAO, Repositories)
├─────────────────┤
│    Database     │  (BD)
└─────────────────┘

Problemas:
❌ Dependencias van hacia abajo (Business depende de Persistence)
❌ Difícil testear sin BD
❌ Acoplado a frameworks

CLEAN ARCHITECTURE:
        ┌───────────┐
        │  Entities │  (Core)
        └─────▲─────┘
              │
        ┌─────┴─────┐
        │ Use Cases │
        └─────▲─────┘
              │
    ┌─────────┴─────────┐
    │ Interface Adapters│
    └─────────▲─────────┘
              │
      ┌───────┴───────┐
      │   Frameworks   │
      └───────────────┘

Ventajas:
✓ Dependencias van hacia adentro (Frameworks depende de Core)
✓ Fácil testear sin BD
✓ Independiente de frameworks
```

### Clean Architecture vs DDD (Domain-Driven Design)

```
NO son lo mismo, se COMPLEMENTAN:

DDD:
- Metodología de diseño
- Cómo modelar el dominio (Aggregates, Value Objects, Bounded Contexts)
- QUÉ va en el dominio

Clean Architecture:
- Patrón arquitectónico
- Cómo organizar el código (capas, dependencias)
- DÓNDE va el código

Juntos:
- DDD define el contenido del dominio
- Clean Architecture define la estructura del proyecto
- Muchas implementaciones de DDD usan Clean Architecture
```

---

## 9. Ventajas y Desventajas

### ✅ Ventajas

#### 1. **Independencia de Frameworks**

```
Puedes cambiar:
- Spring Boot → Quarkus
- Express → Fastify
- Django → Flask

Sin tocar el núcleo de negocio
```

**Ejemplo:**
```java
// Entities y Use Cases son los mismos
// Solo cambias la capa de Frameworks & Drivers

Antes (Spring Boot):
@RestController
class CourseController { ... }

Después (Quarkus):
@Path("/courses")
class CourseResource { ... }

// Entities y UseCases: sin cambios
```

#### 2. **Independencia de Base de Datos**

```
Puedes migrar:
- MySQL → PostgreSQL
- MongoDB → DynamoDB
- SQL → NoSQL

Sin tocar lógica de negocio
```

#### 3. **Testabilidad Superior**

```java
// Test de Entities: sin mocks
@Test
void shouldEnrollStudentWhenCourseHasCapacity() {
  Course course = new Course("1", "Math", 30);

  course.enrollStudent("student-1");

  assertEquals(1, course.getEnrolledCount());
}

// Test de Use Cases: mocks simples
@Test
void shouldEnrollStudentSuccessfully() {
  CourseRepository mockRepo = mock(CourseRepository.class);
  when(mockRepo.findById("1"))
    .thenReturn(Optional.of(new Course("1", "Math", 30)));

  EnrollStudentInteractor useCase = new EnrollStudentInteractor(mockRepo, ...);
  EnrollStudentOutput output = useCase.execute(new EnrollStudentInput("s1", "1"));

  assertTrue(output.isSuccess());
}
```

#### 4. **Mantenibilidad a Largo Plazo**

```
Cambios están aislados:
- Cambio de BD → Solo capa de Frameworks
- Cambio de UI → Solo capa de Controllers
- Cambio de regla de negocio → Solo Entities o Use Cases

No efecto dominó
```

#### 5. **Reutilización**

```
Mismos Use Cases usados por:
✓ REST API
✓ GraphQL API
✓ gRPC
✓ CLI
✓ Jobs programados
✓ Tests automatizados
```

#### 6. **Evolución Gradual**

```
Puedes empezar con tecnología simple y evolucionar:

Fase 1: SQLite local + Email fake
Fase 2: PostgreSQL + Email real
Fase 3: PostgreSQL en AWS + SendGrid
Fase 4: DynamoDB + AWS SES

Core de negocio: intacto en todas las fases
```

---

### ❌ Desventajas

#### 1. **Complejidad Inicial**

```
CRUD simple sin Clean Architecture:
- 1 archivo: BookController.java (50 líneas)

Mismo CRUD con Clean Architecture:
- Entities/Book.java
- UseCases/CreateBook/CreateBookUseCase.java
- UseCases/CreateBook/CreateBookInput.java
- UseCases/CreateBook/CreateBookOutput.java
- UseCases/CreateBook/CreateBookInteractor.java
- UseCases/Ports/BookRepository.java
- InterfaceAdapters/Controllers/BookController.java
- InterfaceAdapters/Gateways/JpaBookRepository.java
- FrameworksAndDrivers/Database/Entities/BookEntity.java

9 archivos vs 1
```

**Cuándo es problema:**
- Prototipos rápidos
- Hackathons
- MVPs desechables

#### 2. **Curva de Aprendizaje**

```
Conceptos a aprender:
- 4 capas y sus responsabilidades
- Regla de dependencia
- Inversión de dependencias
- Inyección de dependencias
- Puertos e interfaces
- Boundary crossing
```

**Mitigación:**
- Capacitación del equipo
- Code reviews
- Documentación
- Pair programming

#### 3. **Over-engineering en Proyectos Simples**

```
Proyecto de 3 tablas CRUD sin lógica:
→ Clean Architecture es overkill
→ Un MVC simple es suficiente
```

#### 4. **Más Código (Boilerplate)**

```
Conversiones entre capas:
HTTP DTO → Input → Entity → DB Entity

Y viceversa:
DB Entity → Entity → Output → HTTP DTO

Mucho mapeo:
- toInput()
- toEntity()
- toOutput()
- toDTO()
```

**Solución:**
- Librerías de mapeo (MapStruct, ModelMapper)
- Generadores de código
- Aceptar el trade-off (claridad vs verbosidad)

#### 5. **Performance Overhead (Mínimo)**

```
Capas adicionales = llamadas adicionales

En la práctica:
- Overhead negligible (microsegundos)
- No afecta performance real
- Los beneficios superan el costo
```

---

## 10. Cuándo Usar (y Cuándo No)

### ✅ USA Clean Architecture Cuando:

```
✓ Aplicación de larga duración (> 2 años)
✓ Lógica de negocio compleja y valiosa
✓ Múltiples interfaces (Web + Mobile + API + CLI)
✓ Equipo grande (> 5 developers)
✓ Alta necesidad de testabilidad
✓ Anticipas cambios tecnológicos frecuentes
✓ La mantenibilidad es crítica
✓ Proyecto enterprise
✓ Requisitos de negocio cambian frecuentemente
✓ Múltiples equipos trabajando en paralelo
```

### ❌ NO Uses Clean Architecture Cuando:

```
✗ Prototipo de 1 semana
✗ Hackathon
✗ CRUD ultra-simple sin lógica
✗ Script de uso único
✗ Aplicación personal pequeña
✗ Equipo sin experiencia (primero aprender lo básico)
✗ Presión extrema de tiempo
✗ No hay lógica de negocio real (solo guardar/leer datos)
✗ Proyecto descartable
```

### 🤔 Evalúa Caso por Caso

**Checklist de Decisión:**

```markdown
Responde SÍ/NO:

1. [ ] ¿La aplicación tendrá vida útil > 2 años?
2. [ ] ¿Hay lógica de negocio compleja (no solo CRUD)?
3. [ ] ¿El equipo tiene > 3 developers?
4. [ ] ¿Necesitas testear independientemente de frameworks/BD?
5. [ ] ¿Podrías cambiar tecnologías (BD, frameworks, UI)?
6. [ ] ¿Múltiples puntos de entrada (Web + Mobile + API)?
7. [ ] ¿Los requisitos de negocio cambian frecuentemente?
8. [ ] ¿La mantenibilidad es más importante que velocidad inicial?

RESULTADO:
- 7-8 SÍ: Definitivamente usa Clean Architecture
- 5-6 SÍ: Muy recomendable
- 3-4 SÍ: Evalúa beneficios vs costo
- 1-2 SÍ: Probablemente no lo necesites
- 0 SÍ: NO uses Clean Architecture, usa algo más simple
```

### Alternativas Según Contexto

```
Proyecto muy simple:
→ MVC tradicional (Rails, Django)

Proyecto mediano con algo de lógica:
→ Layered Architecture con servicios

Proyecto complejo:
→ Clean Architecture o Hexagonal

Proyecto distribuido:
→ Clean Architecture + Microservicios + DDD
```

---

## 11. Errores Comunes

### Error #1: Poner Anotaciones de Framework en Entities

```java
// ❌ MAL: Entity con anotaciones de JPA
package Entities;

@Entity  // ← Anotación de JPA (framework)
@Table(name = "courses")
public class Course {
  @Id
  @GeneratedValue
  private Long id;

  @Column(nullable = false)
  private String title;
}
```

**Por qué es error:**
- Entities no deben conocer frameworks
- Acopla el núcleo a JPA
- Rompe la regla de dependencia

```java
// ✅ BIEN: Entity pura
package Entities;

public class Course {
  private String id;
  private String title;

  // Sin anotaciones, pura lógica de negocio
}

// FrameworksAndDrivers/Database/Entities/CourseEntity.java
@Entity
@Table(name = "courses")
public class CourseEntity {  // ← Anotaciones aquí, en la capa externa
  @Id
  @GeneratedValue
  private Long id;

  @Column(nullable = false)
  private String title;
}
```

---

### Error #2: Use Case Dependiendo de Frameworks

```java
// ❌ MAL: Use Case con dependencia de Spring
package UseCases;

import org.springframework.stereotype.Service;

@Service  // ← Anotación de Spring
public class EnrollStudentInteractor implements EnrollStudentUseCase {
  // ...
}
```

**Por qué es error:**
- Use Cases no deben conocer Spring
- Dificulta testear sin Spring context

```java
// ✅ BIEN: Use Case puro
package UseCases;

public class EnrollStudentInteractor implements EnrollStudentUseCase {
  // Sin anotaciones de framework
}

// InterfaceAdapters o FrameworksAndDrivers/Configuration
@Configuration
public class UseCaseConfiguration {
  @Bean  // ← Anotaciones de Spring solo en configuración
  public EnrollStudentUseCase enrollStudentUseCase(...) {
    return new EnrollStudentInteractor(...);
  }
}
```

---

### Error #3: Lógica de Negocio en Controllers

```java
// ❌ MAL: Lógica en el controller
@RestController
public class EnrollmentController {

  @PostMapping("/enroll")
  public ResponseEntity<?> enroll(@RequestBody EnrollRequest request) {
    Course course = courseRepo.findById(request.getCourseId());

    // ❌ Lógica de negocio en el controller
    if (course.getEnrolledCount() >= course.getCapacity()) {
      return ResponseEntity.badRequest().body("Course is full");
    }

    course.setEnrolledCount(course.getEnrolledCount() + 1);
    courseRepo.save(course);

    return ResponseEntity.ok("Enrolled");
  }
}
```

```java
// ✅ BIEN: Controller solo adapta
@RestController
public class EnrollmentController {

  private final EnrollStudentUseCase enrollUseCase;

  @PostMapping("/enroll")
  public ResponseEntity<?> enroll(@RequestBody EnrollRequest request) {
    // Solo conversión y delegación
    EnrollStudentInput input = toInput(request);
    EnrollStudentOutput output = enrollUseCase.execute(input);
    return toResponse(output);
  }
}

// La lógica está en Entity y Use Case
public class Course {
  public void enrollStudent() {
    if (enrolledCount >= capacity) {
      throw new CourseFullException();  // Lógica de negocio
    }
    enrolledCount++;
  }
}
```

---

### Error #4: Entidades Anémicas

```java
// ❌ MAL: Entidad sin comportamiento (anémica)
public class Course {
  private String id;
  private int capacity;
  private int enrolledCount;

  // Solo getters y setters, sin lógica
  public int getCapacity() { return capacity; }
  public void setCapacity(int capacity) { this.capacity = capacity; }
  public int getEnrolledCount() { return enrolledCount; }
  public void setEnrolledCount(int count) { this.enrolledCount = count; }
}

// ❌ Toda la lógica termina en el Use Case
public class EnrollStudentInteractor {
  public void execute(EnrollStudentInput input) {
    Course course = repo.findById(input.getCourseId());

    // ❌ Lógica que debería estar en la entidad
    if (course.getEnrolledCount() >= course.getCapacity()) {
      throw new CourseFullException();
    }
    course.setEnrolledCount(course.getEnrolledCount() + 1);

    repo.save(course);
  }
}
```

```java
// ✅ BIEN: Entidad rica (con comportamiento)
public class Course {
  private String id;
  private int capacity;
  private int enrolledCount;

  // Comportamiento de dominio
  public void enrollStudent() {
    if (enrolledCount >= capacity) {
      throw new CourseFullException();
    }
    enrolledCount++;
  }

  public boolean hasAvailableSlots() {
    return enrolledCount < capacity;
  }

  // Getters sin setters públicos (inmutabilidad cuando sea posible)
  public int getCapacity() { return capacity; }
  public int getEnrolledCount() { return enrolledCount; }
}

// ✅ Use Case delgado (orquestación)
public class EnrollStudentInteractor {
  public void execute(EnrollStudentInput input) {
    Course course = repo.findById(input.getCourseId());

    course.enrollStudent();  // ✅ Entidad encapsula su lógica

    repo.save(course);
  }
}
```

---

### Error #5: Devolver Entities Directamente en Responses

```java
// ❌ MAL: Devolver entidad de dominio directamente
@RestController
public class CourseController {

  @GetMapping("/courses/{id}")
  public Course getCourse(@PathVariable String id) {
    return courseRepo.findById(id);  // ❌ Retorna entity directamente
  }
}
```

**Por qué es error:**
- Expone estructura interna del dominio
- Cambios en entity rompen API
- Puede exponer datos sensibles

```java
// ✅ BIEN: Usar DTOs
@RestController
public class CourseController {

  @GetMapping("/courses/{id}")
  public CourseDTO getCourse(@PathVariable String id) {
    Course course = courseRepo.findById(id);
    return toDTO(course);  // ✅ Convierte a DTO
  }

  private CourseDTO toDTO(Course course) {
    return new CourseDTO(
      course.getId(),
      course.getTitle(),
      course.getAvailableSlots()  // Solo expone lo necesario
    );
  }
}
```

---

### Error #6: Demasiada Granularidad en Use Cases

```java
// ❌ OVERKILL: Un use case por cada operación trivial
interface GetCourseByIdUseCase { ... }
interface GetCourseByTitleUseCase { ... }
interface GetAllCoursesUseCase { ... }
interface SaveCourseUseCase { ... }
interface UpdateCourseUseCase { ... }
interface DeleteCourseUseCase { ... }

// ✅ EQUILIBRADO: Agrupar operaciones relacionadas simples
interface CourseQueryService {
  Course findById(String id);
  List<Course> findAll();
  List<Course> findByTitle(String title);
}

interface EnrollStudentUseCase { ... }  // Use case complejo separado
```

**Regla:**
- Operaciones CRUD simples: pueden agruparse
- Operaciones con lógica compleja: use case propio

---

### Error #7: Saltarse Capas

```java
// ❌ MAL: Controller llama directamente a Repository
@RestController
public class CourseController {

  private final CourseRepository repository;  // ❌ Salta Use Cases

  @PostMapping("/enroll")
  public ResponseEntity<?> enroll(@RequestBody EnrollRequest request) {
    Course course = repository.findById(request.getCourseId());
    course.enrollStudent(request.getStudentId());
    repository.save(course);
    return ResponseEntity.ok("Enrolled");
  }
}
```

**Por qué es error:**
- No hay lugar para lógica de aplicación (validaciones, coordinación)
- No hay separación de concerns
- Dificulta reutilización

```java
// ✅ BIEN: Respetar las capas
@RestController
public class CourseController {

  private final EnrollStudentUseCase enrollUseCase;  // ✅ Usa Use Case

  @PostMapping("/enroll")
  public ResponseEntity<?> enroll(@RequestBody EnrollRequest request) {
    EnrollStudentInput input = toInput(request);
    EnrollStudentOutput output = enrollUseCase.execute(input);
    return toResponse(output);
  }
}
```

---

## 12. Implementación Práctica

### Paso a Paso para Empezar

#### 1. Estructura de Directorios

```
src/
├── main/
│   └── java/
│       └── com/
│           └── tuproyecto/
│               ├── entities/                # Capa 1
│               │   ├── Course.java
│               │   ├── Student.java
│               │   └── exceptions/
│               │
│               ├── usecases/                # Capa 2
│               │   ├── enrollstudent/
│               │   │   ├── EnrollStudentUseCase.java
│               │   │   ├── EnrollStudentInput.java
│               │   │   ├── EnrollStudentOutput.java
│               │   │   └── EnrollStudentInteractor.java
│               │   └── ports/
│               │       ├── CourseRepository.java
│               │       └── NotificationService.java
│               │
│               ├── interfaceadapters/       # Capa 3
│               │   ├── controllers/
│               │   │   └── EnrollmentController.java
│               │   ├── gateways/
│               │   │   ├── JpaCourseRepository.java
│               │   │   └── EmailNotificationService.java
│               │   └── presenters/
│               │
│               └── frameworks/              # Capa 4
│                   ├── database/
│                   │   ├── entities/
│                   │   │   └── CourseEntity.java
│                   │   └── SpringDataCourseRepository.java
│                   └── config/
│                       └── BeanConfiguration.java
│
└── test/
    └── java/
        └── com/
            └── tuproyecto/
                ├── entities/                # Tests sin mocks
                └── usecases/                # Tests con mocks simples
```

#### 2. Convención de Nombres

```
Entities:
- Nombres sustantivos: Course, Student, Enrollment
- Sin sufijos: NO CourseEntity (reservado para JPA)

Use Cases:
- Verbos + sustantivo: EnrollStudentUseCase, CancelEnrollmentUseCase
- Input/Output: EnrollStudentInput, EnrollStudentOutput
- Implementación: EnrollStudentInteractor o EnrollStudentService

Repositories (interfaces):
- Sustantivo + Repository: CourseRepository, StudentRepository

Controllers:
- Sustantivo + Controller: EnrollmentController, CourseController

DTOs:
- Propósito + DTO: EnrollmentRequestDTO, EnrollmentResponseDTO
```

#### 3. Inyección de Dependencias

**Opción A: Constructor Injection (Recomendado)**

```java
public class EnrollStudentInteractor implements EnrollStudentUseCase {

  private final CourseRepository courseRepository;
  private final StudentRepository studentRepository;

  // Constructor injection
  public EnrollStudentInteractor(
      CourseRepository courseRepository,
      StudentRepository studentRepository) {
    this.courseRepository = courseRepository;
    this.studentRepository = studentRepository;
  }
}

// Configuración (con Spring)
@Configuration
public class UseCaseConfiguration {

  @Bean
  public EnrollStudentUseCase enrollStudentUseCase(
      CourseRepository courseRepository,
      StudentRepository studentRepository) {
    return new EnrollStudentInteractor(courseRepository, studentRepository);
  }
}
```

**Opción B: Sin Framework (Manual)**

```java
public class Main {
  public static void main(String[] args) {
    // Capa 4: Frameworks
    SpringDataCourseRepository springRepo = new SpringDataCourseRepository(...);

    // Capa 3: Adapters
    CourseRepository courseRepo = new JpaCourseRepository(springRepo);
    NotificationService notificationService = new EmailNotificationService(...);

    // Capa 2: Use Cases
    EnrollStudentUseCase enrollUseCase = new EnrollStudentInteractor(
      courseRepo,
      notificationService
    );

    // Capa 3: Controllers
    EnrollmentController controller = new EnrollmentController(enrollUseCase);

    // Iniciar servidor
    Server server = new Server(controller);
    server.start();
  }
}
```

#### 4. Testing Strategy

```java
// Test de Entities (sin mocks, sin frameworks)
class CourseTest {

  @Test
  void shouldEnrollStudentWhenCourseHasCapacity() {
    Course course = new Course("1", "Math", 30);

    course.enrollStudent("student-1");

    assertEquals(1, course.getEnrolledCount());
  }

  @Test
  void shouldThrowExceptionWhenCourseIsFull() {
    Course course = new Course("1", "Math", 1);
    course.enrollStudent("student-1");

    assertThrows(CourseFullException.class, () -> {
      course.enrollStudent("student-2");
    });
  }
}

// Test de Use Cases (mocks de interfaces, sin BD)
class EnrollStudentInteractorTest {

  @Test
  void shouldEnrollStudentSuccessfully() {
    // Arrange: mocks
    CourseRepository mockCourseRepo = mock(CourseRepository.class);
    StudentRepository mockStudentRepo = mock(StudentRepository.class);
    NotificationService mockNotification = mock(NotificationService.class);

    Course course = new Course("1", "Math", 30);
    Student student = new Student("s1", "John", "john@example.com");

    when(mockCourseRepo.findById("1")).thenReturn(Optional.of(course));
    when(mockStudentRepo.findById("s1")).thenReturn(Optional.of(student));

    EnrollStudentInteractor useCase = new EnrollStudentInteractor(
      mockCourseRepo,
      mockStudentRepo,
      mock(EnrollmentRepository.class),
      mockNotification
    );

    // Act
    EnrollStudentInput input = new EnrollStudentInput("s1", "1");
    EnrollStudentOutput output = useCase.execute(input);

    // Assert
    assertTrue(output.isSuccess());
    verify(mockCourseRepo).save(any(Course.class));
    verify(mockNotification).notifyEnrollment(eq("john@example.com"), any(), any());
  }
}

// Test de integración (con BD real o en memoria)
@SpringBootTest
class EnrollmentIntegrationTest {

  @Autowired
  private EnrollStudentUseCase enrollUseCase;

  @Test
  void shouldEnrollStudentEndToEnd() {
    // Test con BD real (H2 en memoria)
    EnrollStudentInput input = new EnrollStudentInput("student-1", "course-1");
    EnrollStudentOutput output = enrollUseCase.execute(input);

    assertTrue(output.isSuccess());
    assertNotNull(output.getEnrollmentId());
  }
}
```

---

## 13. Preguntas Frecuentes

### ¿Es Clean Architecture lo mismo que Clean Code?

**NO:**

```
Clean Code (libro de Robert C. Martin):
- Prácticas de escritura de código
- Nombres descriptivos, funciones pequeñas, comentarios mínimos
- Nivel: línea de código, función, clase

Clean Architecture (libro de Robert C. Martin):
- Organización de alto nivel del sistema
- Separación en capas, dependencias, módulos
- Nivel: módulo, componente, sistema completo

Relación: Se complementan
- Clean Code: cómo escribir cada línea
- Clean Architecture: cómo organizar miles de líneas
```

### ¿Dónde van las validaciones?

**Depende del tipo:**

```
Validaciones de DOMINIO (reglas de negocio):
→ En Entities
Ejemplo: "Un ISBN debe tener 13 dígitos"

Validaciones de APLICACIÓN (reglas de caso de uso):
→ En Use Cases
Ejemplo: "Un estudiante no puede inscribirse si tiene multas"

Validaciones de ENTRADA (formato, tipo):
→ En Interface Adapters (Controllers)
Ejemplo: "El campo email es obligatorio y debe ser válido"
```

```java
// Controller: validaciones de formato
@PostMapping("/enroll")
public ResponseEntity<?> enroll(@Valid @RequestBody EnrollRequestDTO request) {
  // @Valid valida: email formato correcto, campos obligatorios
}

// Use Case: validaciones de aplicación
public EnrollStudentOutput execute(EnrollStudentInput input) {
  if (student.hasOverdueLoan()) {  // Regla de aplicación
    return EnrollStudentOutput.failure("Student has overdue loan");
  }
}

// Entity: validaciones de dominio
public class Book {
  public Book(String isbn) {
    if (isbn.length() != 13) {  // Regla de dominio
      throw new InvalidISBNException();
    }
    this.isbn = isbn;
  }
}
```

### ¿Dónde van las transacciones?

**En Use Cases (Interactors):**

```java
// ✅ BIEN
@Transactional  // En el Use Case
public class EnrollStudentInteractor implements EnrollStudentUseCase {

  public EnrollStudentOutput execute(EnrollStudentInput input) {
    course.enrollStudent();
    courseRepository.save(course);
    enrollmentRepository.save(enrollment);
    // Todo en una transacción
  }
}
```

**Razón:**
Las transacciones coordinan operaciones → responsabilidad de Use Cases.

### ¿Puedo usar el mismo objeto en todas las capas?

**NO recomendado:**

```java
// ❌ Evitar: mismo objeto en todas las capas
Course (usado en Entity, Use Case, Controller, BD)

// ✅ Mejor: objetos específicos por capa
Course (Entity) → lógica de negocio
CourseEntity (BD) → anotaciones JPA
CourseDTO (Controller) → JSON serialization
```

**Excepción:** Value Objects simples pueden compartirse.

```java
// OK compartir
class Money {
  private BigDecimal amount;
  private Currency currency;
}

// NO compartir
class Course {
  // Tiene comportamiento y se mapea a BD
}
```

### ¿Cómo manejo paginación?

```java
// Opción 1: En Use Case (recomendado si hay lógica)
interface ListCoursesUseCase {
  ListCoursesOutput execute(ListCoursesInput input);
}

class ListCoursesInput {
  private int page;
  private int size;
}

class ListCoursesOutput {
  private List<Course> courses;
  private int totalPages;
  private int currentPage;
}

// Opción 2: En Repository (si es solo paginación simple)
interface CourseRepository {
  Page<Course> findAll(Pageable pageable);
}
```

### ¿Clean Architecture funciona con Microservicios?

**SÍ, perfectamente:**

```
Cada microservicio puede usar Clean Architecture internamente:

Microservicio de Cursos:
├── Entities (Course, Enrollment)
├── Use Cases (EnrollStudent, CancelEnrollment)
├── Interface Adapters (REST, gRPC)
└── Frameworks (PostgreSQL, Kafka)

Microservicio de Pagos:
├── Entities (Payment, Invoice)
├── Use Cases (ProcessPayment, RefundPayment)
├── Interface Adapters (REST, Event listeners)
└── Frameworks (MySQL, Stripe API)

Cada microservicio es autónomo y usa Clean Architecture
```

### ¿Necesito usar todos los patrones de DDD?

**NO, Clean Architecture no requiere DDD:**

```
Clean Architecture: estructura del código
DDD: modelado del dominio

Puedes usar:
✓ Clean Architecture sin DDD
✓ DDD sin Clean Architecture
✓ Ambos juntos (común en proyectos complejos)

Clean Architecture + DDD lite:
- Usa Entities y Value Objects de DDD
- No necesariamente Aggregates, Bounded Contexts, etc.
```

---

## Resumen Final

### Los 5 Pilares de Clean Architecture

```
1. SEPARACIÓN EN CAPAS
   Entities → Use Cases → Interface Adapters → Frameworks
   Cada capa tiene responsabilidad clara

2. REGLA DE DEPENDENCIA
   Dependencias apuntan SOLO hacia adentro
   Core no conoce mundo exterior

3. INDEPENDENCIA
   Independiente de frameworks, BD, UI
   Fácil cambiar tecnologías

4. TESTABILIDAD
   Testear sin frameworks, BD, UI
   Tests rápidos y confiables

5. INVERSIÓN DE DEPENDENCIAS
   Use Cases definen interfaces
   Frameworks implementan interfaces
```

---

### Checklist de Implementación

```
Al implementar nueva feature:

Entities:
□ ¿La entidad tiene lógica de negocio (no solo getters/setters)?
□ ¿La entidad NO tiene anotaciones de frameworks?
□ ¿Las validaciones de dominio están en la entidad?
□ ¿La entidad es testeable sin mocks?

Use Cases:
□ ¿El use case define interfaces (puertos)?
□ ¿El use case NO depende de frameworks?
□ ¿El use case orquesta sin lógica de negocio compleja?
□ ¿Input y Output son DTOs simples?

Interface Adapters:
□ ¿Los adapters implementan interfaces de use cases?
□ ¿Los controllers solo convierten formatos?
□ ¿Los gateways traducen entre dominio y mundo exterior?
□ ¿NO hay lógica de negocio en adapters?

Frameworks & Drivers:
□ ¿Las anotaciones de frameworks están solo aquí?
□ ¿La configuración está aislada?
□ ¿Es fácil reemplazar esta capa?

Dependencias:
□ ¿Entities NO importan de otras capas?
□ ¿Use Cases NO importan de Adapters o Frameworks?
□ ¿Interface Adapters NO importan de Frameworks?
□ ¿Todas las dependencias apuntan hacia adentro?

Testing:
□ ¿Puedes testear entities sin mocks?
□ ¿Puedes testear use cases con mocks simples?
□ ¿Los tests NO requieren BD/servidor real?
```

---

## Recursos Adicionales

### Libros Esenciales

```
📚 "Clean Architecture" - Robert C. Martin (Uncle Bob)
   → El libro definitivo, lectura obligatoria

📚 "Clean Code" - Robert C. Martin
   → Complemento perfecto para escribir buen código

📚 "Domain-Driven Design" - Eric Evans
   → Para modelado del dominio

📚 "Implementing Domain-Driven Design" - Vaughn Vernon
   → DDD práctico

📚 "Get Your Hands Dirty on Clean Architecture" - Tom Hombergs
   → Implementación práctica con Java/Spring
```

### Artículos y Blogs

```
🔗 The Clean Architecture (Uncle Bob):
   https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html

🔗 Netflix Tech Blog (aplicaciones reales)

🔗 Martin Fowler's Blog
   https://martinfowler.com
```

### Videos

```
🎥 "Clean Architecture and Design" - Robert C. Martin
🎥 "ITkonekt 2019 | Robert C. Martin (Uncle Bob), Clean Architecture and Design"
```

---

## Conclusión

**Clean Architecture NO es:**
- ❌ Una receta mágica para todo proyecto
- ❌ Obligatoria en aplicaciones simples
- ❌ Difícil de entender (una vez que captas los fundamentos)

**Clean Architecture ES:**
- ✅ Una forma de organizar código complejo
- ✅ Una inversión en mantenibilidad a largo plazo
- ✅ Especialmente valiosa en proyectos enterprise
- ✅ Un facilitador de cambios tecnológicos
- ✅ Un patrón que mejora testabilidad drásticamente

**Pregunta clave antes de usarla:**

> ¿Mi aplicación tiene suficiente complejidad y longevidad para justificar esta estructura?

**Si la respuesta es SÍ** → Clean Architecture te ahorrará dolor a largo plazo.

**Si la respuesta es NO** → Un patrón más simple (MVC, Layered) puede ser suficiente.

---

**La arquitectura debe servir al proyecto, no al revés.**

No uses Clean Architecture porque está de moda.
Úsala porque tu proyecto la necesita.

**Happy Clean Coding!** 🎯

---

**Última actualización:** Diciembre 2025
**Versión:** 1.0
**Autor:** Illescas, Michael Jonathan
**Licencia:** Uso libre para aprendizaje y aplicación profesional
