# Manual de Patrones de Diseño (Design Patterns)
## Soluciones Probadas a Problemas Comunes del Desarrollo

> **Aprende los patrones de diseño más importantes del software moderno**
>
> Este manual explica los Design Patterns de forma simple, práctica y con ejemplos reales, sin asumir conocimientos previos.

---

## 📚 Índice

1. [¿Qué Son los Patrones de Diseño?](#1-qué-son-los-patrones-de-diseño)
2. [Historia y el Gang of Four](#2-historia-y-el-gang-of-four)
3. [Por Qué Son Importantes](#3-por-qué-son-importantes)
4. [Categorías de Patrones](#4-categorías-de-patrones)
5. [Patrones Creacionales](#5-patrones-creacionales)
6. [Patrones Estructurales](#6-patrones-estructurales)
7. [Patrones de Comportamiento](#7-patrones-de-comportamiento)
8. [Patrones Arquitectónicos](#8-patrones-arquitectónicos)
9. [Antipatrones](#9-antipatrones)
10. [Cómo Elegir el Patrón Correcto](#10-cómo-elegir-el-patrón-correcto)
11. [Patrones y SOLID](#11-patrones-y-solid)
12. [Errores Comunes](#12-errores-comunes)
13. [Preguntas Frecuentes](#13-preguntas-frecuentes)

---

## 1. ¿Qué Son los Patrones de Diseño?

### Definición Simple

**Un patrón de diseño es una solución reutilizable a un problema común que ocurre frecuentemente en el diseño de software.**

```
Piénsalo como:
- Recetas de cocina para programadores
- Soluciones documentadas a problemas recurrentes
- Vocabulario común entre desarrolladores
- Best practices codificadas
```

### Analogía del Mundo Real

```
Problema: Cruzar un río
Soluciones (patrones):
- Puente colgante (para lugares con viento)
- Puente de arco (para distancias largas)
- Puente levadizo (para permitir paso de barcos)

Cada "patrón de puente" resuelve el mismo problema
pero es apropiado para diferentes contextos.

En software:
Problema: Crear objetos complejos
Soluciones (patrones):
- Factory (cuando el tipo varía)
- Builder (cuando tiene muchos parámetros)
- Singleton (cuando solo debe haber uno)
```

### Qué NO Son los Patrones

```
❌ NO son código que puedes copiar y pegar
❌ NO son algoritmos específicos
❌ NO son frameworks o librerías
❌ NO son la solución a TODO problema

✅ SON plantillas/guías conceptuales
✅ SON soluciones abstractas que adaptas
✅ SON lenguaje común entre desarrolladores
✅ SON best practices documentadas
```

---

## 2. Historia y el Gang of Four

### Origen

```
📅 1994: Se publica "Design Patterns: Elements of Reusable Object-Oriented Software"
👥 Autores: Erich Gamma, Richard Helm, Ralph Johnson, John Vlissides
📖 Conocidos como "Gang of Four" (GoF)
🎯 Documentan 23 patrones fundamentales
```

### Impacto

```
Antes del GoF:
- Cada desarrollador "reinventaba la rueda"
- No había vocabulario común
- Soluciones ad-hoc sin documentar

Después del GoF:
- Vocabulario estándar ("usa un Factory ahí")
- Soluciones probadas y documentadas
- Mejor comunicación en equipos
- Base de frameworks modernos
```

### Los 23 Patrones del GoF

**Creacionales (5):**
```
Singleton, Factory Method, Abstract Factory, Builder, Prototype
```

**Estructurales (7):**
```
Adapter, Bridge, Composite, Decorator, Facade, Flyweight, Proxy
```

**Comportamiento (11):**
```
Chain of Responsibility, Command, Iterator, Mediator, Memento,
Observer, State, Strategy, Template Method, Visitor, Interpreter
```

---

## 3. Por Qué Son Importantes

### Beneficios Directos

#### 1. **Vocabulario Común**
```java
// Sin patrones:
"Crea una clase que envuelva otra y agregue funcionalidad"

// Con patrones:
"Usa el patrón Decorator"

// Comunicación más eficiente
```

#### 2. **Soluciones Probadas**
```
No reinventas la rueda
Evitas errores comunes
Código más mantenible
Menos bugs
```

#### 3. **Facilita Refactoring**
```
Reconoces problemas: "Esto necesita un Strategy"
Sabes cómo solucionarlos: Aplicas el patrón conocido
Código más limpio y organizado
```

#### 4. **Mejores Diseños**
```
Aplicar patrones → Cumplir principios SOLID
Código más flexible y extensible
Mejor separación de concerns
```

### Cuándo Usar Patrones

```
✓ Cuando reconoces el problema que el patrón resuelve
✓ Cuando la solución del patrón encaja naturalmente
✓ Cuando el costo del patrón < beneficio

❌ NO uses patrones "porque sí"
❌ NO fuerces patrones donde no encajan
❌ NO sobre-ingenieríes con patrones innecesarios
```

---

## 4. Categorías de Patrones

### Patrones Creacionales

**Propósito:** Cómo crear objetos

```
Problema: new Clase() puede ser:
- Complejo (muchos parámetros)
- Condicional (depende del tipo)
- Único (solo una instancia)

Solución: Patrones creacionales abstraen la creación
```

**Patrones:**
- **Singleton** - Solo una instancia
- **Factory Method** - Delega creación a subclases
- **Abstract Factory** - Familias de objetos relacionados
- **Builder** - Construcción paso a paso
- **Prototype** - Clonación de objetos

### Patrones Estructurales

**Propósito:** Cómo componer clases y objetos

```
Problema: Necesitas:
- Adaptar interfaces incompatibles
- Agregar funcionalidad dinámicamente
- Simplificar sistemas complejos

Solución: Patrones estructurales organizan relaciones
```

**Patrones:**
- **Adapter** - Adapta interfaces incompatibles
- **Decorator** - Agrega responsabilidades dinámicamente
- **Facade** - Simplifica interfaz compleja
- **Proxy** - Controla acceso a un objeto
- **Composite** - Compone objetos en estructuras de árbol
- **Bridge** - Separa abstracción de implementación
- **Flyweight** - Comparte objetos para eficiencia

### Patrones de Comportamiento

**Propósito:** Cómo los objetos interactúan y distribuyen responsabilidades

```
Problema: Necesitas:
- Cambiar algoritmo en runtime
- Definir comunicación entre objetos
- Encapsular requests

Solución: Patrones de comportamiento definen interacciones
```

**Patrones:**
- **Strategy** - Intercambia algoritmos
- **Observer** - Notificación de cambios
- **Command** - Encapsula requests
- **State** - Cambia comportamiento según estado
- **Template Method** - Define esqueleto de algoritmo
- **Chain of Responsibility** - Cadena de handlers
- Mediator, Memento, Visitor, Iterator, Interpreter

---

## 5. Patrones Creacionales

### 5.1 Singleton

#### Definición

> **Asegura que una clase tenga solo una instancia y proporciona un punto de acceso global a ella.**

#### Problema

```java
// ❌ Sin Singleton: Múltiples instancias indeseadas
class DatabaseConnection {
  public DatabaseConnection() {
    // Conexión costosa
  }
}

// Diferentes partes del código crean múltiples conexiones
DatabaseConnection db1 = new DatabaseConnection();
DatabaseConnection db2 = new DatabaseConnection();  // ❌ Nueva conexión innecesaria
DatabaseConnection db3 = new DatabaseConnection();  // ❌ Otra más

// Problemas:
// - Desperdicio de recursos
// - Posibles inconsistencias
// - No hay control sobre instancias
```

#### Solución

```java
// ✅ Singleton: Una sola instancia
class DatabaseConnection {
  private static DatabaseConnection instance;

  // Constructor privado (no se puede instanciar desde fuera)
  private DatabaseConnection() {
    System.out.println("Connecting to database...");
  }

  // Método estático para obtener la única instancia
  public static DatabaseConnection getInstance() {
    if (instance == null) {
      instance = new DatabaseConnection();
    }
    return instance;
  }

  public void query(String sql) {
    System.out.println("Executing: " + sql);
  }
}

// Uso
DatabaseConnection db1 = DatabaseConnection.getInstance();
DatabaseConnection db2 = DatabaseConnection.getInstance();
DatabaseConnection db3 = DatabaseConnection.getInstance();

// db1, db2, db3 son la MISMA instancia
```

#### Singleton Thread-Safe

```java
// ✅ Thread-safe con inicialización temprana
class DatabaseConnection {
  private static final DatabaseConnection instance = new DatabaseConnection();

  private DatabaseConnection() {
    // Constructor privado
  }

  public static DatabaseConnection getInstance() {
    return instance;
  }
}

// ✅ Thread-safe con double-checked locking
class DatabaseConnection {
  private static volatile DatabaseConnection instance;

  private DatabaseConnection() { }

  public static DatabaseConnection getInstance() {
    if (instance == null) {
      synchronized (DatabaseConnection.class) {
        if (instance == null) {
          instance = new DatabaseConnection();
        }
      }
    }
    return instance;
  }
}

// ✅ Thread-safe con Enum (mejor práctica en Java)
enum DatabaseConnection {
  INSTANCE;

  public void query(String sql) {
    System.out.println("Executing: " + sql);
  }
}

// Uso
DatabaseConnection.INSTANCE.query("SELECT * FROM users");
```

#### Cuándo Usar Singleton

```
✓ Configuración global
✓ Logger
✓ Pool de conexiones
✓ Cache
✓ Driver de hardware (impresora, etc.)

❌ NO usar para:
- Evitar pasar dependencias (usa DI)
- Estado global mutable (antipatrón)
- Testing (dificulta mocks)
```

#### Singleton y SOLID

```
⚠️ Singleton VIOLA algunos principios SOLID:
- Violación de SRP (controla creación + lógica)
- Dificulta DIP (acoplamiento a implementación concreta)
- Dificulta testing (estado global)

Alternativa moderna: Dependency Injection con scope Singleton
```

```java
// Mejor: DI con scope singleton
@Component
@Scope("singleton")  // Spring lo maneja
class DatabaseConnection {
  // Constructor normal
}

// Uso con DI
class UserService {
  private final DatabaseConnection db;

  public UserService(DatabaseConnection db) {  // Inyectado
    this.db = db;
  }
}
```

---

### 5.2 Factory Method

#### Definición

> **Define una interfaz para crear objetos, pero deja que las subclases decidan qué clase instanciar.**

#### Problema

```java
// ❌ Sin Factory: Lógica de creación acoplada
class NotificationService {
  public void sendNotification(String type, String message) {
    Notification notification;

    // Lógica de creación mezclada con lógica de negocio
    if (type.equals("EMAIL")) {
      notification = new EmailNotification();
    } else if (type.equals("SMS")) {
      notification = new SMSNotification();
    } else if (type.equals("PUSH")) {
      notification = new PushNotification();
    } else {
      throw new IllegalArgumentException("Unknown type");
    }

    notification.send(message);
  }
}

// Problemas:
// - Violación de OCP (agregar tipo = modificar clase)
// - Lógica de creación mezclada
// - Difícil de testear
```

#### Solución

```java
// ✅ Factory Method: Creación delegada

// Producto abstracto
interface Notification {
  void send(String message);
}

// Productos concretos
class EmailNotification implements Notification {
  @Override
  public void send(String message) {
    System.out.println("Sending EMAIL: " + message);
  }
}

class SMSNotification implements Notification {
  @Override
  public void send(String message) {
    System.out.println("Sending SMS: " + message);
  }
}

class PushNotification implements Notification {
  @Override
  public void send(String message) {
    System.out.println("Sending PUSH: " + message);
  }
}

// Creator abstracto
abstract class NotificationFactory {
  // Factory Method (abstracto)
  protected abstract Notification createNotification();

  // Template method que usa factory method
  public void sendNotification(String message) {
    Notification notification = createNotification();
    notification.send(message);
  }
}

// Creators concretos
class EmailNotificationFactory extends NotificationFactory {
  @Override
  protected Notification createNotification() {
    return new EmailNotification();
  }
}

class SMSNotificationFactory extends NotificationFactory {
  @Override
  protected Notification createNotification() {
    return new SMSNotification();
  }
}

// Uso
NotificationFactory factory = new EmailNotificationFactory();
factory.sendNotification("Hello World");  // Envía por email

factory = new SMSNotificationFactory();
factory.sendNotification("Hello World");  // Envía por SMS
```

#### Factory Method Simplificado (Sin Herencia)

```java
// ✅ Factory Method simple (más común)
class NotificationFactory {
  public static Notification createNotification(String type) {
    switch (type.toUpperCase()) {
      case "EMAIL":
        return new EmailNotification();
      case "SMS":
        return new SMSNotification();
      case "PUSH":
        return new PushNotification();
      default:
        throw new IllegalArgumentException("Unknown type: " + type);
    }
  }
}

// Uso
Notification notification = NotificationFactory.createNotification("EMAIL");
notification.send("Hello");
```

#### Cuándo Usar Factory Method

```
✓ Cuando no sabes de antemano qué tipo exacto de objeto necesitas
✓ Cuando la creación es compleja
✓ Cuando quieres desacoplar creación de uso
✓ Cuando hay lógica condicional de creación

❌ NO usar para:
- Objetos muy simples (overkill)
- Cuando solo hay un tipo
```

---

### 5.3 Builder

#### Definición

> **Separa la construcción de un objeto complejo de su representación, permitiendo crear diferentes representaciones con el mismo proceso de construcción.**

#### Problema

```java
// ❌ Sin Builder: Constructor con muchos parámetros
class User {
  private String firstName;
  private String lastName;
  private String email;
  private String phone;
  private String address;
  private String city;
  private String country;
  private String zipCode;
  private boolean newsletter;
  private boolean premiumMember;

  // Constructor telescópico (antipatrón)
  public User(String firstName, String lastName, String email) {
    this(firstName, lastName, email, null, null, null, null, null, false, false);
  }

  public User(String firstName, String lastName, String email, String phone) {
    this(firstName, lastName, email, phone, null, null, null, null, false, false);
  }

  // Constructor con TODOS los parámetros
  public User(String firstName, String lastName, String email, String phone,
              String address, String city, String country, String zipCode,
              boolean newsletter, boolean premiumMember) {
    this.firstName = firstName;
    this.lastName = lastName;
    // ... 10 asignaciones
  }
}

// Uso: Confuso y propenso a errores
User user = new User("John", "Doe", "john@example.com", "555-1234",
                     "123 Main St", "New York", "USA", "10001",
                     true, false);
// ¿Cuál era el parámetro 9? ¿Y el 10?
```

#### Solución

```java
// ✅ Builder: Construcción paso a paso

class User {
  // Atributos finales (inmutable)
  private final String firstName;
  private final String lastName;
  private final String email;
  private final String phone;
  private final String address;
  private final String city;
  private final String country;
  private final String zipCode;
  private final boolean newsletter;
  private final boolean premiumMember;

  // Constructor privado (solo Builder puede crear)
  private User(UserBuilder builder) {
    this.firstName = builder.firstName;
    this.lastName = builder.lastName;
    this.email = builder.email;
    this.phone = builder.phone;
    this.address = builder.address;
    this.city = builder.city;
    this.country = builder.country;
    this.zipCode = builder.zipCode;
    this.newsletter = builder.newsletter;
    this.premiumMember = builder.premiumMember;
  }

  // Getters
  public String getFirstName() { return firstName; }
  public String getEmail() { return email; }
  // ...

  // Builder estático interno
  public static class UserBuilder {
    // Campos obligatorios
    private final String firstName;
    private final String lastName;
    private final String email;

    // Campos opcionales (valores por defecto)
    private String phone = "";
    private String address = "";
    private String city = "";
    private String country = "";
    private String zipCode = "";
    private boolean newsletter = false;
    private boolean premiumMember = false;

    // Constructor con campos obligatorios
    public UserBuilder(String firstName, String lastName, String email) {
      this.firstName = firstName;
      this.lastName = lastName;
      this.email = email;
    }

    // Métodos fluent para campos opcionales
    public UserBuilder phone(String phone) {
      this.phone = phone;
      return this;
    }

    public UserBuilder address(String address) {
      this.address = address;
      return this;
    }

    public UserBuilder city(String city) {
      this.city = city;
      return this;
    }

    public UserBuilder country(String country) {
      this.country = country;
      return this;
    }

    public UserBuilder zipCode(String zipCode) {
      this.zipCode = zipCode;
      return this;
    }

    public UserBuilder newsletter(boolean newsletter) {
      this.newsletter = newsletter;
      return this;
    }

    public UserBuilder premiumMember(boolean premiumMember) {
      this.premiumMember = premiumMember;
      return this;
    }

    // Método de construcción final
    public User build() {
      // Validaciones antes de crear
      if (firstName == null || firstName.isEmpty()) {
        throw new IllegalStateException("First name is required");
      }
      if (!email.contains("@")) {
        throw new IllegalStateException("Invalid email");
      }

      return new User(this);
    }
  }
}

// Uso: Claro y legible
User user = new User.UserBuilder("John", "Doe", "john@example.com")
    .phone("555-1234")
    .address("123 Main St")
    .city("New York")
    .newsletter(true)
    .build();

// Otro usuario con menos campos
User simpleUser = new User.UserBuilder("Jane", "Smith", "jane@example.com")
    .build();
```

#### Builder con Lombok (Java)

```java
// ✅ Builder automático con Lombok
import lombok.Builder;
import lombok.Value;

@Value  // Inmutable
@Builder
class User {
  String firstName;
  String lastName;
  String email;
  String phone;
  String address;
  boolean newsletter;
}

// Uso
User user = User.builder()
    .firstName("John")
    .lastName("Doe")
    .email("john@example.com")
    .newsletter(true)
    .build();
```

#### Cuándo Usar Builder

```
✓ Objetos con muchos parámetros (> 4-5)
✓ Muchos parámetros opcionales
✓ Cuando necesitas validación antes de crear
✓ Cuando quieres inmutabilidad
✓ Cuando el orden de construcción importa

❌ NO usar para:
- Objetos simples (2-3 parámetros)
- Objetos mutables simples (usa setters)
```

---

### 5.4 Prototype

#### Definición

> **Crea nuevos objetos clonando una instancia existente (prototipo).**

#### Problema

```java
// ❌ Sin Prototype: Creación costosa repetida
class ComplexObject {
  private List<String> data;
  private Map<String, Object> config;

  public ComplexObject() {
    // Inicialización MUY costosa
    data = loadFromDatabase();      // 2 segundos
    config = loadFromAPI();          // 3 segundos
    processComplexLogic();           // 5 segundos
    // Total: 10 segundos por instancia
  }
}

// Necesitas 100 instancias similares
for (int i = 0; i < 100; i++) {
  ComplexObject obj = new ComplexObject();  // ❌ 10 segundos cada una = 1000 segundos!
}
```

#### Solución

```java
// ✅ Prototype: Clonación
class ComplexObject implements Cloneable {
  private List<String> data;
  private Map<String, Object> config;

  public ComplexObject() {
    // Inicialización costosa solo UNA vez
    data = loadFromDatabase();
    config = loadFromAPI();
    processComplexLogic();
  }

  // Constructor privado para copia
  private ComplexObject(ComplexObject source) {
    // Copia profunda (deep copy)
    this.data = new ArrayList<>(source.data);
    this.config = new HashMap<>(source.config);
  }

  @Override
  public ComplexObject clone() {
    return new ComplexObject(this);
  }
}

// Uso
ComplexObject prototype = new ComplexObject();  // 10 segundos (solo una vez)

// Clonar es instantáneo
for (int i = 0; i < 100; i++) {
  ComplexObject obj = prototype.clone();  // ✓ Milisegundos!
}
```

#### Prototype con Registro

```java
// ✅ Prototype Registry
class ShapeCache {
  private static Map<String, Shape> shapeMap = new HashMap<>();

  // Cargar prototipos
  public static void loadCache() {
    Circle circle = new Circle();
    circle.setRadius(5);
    shapeMap.put("circle", circle);

    Rectangle rectangle = new Rectangle();
    rectangle.setWidth(10);
    rectangle.setHeight(5);
    shapeMap.put("rectangle", rectangle);
  }

  // Obtener clon
  public static Shape getShape(String type) {
    Shape cachedShape = shapeMap.get(type);
    return cachedShape.clone();
  }
}

// Uso
ShapeCache.loadCache();

Shape clonedCircle1 = ShapeCache.getShape("circle");
Shape clonedCircle2 = ShapeCache.getShape("circle");
// Dos círculos independientes, creados por clonación
```

#### Cuándo Usar Prototype

```
✓ Cuando crear objetos es muy costoso
✓ Cuando los objetos son muy similares
✓ Cuando necesitas copias independientes
✓ Cuando el tipo de objeto se determina en runtime

❌ NO usar para:
- Objetos simples
- Cuando necesitas objetos realmente únicos
```

---

## 6. Patrones Estructurales

### 6.1 Adapter

#### Definición

> **Convierte la interfaz de una clase en otra interfaz que el cliente espera. Permite que clases con interfaces incompatibles trabajen juntas.**

#### Problema

```java
// ❌ Sin Adapter: Interfaces incompatibles

// Interfaz que espera tu código
interface MediaPlayer {
  void play(String filename);
}

// Tu implementación
class AudioPlayer implements MediaPlayer {
  @Override
  public void play(String filename) {
    if (filename.endsWith(".mp3")) {
      System.out.println("Playing MP3: " + filename);
    } else {
      System.out.println("Format not supported");
    }
  }
}

// Librería externa (no puedes modificar)
class AdvancedMediaPlayer {
  public void playVLC(String filename) {
    System.out.println("Playing VLC: " + filename);
  }

  public void playMP4(String filename) {
    System.out.println("Playing MP4: " + filename);
  }
}

// ❌ No puedes usar AdvancedMediaPlayer porque tiene interfaz diferente
// AudioPlayer player = new AdvancedMediaPlayer();  // ERROR: interfaces incompatibles
```

#### Solución

```java
// ✅ Adapter: Convierte interfaz incompatible

// Adaptador
class MediaAdapter implements MediaPlayer {
  private AdvancedMediaPlayer advancedPlayer;

  public MediaAdapter(String fileType) {
    this.advancedPlayer = new AdvancedMediaPlayer();
  }

  @Override
  public void play(String filename) {
    // Adapta la llamada
    if (filename.endsWith(".vlc")) {
      advancedPlayer.playVLC(filename);
    } else if (filename.endsWith(".mp4")) {
      advancedPlayer.playMP4(filename);
    }
  }
}

// AudioPlayer mejorado que usa el adaptador
class ImprovedAudioPlayer implements MediaPlayer {
  @Override
  public void play(String filename) {
    if (filename.endsWith(".mp3")) {
      System.out.println("Playing MP3: " + filename);
    } else if (filename.endsWith(".vlc") || filename.endsWith(".mp4")) {
      // Usa el adaptador para formatos no soportados
      MediaAdapter adapter = new MediaAdapter(filename);
      adapter.play(filename);
    } else {
      System.out.println("Format not supported");
    }
  }
}

// Uso
MediaPlayer player = new ImprovedAudioPlayer();
player.play("song.mp3");   // Reproducción directa
player.play("movie.mp4");  // A través del adaptador
player.play("video.vlc");  // A través del adaptador
```

#### Ejemplo Real: Integración de APIs

```java
// Tu interfaz de pago
interface PaymentProcessor {
  PaymentResult processPayment(double amount, String cardNumber);
}

// API de Stripe (externa, no puedes modificar)
class StripeAPI {
  public StripeResponse charge(int cents, String token) {
    // Lógica de Stripe
    return new StripeResponse(true, "tx_123");
  }
}

// API de PayPal (externa, diferente interfaz)
class PayPalAPI {
  public PayPalResult createPayment(String amountUSD, String account) {
    // Lógica de PayPal
    return new PayPalResult("COMPLETED", "pay_456");
  }
}

// ✅ Adaptadores
class StripeAdapter implements PaymentProcessor {
  private StripeAPI stripe = new StripeAPI();

  @Override
  public PaymentResult processPayment(double amount, String cardNumber) {
    // Adapta parámetros (dollars → cents, card → token)
    int cents = (int) (amount * 100);
    String token = generateToken(cardNumber);

    // Llama a Stripe con su interfaz
    StripeResponse response = stripe.charge(cents, token);

    // Adapta respuesta
    return new PaymentResult(response.isSuccess(), response.getTransactionId());
  }

  private String generateToken(String cardNumber) {
    return "tok_" + cardNumber.hashCode();
  }
}

class PayPalAdapter implements PaymentProcessor {
  private PayPalAPI paypal = new PayPalAPI();

  @Override
  public PaymentResult processPayment(double amount, String cardNumber) {
    // Adapta parámetros
    String amountUSD = String.format("%.2f", amount);
    String account = convertCardToAccount(cardNumber);

    // Llama a PayPal con su interfaz
    PayPalResult result = paypal.createPayment(amountUSD, account);

    // Adapta respuesta
    return new PaymentResult(
      result.getStatus().equals("COMPLETED"),
      result.getPaymentId()
    );
  }

  private String convertCardToAccount(String cardNumber) {
    return "account_" + cardNumber;
  }
}

// Uso: mismo código para ambas APIs
PaymentProcessor processor;

processor = new StripeAdapter();
processor.processPayment(100.00, "4242424242424242");

processor = new PayPalAdapter();
processor.processPayment(100.00, "4242424242424242");
```

#### Cuándo Usar Adapter

```
✓ Integrar código legacy con código nuevo
✓ Integrar librerías externas con interfaces incompatibles
✓ Cuando no puedes modificar la clase existente
✓ Cuando quieres reutilizar clases existentes

❌ NO usar para:
- Cuando puedes modificar la clase original
- Cuando el esfuerzo de adaptación > beneficio
```

---

### 6.2 Decorator

#### Definición

> **Agrega responsabilidades a un objeto dinámicamente. Proporciona una alternativa flexible a la herencia para extender funcionalidad.**

#### Problema

```java
// ❌ Sin Decorator: Explosión de subclases

interface Coffee {
  double cost();
  String description();
}

class SimpleCoffee implements Coffee {
  public double cost() { return 5.0; }
  public String description() { return "Simple coffee"; }
}

// Necesitas variantes: con leche, con azúcar, con crema...
class CoffeeWithMilk extends SimpleCoffee {
  public double cost() { return super.cost() + 1.5; }
  public String description() { return super.description() + ", milk"; }
}

class CoffeeWithSugar extends SimpleCoffee {
  public double cost() { return super.cost() + 0.5; }
  public String description() { return super.description() + ", sugar"; }
}

class CoffeeWithMilkAndSugar extends SimpleCoffee {
  public double cost() { return super.cost() + 2.0; }
  public String description() { return super.description() + ", milk, sugar"; }
}

// ❌ ¿Y si quieres milk + sugar + cream?
// ❌ ¿Y doble leche?
// ❌ ¿Y todas las combinaciones?
// → Explosión combinatoria de subclases!
```

#### Solución

```java
// ✅ Decorator: Composición dinámica

// Componente base
interface Coffee {
  double cost();
  String description();
}

// Componente concreto
class SimpleCoffee implements Coffee {
  @Override
  public double cost() {
    return 5.0;
  }

  @Override
  public String description() {
    return "Simple coffee";
  }
}

// Decorator abstracto
abstract class CoffeeDecorator implements Coffee {
  protected Coffee decoratedCoffee;

  public CoffeeDecorator(Coffee coffee) {
    this.decoratedCoffee = coffee;
  }

  @Override
  public double cost() {
    return decoratedCoffee.cost();
  }

  @Override
  public String description() {
    return decoratedCoffee.description();
  }
}

// Decoradores concretos
class MilkDecorator extends CoffeeDecorator {
  public MilkDecorator(Coffee coffee) {
    super(coffee);
  }

  @Override
  public double cost() {
    return super.cost() + 1.5;
  }

  @Override
  public String description() {
    return super.description() + ", milk";
  }
}

class SugarDecorator extends CoffeeDecorator {
  public SugarDecorator(Coffee coffee) {
    super(coffee);
  }

  @Override
  public double cost() {
    return super.cost() + 0.5;
  }

  @Override
  public String description() {
    return super.description() + ", sugar";
  }
}

class CreamDecorator extends CoffeeDecorator {
  public CreamDecorator(Coffee coffee) {
    super(coffee);
  }

  @Override
  public double cost() {
    return super.cost() + 2.0;
  }

  @Override
  public String description() {
    return super.description() + ", cream";
  }
}

// Uso: Composición dinámica
Coffee coffee = new SimpleCoffee();
System.out.println(coffee.description() + " = $" + coffee.cost());
// "Simple coffee = $5.0"

// Agregar leche
coffee = new MilkDecorator(coffee);
System.out.println(coffee.description() + " = $" + coffee.cost());
// "Simple coffee, milk = $6.5"

// Agregar azúcar
coffee = new SugarDecorator(coffee);
System.out.println(coffee.description() + " = $" + coffee.cost());
// "Simple coffee, milk, sugar = $7.0"

// Doble leche + crema + azúcar
Coffee fancyCoffee = new SimpleCoffee();
fancyCoffee = new MilkDecorator(fancyCoffee);
fancyCoffee = new MilkDecorator(fancyCoffee);  // Doble leche
fancyCoffee = new CreamDecorator(fancyCoffee);
fancyCoffee = new SugarDecorator(fancyCoffee);
System.out.println(fancyCoffee.description() + " = $" + fancyCoffee.cost());
// "Simple coffee, milk, milk, cream, sugar = $11.5"
```

#### Ejemplo Real: Logging Decorado

```java
// Componente base
interface DataSource {
  void writeData(String data);
  String readData();
}

// Implementación concreta
class FileDataSource implements DataSource {
  private String filename;

  public FileDataSource(String filename) {
    this.filename = filename;
  }

  @Override
  public void writeData(String data) {
    System.out.println("Writing to file: " + data);
  }

  @Override
  public String readData() {
    return "Data from file";
  }
}

// Decorator: Encriptación
class EncryptionDecorator implements DataSource {
  private DataSource wrappee;

  public EncryptionDecorator(DataSource source) {
    this.wrappee = source;
  }

  @Override
  public void writeData(String data) {
    String encrypted = encrypt(data);
    wrappee.writeData(encrypted);
  }

  @Override
  public String readData() {
    String data = wrappee.readData();
    return decrypt(data);
  }

  private String encrypt(String data) {
    return "ENCRYPTED(" + data + ")";
  }

  private String decrypt(String data) {
    return data.replace("ENCRYPTED(", "").replace(")", "");
  }
}

// Decorator: Compresión
class CompressionDecorator implements DataSource {
  private DataSource wrappee;

  public CompressionDecorator(DataSource source) {
    this.wrappee = source;
  }

  @Override
  public void writeData(String data) {
    String compressed = compress(data);
    wrappee.writeData(compressed);
  }

  @Override
  public String readData() {
    String data = wrappee.readData();
    return decompress(data);
  }

  private String compress(String data) {
    return "COMPRESSED(" + data + ")";
  }

  private String decompress(String data) {
    return data.replace("COMPRESSED(", "").replace(")", "");
  }
}

// Uso: Combinar decoradores
DataSource source = new FileDataSource("data.txt");

// Solo archivo
source.writeData("Hello World");

// Archivo + encriptación
source = new EncryptionDecorator(new FileDataSource("data.txt"));
source.writeData("Hello World");  // Guarda "ENCRYPTED(Hello World)"

// Archivo + compresión + encriptación
source = new EncryptionDecorator(
           new CompressionDecorator(
             new FileDataSource("data.txt")
           )
         );
source.writeData("Hello World");
// Guarda "ENCRYPTED(COMPRESSED(Hello World))"
```

#### Decorator en Java Streams

```java
// Java I/O usa Decorator extensivamente
InputStream fileStream = new FileInputStream("file.txt");
InputStream bufferedStream = new BufferedInputStream(fileStream);
InputStream gzipStream = new GZIPInputStream(bufferedStream);

// Cada capa agrega funcionalidad
```

#### Cuándo Usar Decorator

```
✓ Agregar responsabilidades dinámicamente
✓ Combinar funcionalidades de forma flexible
✓ Alternativa a explosión de subclases
✓ Cuando necesitas "wrappers" con funcionalidad extra

❌ NO usar para:
- Cuando la herencia simple es suficiente
- Cuando hay pocas variantes (2-3)
```

---

### 6.3 Facade

#### Definición

> **Proporciona una interfaz simplificada a un conjunto complejo de subsistemas.**

#### Problema

```java
// ❌ Sin Facade: Cliente debe interactuar con subsistemas complejos

// Subsistemas complejos
class CPU {
  public void freeze() { System.out.println("CPU: Freezing"); }
  public void jump(long position) { System.out.println("CPU: Jumping to " + position); }
  public void execute() { System.out.println("CPU: Executing"); }
}

class Memory {
  public void load(long position, byte[] data) {
    System.out.println("Memory: Loading data at " + position);
  }
}

class HardDrive {
  public byte[] read(long lba, int size) {
    System.out.println("HDD: Reading " + size + " bytes from " + lba);
    return new byte[size];
  }
}

// ❌ Cliente debe conocer todos los detalles
class Client {
  public void startComputer() {
    CPU cpu = new CPU();
    Memory memory = new Memory();
    HardDrive hd = new HardDrive();

    // Cliente necesita saber el proceso completo
    cpu.freeze();
    memory.load(0x00, hd.read(0x7C00, 512));
    cpu.jump(0x00);
    cpu.execute();
  }
}
```

#### Solución

```java
// ✅ Facade: Interfaz simplificada

class ComputerFacade {
  private CPU cpu;
  private Memory memory;
  private HardDrive hardDrive;

  public ComputerFacade() {
    this.cpu = new CPU();
    this.memory = new Memory();
    this.hardDrive = new HardDrive();
  }

  // Método simple que esconde la complejidad
  public void start() {
    cpu.freeze();
    memory.load(0x00, hardDrive.read(0x7C00, 512));
    cpu.jump(0x00);
    cpu.execute();
  }
}

// ✅ Cliente usa interfaz simple
class Client {
  public void startComputer() {
    ComputerFacade computer = new ComputerFacade();
    computer.start();  // ¡Simple!
  }
}
```

#### Ejemplo Real: Sistema de Home Theater

```java
// Subsistemas complejos
class Amplifier {
  public void on() { System.out.println("Amplifier ON"); }
  public void setVolume(int level) { System.out.println("Volume: " + level); }
  public void off() { System.out.println("Amplifier OFF"); }
}

class DVDPlayer {
  public void on() { System.out.println("DVD Player ON"); }
  public void play(String movie) { System.out.println("Playing: " + movie); }
  public void stop() { System.out.println("DVD stopped"); }
  public void off() { System.out.println("DVD Player OFF"); }
}

class Projector {
  public void on() { System.out.println("Projector ON"); }
  public void wideScreenMode() { System.out.println("Widescreen mode"); }
  public void off() { System.out.println("Projector OFF"); }
}

class Lights {
  public void dim(int level) { System.out.println("Lights dimmed to " + level + "%"); }
}

class Screen {
  public void down() { System.out.println("Screen down"); }
  public void up() { System.out.println("Screen up"); }
}

// ✅ Facade
class HomeTheaterFacade {
  private Amplifier amp;
  private DVDPlayer dvd;
  private Projector projector;
  private Lights lights;
  private Screen screen;

  public HomeTheaterFacade(Amplifier amp, DVDPlayer dvd, Projector projector,
                           Lights lights, Screen screen) {
    this.amp = amp;
    this.dvd = dvd;
    this.projector = projector;
    this.lights = lights;
    this.screen = screen;
  }

  public void watchMovie(String movie) {
    System.out.println("\n=== Get ready to watch a movie ===");
    lights.dim(10);
    screen.down();
    projector.on();
    projector.wideScreenMode();
    amp.on();
    amp.setVolume(5);
    dvd.on();
    dvd.play(movie);
  }

  public void endMovie() {
    System.out.println("\n=== Shutting down theater ===");
    lights.dim(100);
    screen.up();
    projector.off();
    amp.off();
    dvd.stop();
    dvd.off();
  }
}

// Uso
HomeTheaterFacade homeTheater = new HomeTheaterFacade(
  new Amplifier(),
  new DVDPlayer(),
  new Projector(),
  new Lights(),
  new Screen()
);

homeTheater.watchMovie("Inception");  // ¡Un método hace todo!
homeTheater.endMovie();
```

#### Cuándo Usar Facade

```
✓ Sistema complejo con muchos componentes
✓ Simplificar API complicada
✓ Desacoplar cliente de subsistemas
✓ Proporcionar puntos de entrada claros

❌ NO usar para:
- Sistemas ya simples
- Cuando el cliente necesita control fino
```

---

### 6.4 Proxy

#### Definición

> **Proporciona un sustituto o marcador de posición para otro objeto para controlar el acceso a él.**

#### Tipos de Proxy

```
1. Remote Proxy: Representa objeto en espacio de direcciones diferente
2. Virtual Proxy: Crea objetos costosos bajo demanda
3. Protection Proxy: Controla acceso (permisos)
4. Smart Proxy: Agrega funcionalidad extra (logging, caching)
```

#### Problema y Solución: Virtual Proxy

```java
// ❌ Sin Proxy: Carga inmediata de recursos costosos

class HighResolutionImage {
  private String filename;
  private byte[] imageData;

  public HighResolutionImage(String filename) {
    this.filename = filename;
    loadFromDisk();  // ❌ Carga inmediata (lento)
  }

  private void loadFromDisk() {
    System.out.println("Loading high-res image: " + filename);
    // Simula carga costosa
    try {
      Thread.sleep(3000);  // 3 segundos
    } catch (InterruptedException e) { }
    imageData = new byte[10000000];  // 10 MB
  }

  public void display() {
    System.out.println("Displaying: " + filename);
  }
}

// ❌ Problema: todas las imágenes se cargan al crear el objeto
class Gallery {
  private List<HighResolutionImage> images = new ArrayList<>();

  public Gallery() {
    images.add(new HighResolutionImage("photo1.jpg"));  // 3 segundos
    images.add(new HighResolutionImage("photo2.jpg"));  // 3 segundos
    images.add(new HighResolutionImage("photo3.jpg"));  // 3 segundos
    // Total: 9 segundos solo para crear la galería
    // Pero tal vez nunca se muestren todas
  }
}
```

```java
// ✅ Virtual Proxy: Carga bajo demanda (lazy loading)

interface Image {
  void display();
}

// Objeto real (costoso)
class RealImage implements Image {
  private String filename;

  public RealImage(String filename) {
    this.filename = filename;
    loadFromDisk();
  }

  private void loadFromDisk() {
    System.out.println("Loading: " + filename);
    try {
      Thread.sleep(3000);
    } catch (InterruptedException e) { }
  }

  @Override
  public void display() {
    System.out.println("Displaying: " + filename);
  }
}

// Proxy (ligero)
class ImageProxy implements Image {
  private String filename;
  private RealImage realImage;  // Carga diferida

  public ImageProxy(String filename) {
    this.filename = filename;
    // NO carga la imagen todavía
  }

  @Override
  public void display() {
    // Carga solo cuando se necesita
    if (realImage == null) {
      realImage = new RealImage(filename);
    }
    realImage.display();
  }
}

// Uso
List<Image> images = new ArrayList<>();
images.add(new ImageProxy("photo1.jpg"));  // Instantáneo
images.add(new ImageProxy("photo2.jpg"));  // Instantáneo
images.add(new ImageProxy("photo3.jpg"));  // Instantáneo
// Total: < 1 segundo

// Solo carga cuando se muestra
images.get(0).display();  // Ahora SÍ carga photo1.jpg (3 seg)
images.get(0).display();  // Ya está cargado (rápido)
```

#### Protection Proxy

```java
// ✅ Protection Proxy: Control de acceso

interface Document {
  void view();
  void edit();
  void delete();
}

class RealDocument implements Document {
  private String content;

  public RealDocument(String content) {
    this.content = content;
  }

  @Override
  public void view() {
    System.out.println("Viewing: " + content);
  }

  @Override
  public void edit() {
    System.out.println("Editing document");
  }

  @Override
  public void delete() {
    System.out.println("Deleting document");
  }
}

class ProtectedDocumentProxy implements Document {
  private RealDocument realDocument;
  private String userRole;

  public ProtectedDocumentProxy(String content, String userRole) {
    this.realDocument = new RealDocument(content);
    this.userRole = userRole;
  }

  @Override
  public void view() {
    // Todos pueden ver
    realDocument.view();
  }

  @Override
  public void edit() {
    // Solo EDITOR y ADMIN pueden editar
    if (userRole.equals("EDITOR") || userRole.equals("ADMIN")) {
      realDocument.edit();
    } else {
      System.out.println("Access denied: You cannot edit");
    }
  }

  @Override
  public void delete() {
    // Solo ADMIN puede eliminar
    if (userRole.equals("ADMIN")) {
      realDocument.delete();
    } else {
      System.out.println("Access denied: You cannot delete");
    }
  }
}

// Uso
Document userDoc = new ProtectedDocumentProxy("Secret data", "USER");
userDoc.view();    // ✓ OK
userDoc.edit();    // ✗ Access denied
userDoc.delete();  // ✗ Access denied

Document adminDoc = new ProtectedDocumentProxy("Secret data", "ADMIN");
adminDoc.view();    // ✓ OK
adminDoc.edit();    // ✓ OK
adminDoc.delete();  // ✓ OK
```

#### Smart Proxy (Caching)

```java
// ✅ Smart Proxy: Caching

interface DatabaseQuery {
  List<User> getUsers();
}

class RealDatabase implements DatabaseQuery {
  @Override
  public List<User> getUsers() {
    System.out.println("Querying database (slow)...");
    // Simulación de query lento
    try {
      Thread.sleep(2000);
    } catch (InterruptedException e) { }
    return Arrays.asList(new User("John"), new User("Jane"));
  }
}

class CachingDatabaseProxy implements DatabaseQuery {
  private RealDatabase realDatabase;
  private List<User> cachedUsers;
  private long cacheTime;
  private static final long CACHE_DURATION = 60000;  // 1 minuto

  public CachingDatabaseProxy() {
    this.realDatabase = new RealDatabase();
  }

  @Override
  public List<User> getUsers() {
    long currentTime = System.currentTimeMillis();

    // Si el cache es válido, retornar desde cache
    if (cachedUsers != null && (currentTime - cacheTime) < CACHE_DURATION) {
      System.out.println("Returning from cache (fast)");
      return cachedUsers;
    }

    // Sino, consultar BD y cachear
    cachedUsers = realDatabase.getUsers();
    cacheTime = currentTime;
    return cachedUsers;
  }
}

// Uso
DatabaseQuery db = new CachingDatabaseProxy();

db.getUsers();  // Query a BD (2 segundos)
db.getUsers();  // Desde cache (instantáneo)
db.getUsers();  // Desde cache (instantáneo)
```

#### Cuándo Usar Proxy

```
Virtual Proxy:
✓ Objetos costosos de crear
✓ Lazy loading
✓ Optimización de recursos

Protection Proxy:
✓ Control de acceso
✓ Permisos y roles
✓ Seguridad

Smart Proxy:
✓ Caching
✓ Logging
✓ Reference counting
```

---

## 7. Patrones de Comportamiento

### 7.1 Strategy

#### Definición

> **Define una familia de algoritmos, encapsula cada uno y los hace intercambiables. Permite que el algoritmo varíe independientemente de los clientes que lo usan.**

#### Problema

```java
// ❌ Sin Strategy: Lógica condicional

class PaymentService {
  public void processPayment(String paymentType, double amount) {
    if (paymentType.equals("CREDIT_CARD")) {
      System.out.println("Processing credit card payment: $" + amount);
      // Lógica específica de tarjeta
      validateCard();
      chargeCreditCard(amount);
    } else if (paymentType.equals("PAYPAL")) {
      System.out.println("Processing PayPal payment: $" + amount);
      // Lógica específica de PayPal
      authenticatePayPal();
      chargePayPal(amount);
    } else if (paymentType.equals("BITCOIN")) {
      System.out.println("Processing Bitcoin payment: $" + amount);
      // Lógica específica de Bitcoin
      validateWallet();
      transferBitcoin(amount);
    }
    // ❌ Agregar nuevo método = modificar esta clase (viola OCP)
  }
}
```

#### Solución

```java
// ✅ Strategy: Algoritmos intercambiables

// Estrategia (interface)
interface PaymentStrategy {
  void pay(double amount);
}

// Estrategias concretas
class CreditCardStrategy implements PaymentStrategy {
  private String cardNumber;
  private String cvv;

  public CreditCardStrategy(String cardNumber, String cvv) {
    this.cardNumber = cardNumber;
    this.cvv = cvv;
  }

  @Override
  public void pay(double amount) {
    System.out.println("Paying $" + amount + " with credit card " + cardNumber);
    // Lógica específica de tarjeta
  }
}

class PayPalStrategy implements PaymentStrategy {
  private String email;

  public PayPalStrategy(String email) {
    this.email = email;
  }

  @Override
  public void pay(double amount) {
    System.out.println("Paying $" + amount + " with PayPal account " + email);
    // Lógica específica de PayPal
  }
}

class BitcoinStrategy implements PaymentStrategy {
  private String walletAddress;

  public BitcoinStrategy(String walletAddress) {
    this.walletAddress = walletAddress;
  }

  @Override
  public void pay(double amount) {
    System.out.println("Paying $" + amount + " with Bitcoin wallet " + walletAddress);
    // Lógica específica de Bitcoin
  }
}

// Contexto
class ShoppingCart {
  private PaymentStrategy paymentStrategy;

  public void setPaymentStrategy(PaymentStrategy strategy) {
    this.paymentStrategy = strategy;
  }

  public void checkout(double amount) {
    if (paymentStrategy == null) {
      throw new IllegalStateException("Payment method not set");
    }
    paymentStrategy.pay(amount);
  }
}

// Uso
ShoppingCart cart = new ShoppingCart();

// Pagar con tarjeta
cart.setPaymentStrategy(new CreditCardStrategy("1234-5678-9012-3456", "123"));
cart.checkout(100.00);

// Cambiar a PayPal
cart.setPaymentStrategy(new PayPalStrategy("user@example.com"));
cart.checkout(50.00);

// Cambiar a Bitcoin
cart.setPaymentStrategy(new BitcoinStrategy("1A2B3C4D5E6F"));
cart.checkout(75.00);
```

#### Ejemplo Real: Ordenamiento

```java
// Estrategias de ordenamiento
interface SortStrategy {
  void sort(int[] array);
}

class BubbleSortStrategy implements SortStrategy {
  @Override
  public void sort(int[] array) {
    System.out.println("Sorting using Bubble Sort");
    // Implementación de bubble sort
  }
}

class QuickSortStrategy implements SortStrategy {
  @Override
  public void sort(int[] array) {
    System.out.println("Sorting using Quick Sort");
    // Implementación de quick sort
  }
}

class MergeSortStrategy implements SortStrategy {
  @Override
  public void sort(int[] array) {
    System.out.println("Sorting using Merge Sort");
    // Implementación de merge sort
  }
}

// Contexto
class Sorter {
  private SortStrategy strategy;

  public Sorter(SortStrategy strategy) {
    this.strategy = strategy;
  }

  public void setStrategy(SortStrategy strategy) {
    this.strategy = strategy;
  }

  public void sort(int[] array) {
    strategy.sort(array);
  }
}

// Uso
int[] data = {5, 2, 8, 1, 9};

Sorter sorter = new Sorter(new QuickSortStrategy());
sorter.sort(data);  // Usa Quick Sort

sorter.setStrategy(new MergeSortStrategy());
sorter.sort(data);  // Usa Merge Sort
```

#### Cuándo Usar Strategy

```
✓ Múltiples algoritmos relacionados
✓ Necesitas cambiar algoritmo en runtime
✓ Eliminar condicionales complejos (if/switch)
✓ Diferentes variantes de comportamiento

❌ NO usar para:
- Solo 1-2 variantes simples
- Algoritmo nunca cambia
```

#### Strategy y SOLID

```
✓ Cumple OCP (extensible sin modificar)
✓ Cumple SRP (cada estrategia una responsabilidad)
✓ Cumple DIP (depende de abstracción)
```

---

### 7.2 Observer

#### Definición

> **Define una dependencia uno-a-muchos entre objetos, de modo que cuando un objeto cambia de estado, todos sus dependientes son notificados y actualizados automáticamente.**

#### Problema

```java
// ❌ Sin Observer: Acoplamiento fuerte

class WeatherStation {
  private double temperature;

  public void setTemperature(double temp) {
    this.temperature = temp;

    // ❌ Acoplado a displays específicos
    CurrentConditionsDisplay display1 = new CurrentConditionsDisplay();
    display1.update(temperature);

    StatisticsDisplay display2 = new StatisticsDisplay();
    display2.update(temperature);

    ForecastDisplay display3 = new ForecastDisplay();
    display3.update(temperature);

    // ❌ Agregar nuevo display = modificar WeatherStation
  }
}
```

#### Solución

```java
// ✅ Observer: Desacoplamiento

// Observer interface
interface Observer {
  void update(double temperature, double humidity, double pressure);
}

// Subject interface
interface Subject {
  void registerObserver(Observer o);
  void removeObserver(Observer o);
  void notifyObservers();
}

// Subject concreto
class WeatherStation implements Subject {
  private List<Observer> observers;
  private double temperature;
  private double humidity;
  private double pressure;

  public WeatherStation() {
    observers = new ArrayList<>();
  }

  @Override
  public void registerObserver(Observer o) {
    observers.add(o);
  }

  @Override
  public void removeObserver(Observer o) {
    observers.remove(o);
  }

  @Override
  public void notifyObservers() {
    for (Observer observer : observers) {
      observer.update(temperature, humidity, pressure);
    }
  }

  public void setMeasurements(double temperature, double humidity, double pressure) {
    this.temperature = temperature;
    this.humidity = humidity;
    this.pressure = pressure;
    notifyObservers();  // Notifica a todos los observadores
  }
}

// Observers concretos
class CurrentConditionsDisplay implements Observer {
  @Override
  public void update(double temperature, double humidity, double pressure) {
    System.out.println("Current conditions: " + temperature + "°C, " + humidity + "% humidity");
  }
}

class StatisticsDisplay implements Observer {
  private double totalTemp = 0;
  private int numReadings = 0;

  @Override
  public void update(double temperature, double humidity, double pressure) {
    totalTemp += temperature;
    numReadings++;
    double avgTemp = totalTemp / numReadings;
    System.out.println("Avg/Max/Min temperature: " + avgTemp + "/TODO/TODO");
  }
}

class ForecastDisplay implements Observer {
  private double lastPressure = 0;

  @Override
  public void update(double temperature, double humidity, double pressure) {
    String forecast;
    if (pressure > lastPressure) {
      forecast = "Improving weather on the way!";
    } else if (pressure < lastPressure) {
      forecast = "Watch out for cooler, rainy weather";
    } else {
      forecast = "More of the same";
    }
    System.out.println("Forecast: " + forecast);
    lastPressure = pressure;
  }
}

// Uso
WeatherStation weatherStation = new WeatherStation();

// Registrar observadores
CurrentConditionsDisplay currentDisplay = new CurrentConditionsDisplay();
StatisticsDisplay statsDisplay = new StatisticsDisplay();
ForecastDisplay forecastDisplay = new ForecastDisplay();

weatherStation.registerObserver(currentDisplay);
weatherStation.registerObserver(statsDisplay);
weatherStation.registerObserver(forecastDisplay);

// Cambio de estado → notifica a todos
weatherStation.setMeasurements(25.0, 65.0, 1013.0);
// Todos los displays se actualizan automáticamente

// Nuevo cambio
weatherStation.setMeasurements(26.5, 70.0, 1012.0);

// Desregistrar un observador
weatherStation.removeObserver(forecastDisplay);
weatherStation.setMeasurements(28.0, 75.0, 1011.0);
// forecastDisplay ya no se actualiza
```

#### Ejemplo Real: Event Bus

```java
// ✅ Observer con Event Bus

interface EventListener {
  void onEvent(Event event);
}

class Event {
  private String type;
  private Object data;

  public Event(String type, Object data) {
    this.type = type;
    this.data = data;
  }

  public String getType() { return type; }
  public Object getData() { return data; }
}

class EventBus {
  private Map<String, List<EventListener>> listeners = new HashMap<>();

  public void subscribe(String eventType, EventListener listener) {
    listeners.computeIfAbsent(eventType, k -> new ArrayList<>()).add(listener);
  }

  public void unsubscribe(String eventType, EventListener listener) {
    List<EventListener> eventListeners = listeners.get(eventType);
    if (eventListeners != null) {
      eventListeners.remove(listener);
    }
  }

  public void publish(Event event) {
    List<EventListener> eventListeners = listeners.get(event.getType());
    if (eventListeners != null) {
      for (EventListener listener : eventListeners) {
        listener.onEvent(event);
      }
    }
  }
}

// Listeners
class EmailService implements EventListener {
  @Override
  public void onEvent(Event event) {
    if (event.getType().equals("USER_REGISTERED")) {
      System.out.println("Sending welcome email to " + event.getData());
    }
  }
}

class LoggingService implements EventListener {
  @Override
  public void onEvent(Event event) {
    System.out.println("LOG: " + event.getType() + " - " + event.getData());
  }
}

class AnalyticsService implements EventListener {
  @Override
  public void onEvent(Event event) {
    if (event.getType().equals("USER_REGISTERED")) {
      System.out.println("Tracking new user registration");
    }
  }
}

// Uso
EventBus eventBus = new EventBus();

// Suscribir servicios
eventBus.subscribe("USER_REGISTERED", new EmailService());
eventBus.subscribe("USER_REGISTERED", new LoggingService());
eventBus.subscribe("USER_REGISTERED", new AnalyticsService());

// Publicar evento
eventBus.publish(new Event("USER_REGISTERED", "john@example.com"));
// Todos los servicios suscritos son notificados
```

#### Observer en Java

```java
// Java tiene soporte nativo (aunque deprecated en Java 9+)
import java.util.Observable;
import java.util.Observer;

// Mejor usar: java.beans.PropertyChangeListener
// O librerías como RxJava, Spring Events
```

#### Cuándo Usar Observer

```
✓ Un objeto necesita notificar a otros sin conocerlos
✓ Sistema de eventos
✓ Model-View separation (MVC, MVVM)
✓ Pub/Sub systems

❌ NO usar para:
- Relaciones simples 1:1
- Cuando el orden de notificación importa mucho
```

---

### 7.3 Command

#### Definición

> **Encapsula una request como un objeto, permitiendo parametrizar clientes con diferentes requests, encolar requests, y soportar operaciones deshacer/rehacer.**

#### Problema

```java
// ❌ Sin Command: Lógica acoplada

class RemoteControl {
  private Light light;

  public RemoteControl(Light light) {
    this.light = light;
  }

  public void pressButton() {
    // ❌ Lógica hardcoded
    light.turnOn();
  }
}

// ❌ ¿Cómo hacer undo?
// ❌ ¿Cómo cambiar qué hace el botón?
// ❌ ¿Cómo guardar historial de comandos?
```

#### Solución

```java
// ✅ Command: Encapsula requests

// Command interface
interface Command {
  void execute();
  void undo();
}

// Receiver
class Light {
  public void turnOn() {
    System.out.println("Light is ON");
  }

  public void turnOff() {
    System.out.println("Light is OFF");
  }
}

// Concrete Commands
class LightOnCommand implements Command {
  private Light light;

  public LightOnCommand(Light light) {
    this.light = light;
  }

  @Override
  public void execute() {
    light.turnOn();
  }

  @Override
  public void undo() {
    light.turnOff();
  }
}

class LightOffCommand implements Command {
  private Light light;

  public LightOffCommand(Light light) {
    this.light = light;
  }

  @Override
  public void execute() {
    light.turnOff();
  }

  @Override
  public void undo() {
    light.turnOn();
  }
}

// Invoker
class RemoteControl {
  private Command command;
  private Stack<Command> history = new Stack<>();

  public void setCommand(Command command) {
    this.command = command;
  }

  public void pressButton() {
    command.execute();
    history.push(command);
  }

  public void pressUndo() {
    if (!history.isEmpty()) {
      Command lastCommand = history.pop();
      lastCommand.undo();
    }
  }
}

// Uso
Light livingRoomLight = new Light();

Command lightOn = new LightOnCommand(livingRoomLight);
Command lightOff = new LightOffCommand(livingRoomLight);

RemoteControl remote = new RemoteControl();

remote.setCommand(lightOn);
remote.pressButton();  // Light is ON

remote.setCommand(lightOff);
remote.pressButton();  // Light is OFF

remote.pressUndo();    // Light is ON (undo)
remote.pressUndo();    // Light is OFF (undo)
```

#### Ejemplo Real: Editor de Texto

```java
// Receiver
class TextEditor {
  private StringBuilder text = new StringBuilder();

  public void write(String str) {
    text.append(str);
  }

  public void delete(int length) {
    int start = text.length() - length;
    if (start >= 0) {
      text.delete(start, text.length());
    }
  }

  public String getText() {
    return text.toString();
  }
}

// Commands
class WriteCommand implements Command {
  private TextEditor editor;
  private String textToWrite;

  public WriteCommand(TextEditor editor, String text) {
    this.editor = editor;
    this.textToWrite = text;
  }

  @Override
  public void execute() {
    editor.write(textToWrite);
  }

  @Override
  public void undo() {
    editor.delete(textToWrite.length());
  }
}

// Invoker con historial
class Editor {
  private Stack<Command> undoStack = new Stack<>();
  private Stack<Command> redoStack = new Stack<>();

  public void executeCommand(Command command) {
    command.execute();
    undoStack.push(command);
    redoStack.clear();  // Clear redo stack on new command
  }

  public void undo() {
    if (!undoStack.isEmpty()) {
      Command command = undoStack.pop();
      command.undo();
      redoStack.push(command);
    }
  }

  public void redo() {
    if (!redoStack.isEmpty()) {
      Command command = redoStack.pop();
      command.execute();
      undoStack.push(command);
    }
  }
}

// Uso
TextEditor textEditor = new TextEditor();
Editor editor = new Editor();

editor.executeCommand(new WriteCommand(textEditor, "Hello "));
editor.executeCommand(new WriteCommand(textEditor, "World!"));
System.out.println(textEditor.getText());  // "Hello World!"

editor.undo();
System.out.println(textEditor.getText());  // "Hello "

editor.redo();
System.out.println(textEditor.getText());  // "Hello World!"
```

#### Command con Macro

```java
// Macro Command: ejecuta múltiples comandos
class MacroCommand implements Command {
  private List<Command> commands;

  public MacroCommand(List<Command> commands) {
    this.commands = commands;
  }

  @Override
  public void execute() {
    for (Command command : commands) {
      command.execute();
    }
  }

  @Override
  public void undo() {
    // Undo en orden inverso
    for (int i = commands.size() - 1; i >= 0; i--) {
      commands.get(i).undo();
    }
  }
}

// Uso
Light light1 = new Light();
Light light2 = new Light();

List<Command> allLightsOn = Arrays.asList(
  new LightOnCommand(light1),
  new LightOnCommand(light2)
);

Command macro = new MacroCommand(allLightsOn);
macro.execute();  // Enciende ambas luces
macro.undo();     // Apaga ambas luces
```

#### Cuándo Usar Command

```
✓ Undo/Redo functionality
✓ Encolar operaciones
✓ Logging de operaciones
✓ Transacciones
✓ Macros
✓ Menús y botones en UI

❌ NO usar para:
- Operaciones simples sin necesidad de deshacer
- Cuando no necesitas historial
```

---

### 7.4 Template Method

#### Definición

> **Define el esqueleto de un algoritmo en una operación, delegando algunos pasos a las subclases. Permite que las subclases redefinan ciertos pasos sin cambiar la estructura del algoritmo.**

#### Problema

```java
// ❌ Sin Template Method: Código duplicado

class TeaMaker {
  public void makeTea() {
    boilWater();
    steepTeaBag();
    pourInCup();
    addLemon();
  }

  private void boilWater() {
    System.out.println("Boiling water");
  }

  private void steepTeaBag() {
    System.out.println("Steeping tea bag");
  }

  private void pourInCup() {
    System.out.println("Pouring into cup");
  }

  private void addLemon() {
    System.out.println("Adding lemon");
  }
}

class CoffeeMaker {
  public void makeCoffee() {
    boilWater();         // ← Duplicado
    brewCoffeeGrinds();
    pourInCup();         // ← Duplicado
    addSugarAndMilk();
  }

  private void boilWater() {
    System.out.println("Boiling water");  // ← Mismo código
  }

  private void brewCoffeeGrinds() {
    System.out.println("Brewing coffee grinds");
  }

  private void pourInCup() {
    System.out.println("Pouring into cup");  // ← Mismo código
  }

  private void addSugarAndMilk() {
    System.out.println("Adding sugar and milk");
  }
}
```

#### Solución

```java
// ✅ Template Method: Esqueleto común, detalles en subclases

abstract class Beverage {
  // Template method (final para no override)
  public final void prepare() {
    boilWater();
    brew();
    pourInCup();
    if (customerWantsCondiments()) {
      addCondiments();
    }
  }

  // Pasos comunes (implementados en base)
  private void boilWater() {
    System.out.println("Boiling water");
  }

  private void pourInCup() {
    System.out.println("Pouring into cup");
  }

  // Pasos abstractos (subclases deben implementar)
  protected abstract void brew();
  protected abstract void addCondiments();

  // Hook method (opcional, puede ser overrideado)
  protected boolean customerWantsCondiments() {
    return true;
  }
}

class Tea extends Beverage {
  @Override
  protected void brew() {
    System.out.println("Steeping tea bag");
  }

  @Override
  protected void addCondiments() {
    System.out.println("Adding lemon");
  }
}

class Coffee extends Beverage {
  @Override
  protected void brew() {
    System.out.println("Brewing coffee grinds");
  }

  @Override
  protected void addCondiments() {
    System.out.println("Adding sugar and milk");
  }

  @Override
  protected boolean customerWantsCondiments() {
    // Hook: algunas personas no quieren leche/azúcar
    return askCustomer();
  }

  private boolean askCustomer() {
    // En realidad preguntaría al cliente
    return true;
  }
}

// Uso
Beverage tea = new Tea();
tea.prepare();
// Boiling water
// Steeping tea bag
// Pouring into cup
// Adding lemon

System.out.println();

Beverage coffee = new Coffee();
coffee.prepare();
// Boiling water
// Brewing coffee grinds
// Pouring into cup
// Adding sugar and milk
```

#### Ejemplo Real: Procesamiento de Documentos

```java
abstract class DocumentProcessor {
  // Template method
  public final void processDocument() {
    openDocument();
    parseContent();
    if (needsFormatting()) {
      formatContent();
    }
    saveDocument();
    closeDocument();
  }

  // Pasos comunes
  private void openDocument() {
    System.out.println("Opening document");
  }

  private void closeDocument() {
    System.out.println("Closing document");
  }

  // Pasos abstractos
  protected abstract void parseContent();
  protected abstract void formatContent();
  protected abstract void saveDocument();

  // Hook
  protected boolean needsFormatting() {
    return true;
  }
}

class PDFProcessor extends DocumentProcessor {
  @Override
  protected void parseContent() {
    System.out.println("Parsing PDF content");
  }

  @Override
  protected void formatContent() {
    System.out.println("Formatting PDF");
  }

  @Override
  protected void saveDocument() {
    System.out.println("Saving as PDF");
  }
}

class WordProcessor extends DocumentProcessor {
  @Override
  protected void parseContent() {
    System.out.println("Parsing Word content");
  }

  @Override
  protected void formatContent() {
    System.out.println("Formatting Word document");
  }

  @Override
  protected void saveDocument() {
    System.out.println("Saving as DOCX");
  }
}

class PlainTextProcessor extends DocumentProcessor {
  @Override
  protected void parseContent() {
    System.out.println("Parsing plain text");
  }

  @Override
  protected void formatContent() {
    System.out.println("No formatting needed for plain text");
  }

  @Override
  protected void saveDocument() {
    System.out.println("Saving as TXT");
  }

  @Override
  protected boolean needsFormatting() {
    return false;  // Plain text no necesita formato
  }
}
```

#### Template Method vs Strategy

```
Template Method:
- Usa herencia
- Algoritmo en clase base
- Pasos variables en subclases
- Estructura fija

Strategy:
- Usa composición
- Algoritmo completo intercambiable
- Cambio en runtime
- Más flexible
```

#### Cuándo Usar Template Method

```
✓ Algoritmo con pasos comunes y variables
✓ Evitar duplicación de código
✓ Control de puntos de extensión (hooks)
✓ Framework design

❌ NO usar para:
- Cuando necesitas cambiar algoritmo en runtime (usa Strategy)
- Cuando hay muchas variaciones (explosión de subclases)
```

---

### 7.5 State

#### Definición

> **Permite a un objeto alterar su comportamiento cuando su estado interno cambia. El objeto parecerá cambiar su clase.**

#### Problema

```java
// ❌ Sin State: Lógica condicional compleja

class VendingMachine {
  private static final int NO_COIN = 0;
  private static final int HAS_COIN = 1;
  private static final int SOLD = 2;
  private static final int SOLD_OUT = 3;

  private int state = SOLD_OUT;
  private int count = 0;

  public VendingMachine(int count) {
    this.count = count;
    if (count > 0) {
      state = NO_COIN;
    }
  }

  public void insertCoin() {
    if (state == HAS_COIN) {
      System.out.println("You can't insert another coin");
    } else if (state == NO_COIN) {
      state = HAS_COIN;
      System.out.println("Coin inserted");
    } else if (state == SOLD_OUT) {
      System.out.println("Machine is sold out");
    } else if (state == SOLD) {
      System.out.println("Please wait, we're already giving you a product");
    }
  }

  public void ejectCoin() {
    if (state == HAS_COIN) {
      System.out.println("Coin returned");
      state = NO_COIN;
    } else if (state == NO_COIN) {
      System.out.println("You haven't inserted a coin");
    } else if (state == SOLD) {
      System.out.println("Sorry, you already turned the crank");
    } else if (state == SOLD_OUT) {
      System.out.println("You can't eject, you haven't inserted a coin yet");
    }
  }

  public void turnCrank() {
    if (state == SOLD) {
      System.out.println("Turning twice doesn't get you another product");
    } else if (state == NO_COIN) {
      System.out.println("You need to insert a coin first");
    } else if (state == SOLD_OUT) {
      System.out.println("There are no products");
    } else if (state == HAS_COIN) {
      System.out.println("You turned...");
      state = SOLD;
      dispense();
    }
  }

  private void dispense() {
    if (state == SOLD) {
      System.out.println("A product comes rolling out");
      count--;
      if (count == 0) {
        System.out.println("Out of products");
        state = SOLD_OUT;
      } else {
        state = NO_COIN;
      }
    } else {
      System.out.println("ERROR");
    }
  }
  // ❌ Cada método tiene 4 if/else
  // ❌ Agregar nuevo estado = modificar TODOS los métodos
  // ❌ Difícil de mantener y entender
}
```

#### Solución

```java
// ✅ State: Encapsular estados en objetos

// State interface
interface State {
  void insertCoin();
  void ejectCoin();
  void turnCrank();
  void dispense();
}

// Context
class VendingMachine {
  private State noCoinState;
  private State hasCoinState;
  private State soldState;
  private State soldOutState;

  private State currentState;
  private int count;

  public VendingMachine(int count) {
    this.count = count;

    noCoinState = new NoCoinState(this);
    hasCoinState = new HasCoinState(this);
    soldState = new SoldState(this);
    soldOutState = new SoldOutState(this);

    if (count > 0) {
      currentState = noCoinState;
    } else {
      currentState = soldOutState;
    }
  }

  public void insertCoin() {
    currentState.insertCoin();
  }

  public void ejectCoin() {
    currentState.ejectCoin();
  }

  public void turnCrank() {
    currentState.turnCrank();
    currentState.dispense();
  }

  void setState(State state) {
    this.currentState = state;
  }

  void releaseProduct() {
    System.out.println("A product comes rolling out");
    if (count > 0) {
      count--;
    }
  }

  int getCount() {
    return count;
  }

  State getNoCoinState() { return noCoinState; }
  State getHasCoinState() { return hasCoinState; }
  State getSoldState() { return soldState; }
  State getSoldOutState() { return soldOutState; }
}

// Concrete States
class NoCoinState implements State {
  private VendingMachine machine;

  public NoCoinState(VendingMachine machine) {
    this.machine = machine;
  }

  @Override
  public void insertCoin() {
    System.out.println("Coin inserted");
    machine.setState(machine.getHasCoinState());
  }

  @Override
  public void ejectCoin() {
    System.out.println("You haven't inserted a coin");
  }

  @Override
  public void turnCrank() {
    System.out.println("You need to insert a coin first");
  }

  @Override
  public void dispense() {
    System.out.println("You need to pay first");
  }
}

class HasCoinState implements State {
  private VendingMachine machine;

  public HasCoinState(VendingMachine machine) {
    this.machine = machine;
  }

  @Override
  public void insertCoin() {
    System.out.println("You can't insert another coin");
  }

  @Override
  public void ejectCoin() {
    System.out.println("Coin returned");
    machine.setState(machine.getNoCoinState());
  }

  @Override
  public void turnCrank() {
    System.out.println("You turned...");
    machine.setState(machine.getSoldState());
  }

  @Override
  public void dispense() {
    System.out.println("No product dispensed");
  }
}

class SoldState implements State {
  private VendingMachine machine;

  public SoldState(VendingMachine machine) {
    this.machine = machine;
  }

  @Override
  public void insertCoin() {
    System.out.println("Please wait, we're already giving you a product");
  }

  @Override
  public void ejectCoin() {
    System.out.println("Sorry, you already turned the crank");
  }

  @Override
  public void turnCrank() {
    System.out.println("Turning twice doesn't get you another product");
  }

  @Override
  public void dispense() {
    machine.releaseProduct();
    if (machine.getCount() > 0) {
      machine.setState(machine.getNoCoinState());
    } else {
      System.out.println("Out of products");
      machine.setState(machine.getSoldOutState());
    }
  }
}

class SoldOutState implements State {
  private VendingMachine machine;

  public SoldOutState(VendingMachine machine) {
    this.machine = machine;
  }

  @Override
  public void insertCoin() {
    System.out.println("Machine is sold out");
  }

  @Override
  public void ejectCoin() {
    System.out.println("You can't eject, you haven't inserted a coin yet");
  }

  @Override
  public void turnCrank() {
    System.out.println("There are no products");
  }

  @Override
  public void dispense() {
    System.out.println("No product to dispense");
  }
}

// Uso
VendingMachine machine = new VendingMachine(2);

machine.insertCoin();    // "Coin inserted"
machine.turnCrank();     // "You turned..." + product dispensed

machine.insertCoin();    // "Coin inserted"
machine.ejectCoin();     // "Coin returned"

machine.insertCoin();    // "Coin inserted"
machine.turnCrank();     // Product dispensed, machine sold out
```

#### Cuándo Usar State

```
✓ Objeto con estados complejos
✓ Comportamiento cambia según estado
✓ Múltiples condicionales basados en estado
✓ Máquinas de estado

❌ NO usar para:
- 2-3 estados simples
- Comportamiento no cambia mucho por estado
```

---

## 8. Patrones Arquitectónicos

### 8.1 Repository

#### Definición

> **Encapsula la lógica de acceso a datos, proporcionando una interfaz similar a una colección para acceder a objetos de dominio.**

#### Sin Repository

```java
// ❌ Lógica de acceso a datos mezclada con lógica de negocio
class UserService {
  public void registerUser(User user) {
    // Lógica de negocio
    if (user.getAge() < 18) {
      throw new IllegalArgumentException("Must be 18+");
    }

    // ❌ Código de BD directo en el servicio
    try {
      Connection conn = DriverManager.getConnection("jdbc:mysql://localhost/db");
      PreparedStatement stmt = conn.prepareStatement(
        "INSERT INTO users (name, email, age) VALUES (?, ?, ?)"
      );
      stmt.setString(1, user.getName());
      stmt.setString(2, user.getEmail());
      stmt.setInt(3, user.getAge());
      stmt.executeUpdate();
      conn.close();
    } catch (SQLException e) {
      e.printStackTrace();
    }
  }
}
```

#### Con Repository

```java
// ✅ Repository: Separación de concerns

// Interface del repository
interface UserRepository {
  void save(User user);
  User findById(Long id);
  List<User> findAll();
  void delete(User user);
}

// Implementación concreta
class MySQLUserRepository implements UserRepository {
  private Connection connection;

  public MySQLUserRepository(Connection connection) {
    this.connection = connection;
  }

  @Override
  public void save(User user) {
    try {
      PreparedStatement stmt = connection.prepareStatement(
        "INSERT INTO users (name, email, age) VALUES (?, ?, ?)"
      );
      stmt.setString(1, user.getName());
      stmt.setString(2, user.getEmail());
      stmt.setInt(3, user.getAge());
      stmt.executeUpdate();
    } catch (SQLException e) {
      throw new RuntimeException("Error saving user", e);
    }
  }

  @Override
  public User findById(Long id) {
    // Implementación de consulta
    return null;  // Simplificado
  }

  @Override
  public List<User> findAll() {
    // Implementación de consulta
    return new ArrayList<>();  // Simplificado
  }

  @Override
  public void delete(User user) {
    // Implementación de eliminación
  }
}

// Servicio usa el repository (DIP)
class UserService {
  private UserRepository userRepository;

  public UserService(UserRepository userRepository) {
    this.userRepository = userRepository;
  }

  public void registerUser(User user) {
    // Solo lógica de negocio
    if (user.getAge() < 18) {
      throw new IllegalArgumentException("Must be 18+");
    }

    // Delega persistencia al repository
    userRepository.save(user);
  }
}
```

#### Repository con Especificaciones

```java
// Especificación (Specification pattern)
interface Specification<T> {
  boolean isSatisfiedBy(T item);
}

class AgeAboveSpecification implements Specification<User> {
  private int minAge;

  public AgeAboveSpecification(int minAge) {
    this.minAge = minAge;
  }

  @Override
  public boolean isSatisfiedBy(User user) {
    return user.getAge() >= minAge;
  }
}

// Repository con búsqueda por especificación
interface UserRepository {
  List<User> find(Specification<User> spec);
}

// Uso
UserRepository repo = new MySQLUserRepository();
List<User> adults = repo.find(new AgeAboveSpecification(18));
```

---

### 8.2 Dependency Injection (DI)

#### Sin DI

```java
// ❌ Dependencias hardcoded
class OrderService {
  private EmailService emailService;
  private PaymentProcessor paymentProcessor;

  public OrderService() {
    // ❌ Creación directa (acoplamiento fuerte)
    this.emailService = new GmailService();
    this.paymentProcessor = new StripeProcessor();
  }

  public void placeOrder(Order order) {
    paymentProcessor.process(order.getPayment());
    emailService.send(order.getCustomer().getEmail(), "Order confirmed");
  }
}

// ❌ Imposible cambiar implementaciones
// ❌ Imposible testear con mocks
```

#### Con DI

```java
// ✅ Constructor Injection
class OrderService {
  private EmailService emailService;
  private PaymentProcessor paymentProcessor;

  // ✓ Dependencias inyectadas
  public OrderService(EmailService emailService,
                      PaymentProcessor paymentProcessor) {
    this.emailService = emailService;
    this.paymentProcessor = paymentProcessor;
  }

  public void placeOrder(Order order) {
    paymentProcessor.process(order.getPayment());
    emailService.send(order.getCustomer().getEmail(), "Order confirmed");
  }
}

// Composición manual
EmailService emailService = new GmailService();
PaymentProcessor paymentProcessor = new StripeProcessor();
OrderService orderService = new OrderService(emailService, paymentProcessor);

// O con framework (Spring)
@Service
class OrderService {
  @Autowired
  private EmailService emailService;

  @Autowired
  private PaymentProcessor paymentProcessor;

  // Spring inyecta automáticamente
}
```

---

## 9. Antipatrones

### Definición

**Antipatrón:** Solución común a un problema que parece buena pero genera más problemas.

### 9.1 Singleton Abuse

```java
// ❌ Antipatrón: Singleton para todo
class DatabaseConnection {
  private static DatabaseConnection instance;
  private Connection connection;

  private DatabaseConnection() { }

  public static DatabaseConnection getInstance() {
    if (instance == null) {
      instance = new DatabaseConnection();
    }
    return instance;
  }
}

// Problemas:
// - Estado global mutable
// - Dificulta testing
// - Acoplamiento oculto
// - Viola principios SOLID

// ✅ Mejor: DI con scope singleton (si es necesario)
@Component
@Scope("singleton")
class DatabaseConnection { }
```

### 9.2 God Object

```java
// ❌ Antipatrón: Clase que hace todo
class UserManager {
  public void createUser() { }
  public void deleteUser() { }
  public void sendEmail() { }
  public void processPayment() { }
  public void generateReport() { }
  public void validateInput() { }
  public void logActivity() { }
  // ... 50 métodos más
}

// Problemas:
// - Viola SRP
// - Imposible mantener
// - Difícil testear

// ✅ Mejor: Separar responsabilidades
class UserService { }
class EmailService { }
class PaymentService { }
class ReportGenerator { }
```

### 9.3 Lava Flow

```
Código muerto o comentado que nadie se atreve a eliminar

// ❌ Antipatrón
class OldCode {
  // public void oldMethod() {
  //   // Código comentado de hace 3 años
  // }

  public void maybeUsed() {
    // Nadie sabe si esto se usa
  }
}

// ✅ Mejor: Git es tu historial, elimina código muerto
```

### 9.4 Golden Hammer

```
"Si solo tienes un martillo, todo parece un clavo"

❌ Usar Singleton para todo
❌ Usar Factory para objetos simples
❌ Usar Observer cuando no necesitas eventos

✅ Usa la herramienta adecuada para cada problema
```

---

## 10. Cómo Elegir el Patrón Correcto

### Flowchart de Decisión

```
¿Necesitas crear objetos?
  → SÍ: Patrones Creacionales
      ¿Solo una instancia? → Singleton
      ¿Muchos parámetros? → Builder
      ¿Tipo varía? → Factory
      ¿Copia de existente? → Prototype
  → NO: ↓

¿Necesitas estructurar clases?
  → SÍ: Patrones Estructurales
      ¿Interfaces incompatibles? → Adapter
      ¿Agregar funcionalidad? → Decorator
      ¿Simplificar sistema complejo? → Facade
      ¿Control de acceso? → Proxy
  → NO: ↓

¿Necesitas definir interacciones?
  → SÍ: Patrones de Comportamiento
      ¿Intercambiar algoritmos? → Strategy
      ¿Notificar cambios? → Observer
      ¿Undo/redo? → Command
      ¿Pasos comunes/variables? → Template Method
      ¿Comportamiento según estado? → State
```

### Preguntas Guía

```
1. ¿Qué problema estoy tratando de resolver?
2. ¿Ya reconozco este problema de antes?
3. ¿Hay un patrón conocido para esto?
4. ¿El patrón encaja naturalmente?
5. ¿Los beneficios > complejidad agregada?
```

### Señales de Que Necesitas un Patrón

```
Factory/Builder:
□ new Class() con muchos parámetros
□ Lógica condicional de creación

Strategy:
□ Múltiples if/switch basados en tipo
□ Algoritmos intercambiables

Observer:
□ Objeto necesita notificar a otros
□ "Cuando X cambia, hacer Y"

Decorator:
□ Explosión de subclases para variantes
□ Agregar funcionalidad dinámicamente

Command:
□ Necesitas deshacer operaciones
□ Encolar operaciones

Adapter:
□ Integrar código con interfaces incompatibles

Proxy:
□ Control de acceso
□ Lazy loading
□ Logging/caching transparente

Template Method:
□ Código duplicado con pequeñas variaciones
□ Algoritmo con estructura fija

State:
□ Múltiples if basados en estado
□ Comportamiento cambia por estado

Facade:
□ Sistema complejo difícil de usar
□ Múltiples pasos para tarea común
```

---

## 11. Patrones y SOLID

### Cómo los Patrones Implementan SOLID

```
Strategy → OCP, DIP
- Abierto a extensión (nuevas estrategias)
- Cerrado a modificación
- Depende de abstracción

Observer → OCP, DIP
- Sujetos y observadores desacoplados
- Abierto a nuevos observadores

Decorator → OCP, SRP
- Agrega responsabilidades sin modificar
- Cada decorador una responsabilidad

Factory → DIP, OCP
- Cliente depende de abstracción
- Extensible a nuevos tipos

Adapter → DIP
- Cliente depende de interfaz target
- Adaptee desconocido

Template Method → OCP
- Extensible vía hooks
- Estructura cerrada a modificación

Command → SRP, DIP
- Cada comando una operación
- Invoker depende de abstracción
```

---

## 12. Errores Comunes

### Error #1: Patrones por Patrones

```
❌ "Voy a usar Factory porque es un buen patrón"
✅ "Tengo lógica condicional de creación → Factory tiene sentido"

NO uses patrones porque:
- Están de moda
- Los conoces
- "Es buena práctica"

USA patrones cuando:
- Reconoces el problema
- El patrón encaja naturalmente
- Beneficio > complejidad
```

### Error #2: Premature Pattern

```
❌ Aplicar patrones "por las dudas"
✅ Aplicar cuando surge la necesidad

"Tal vez necesite múltiples implementaciones"
→ Espera a que realmente necesites la segunda

YAGNI (You Aren't Gonna Need It) aplica también a patrones
```

### Error #3: Over-Engineering

```
❌ Usar 5 patrones para un script de 50 líneas
✅ Simplicidad primero, patrones cuando se justifican

class HelloWorld {
  // NO necesitas:
  // - HelloWorldFactory
  // - HelloWorldBuilder
  // - HelloWorldSingleton
  // - HelloWorldStrategy
  // - HelloWorldObserver

  public static void main(String[] args) {
    System.out.println("Hello World");  // ✓ Suficiente
  }
}
```

### Error #4: No Entender el Problema

```
❌ Copiar implementación de patrón sin entender
❌ Usar patrón incorrecto para el problema
✅ Entender el problema primero, luego elegir patrón

Aprende:
1. QUÉ problema resuelve el patrón
2. CUÁNDO usarlo
3. CÓMO implementarlo
```

---

## 13. Preguntas Frecuentes

### ¿Debo memorizar todos los patrones?

**No necesariamente.**

```
Enfócate en los más usados:
1. Singleton (con precaución)
2. Factory
3. Builder
4. Adapter
5. Decorator
6. Facade
7. Strategy
8. Observer
9. Command
10. Template Method

Conoce los demás por nombre y propósito.
Profundiza cuando los necesites.
```

### ¿Los patrones son solo para Java/C#?

**No, aplican a cualquier lenguaje OOP:**

```
Java/C#: Implementación clásica
Python: Más flexible (duck typing)
JavaScript: Prototypes, closures
Go: Interfaces implícitas
Rust: Traits

Los conceptos son universales, la sintaxis varía
```

### ¿Patrones en lenguajes funcionales?

**Algunos patrones son innecesarios:**

```
Strategy → Higher-order functions
Command → Functions as first-class citizens
Template Method → Function composition
Observer → Reactive programming (RxJS, etc.)

Otros siguen siendo útiles:
Factory, Builder, Adapter, Decorator (con funciones)
```

### ¿Cuándo NO usar patrones?

```
❌ Código trivial (scripts simples)
❌ Prototipos descartables
❌ Cuando el costo > beneficio
❌ Cuando hay solución más simple
❌ Cuando el equipo no los entiende (aprende primero)
```

### ¿Los frameworks ya implementan patrones?

**Sí, extensivamente:**

```
Spring:
- Singleton (beans)
- Factory (BeanFactory)
- Proxy (AOP)
- Template Method (JdbcTemplate)
- Observer (ApplicationEvent)

React:
- Observer (state management)
- Decorator (Higher-Order Components)
- Strategy (render props)

Django:
- Template Method (class-based views)
- Observer (signals)
- Strategy (backends)
```

---

## Resumen Final

### Los Patrones Más Importantes

**Tier 1 (Esenciales):**
```
1. Singleton - Una instancia
2. Factory - Creación condicional
3. Strategy - Algoritmos intercambiables
4. Observer - Notificaciones
5. Decorator - Funcionalidad dinámica
```

**Tier 2 (Muy Útiles):**
```
6. Builder - Construcción compleja
7. Adapter - Integración
8. Facade - Simplificación
9. Command - Undo/redo
10. Template Method - Esqueleto de algoritmo
```

**Tier 3 (Conocer):**
```
11-23. Los demás patrones del GoF
```

### Checklist de Uso de Patrones

```
Antes de aplicar un patrón:

□ ¿Entiendo el problema que estoy resolviendo?
□ ¿Reconozco este problema de la descripción del patrón?
□ ¿El patrón encaja naturalmente?
□ ¿Los beneficios justifican la complejidad agregada?
□ ¿El equipo entenderá este código?
□ ¿Hay una solución más simple?
□ ¿Estoy aplicando el patrón por las razones correctas?

Si respondes SÍ a la mayoría → adelante
Si tienes dudas → considera alternativa más simple
```

### La Regla de Oro

> **"El mejor código es el que resuelve el problema de la forma más simple posible."**

```
Patrones son herramientas, NO objetivos.

Usa patrones cuando:
✓ Reconoces el problema
✓ El patrón encaja naturalmente
✓ Beneficio > complejidad

NO uses patrones cuando:
✗ No entiendes el problema
✗ Hay solución más simple
✗ Solo por "buena práctica"
```

---

## Recursos Adicionales

### Libros Esenciales

```
📚 "Design Patterns" - Gang of Four
   → El libro original, referencia definitiva

📚 "Head First Design Patterns"
   → Explicación visual y didáctica

📚 "Refactoring to Patterns" - Joshua Kerievsky
   → Cómo refactorizar hacia patrones

📚 "Pattern-Oriented Software Architecture" (POSA)
   → Patrones arquitectónicos
```

### Recursos Online

```
🔗 Refactoring Guru:
   https://refactoring.guru/design-patterns
   → Excelente referencia con ejemplos

🔗 SourceMaking:
   https://sourcemaking.com/design_patterns
   → Patrones + antipatrones

🔗 Java Design Patterns:
   https://java-design-patterns.com
   → Implementaciones en Java
```

---

**Happy Pattern Coding!** 🎯

---

**Última actualización:** Diciembre 2025
**Versión:** 1.0
**Autor:** Manual de Patrones de Diseño
**Licencia:** Uso libre para aprendizaje y aplicación profesional
