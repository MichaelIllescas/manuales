# Manual de Principios SOLID
## Los Fundamentos del Diseño de Software Orientado a Objetos

> **Aprende los 5 principios que son la base de toda buena arquitectura**
>
> Este manual explica los principios SOLID de forma simple, práctica y con ejemplos reales, sin asumir conocimientos previos.

---

## 📚 Índice

1. [¿Qué es SOLID?](#1-qué-es-solid)
2. [Historia y Contexto](#2-historia-y-contexto)
3. [Por Qué Son Importantes](#3-por-qué-son-importantes)
4. [S - Single Responsibility Principle (SRP)](#4-s---single-responsibility-principle-srp)
5. [O - Open/Closed Principle (OCP)](#5-o---openclosed-principle-ocp)
6. [L - Liskov Substitution Principle (LSP)](#6-l---liskov-substitution-principle-lsp)
7. [I - Interface Segregation Principle (ISP)](#7-i---interface-segregation-principle-isp)
8. [D - Dependency Inversion Principle (DIP)](#8-d---dependency-inversion-principle-dip)
9. [SOLID en la Práctica](#9-solid-en-la-práctica)
10. [SOLID y Arquitecturas (Clean, Hexagonal)](#10-solid-y-arquitecturas)
11. [Errores Comunes](#11-errores-comunes)
12. [Balance y Pragmatismo](#12-balance-y-pragmatismo)
13. [Preguntas Frecuentes](#13-preguntas-frecuentes)

---

## 1. ¿Qué es SOLID?

### Definición

**SOLID** es un acrónimo que representa **5 principios fundamentales** del diseño orientado a objetos, propuestos por Robert C. Martin (Uncle Bob).

```
S - Single Responsibility Principle
O - Open/Closed Principle
L - Liskov Substitution Principle
I - Interface Segregation Principle
D - Dependency Inversion Principle
```

### Objetivo

```
Crear software que sea:
✓ Fácil de entender
✓ Fácil de mantener
✓ Fácil de extender
✓ Fácil de testear
✓ Robusto ante cambios
```

### Analogía Simple

Imagina construir una casa:

```
❌ SIN SOLID:
- Muros que sostienen electricidad, plomería y estructura
- Cambiar la plomería requiere demoler muros
- Agregar una ventana afecta la electricidad
- Todo está mezclado y acoplado

✅ CON SOLID:
- Muros solo sostienen estructura (responsabilidad única)
- Plomería separada en tuberías independientes
- Electricidad en su propio sistema
- Puedes cambiar uno sin afectar los otros
- Modular, mantenible, extensible
```

---

## 2. Historia y Contexto

### Origen

```
📅 Años 2000: Robert C. Martin recopila y formaliza estos principios
📚 2002: Publicados en "Agile Software Development: Principles, Patterns, and Practices"
🎯 Objetivo: Combatir el "código espagueti" y sistemas frágiles
```

### Por Qué Se Crearon

En los 90s y 2000s, los proyectos de software sufrían:

```
❌ Código difícil de cambiar (rigidez)
❌ Cambios pequeños rompían todo (fragilidad)
❌ Imposible reutilizar componentes (inmovilidad)
❌ Complejidad innecesaria
❌ Mantenimiento costoso
```

SOLID ofrece principios para evitar estos problemas.

### Evolución

```
2000s: Ampliamente adoptado en Java y C#
2010s: Aplicado en todos los lenguajes OOP
2020s: Base de arquitecturas modernas (Clean, Hexagonal)
```

---

## 3. Por Qué Son Importantes

### Beneficios Directos

#### 1. **Mantenibilidad**
```java
// Sin SOLID: cambiar una cosa rompe otras
class User {
  void saveToDatabase() { ... }
  void sendEmail() { ... }
  void generateReport() { ... }
}
// Cambiar el email afecta User, que afecta la BD, que afecta los reportes

// Con SOLID: cambios aislados
class User { ... }               // Solo datos
class UserRepository { ... }     // Solo persistencia
class EmailService { ... }       // Solo emails
class ReportGenerator { ... }    // Solo reportes
// Cambiar uno no afecta los otros
```

#### 2. **Testabilidad**
```java
// Sin SOLID: difícil de testear
class OrderService {
  void processOrder(Order order) {
    // Acceso directo a BD (no se puede mockear)
    database.save(order);
    // Email hardcodeado (no se puede testear sin enviar emails reales)
    SendGrid.send(order.customerEmail, "Order confirmed");
  }
}

// Con SOLID: fácil de testear
class OrderService {
  private OrderRepository repository;
  private EmailService emailService;

  void processOrder(Order order) {
    repository.save(order);  // Se puede mockear
    emailService.send(order.customerEmail, "Order confirmed");  // Se puede mockear
  }
}
```

#### 3. **Extensibilidad**
```java
// Sin SOLID: modificar código existente para extender
class PaymentProcessor {
  void processPayment(Payment payment) {
    if (payment.type == "CREDIT_CARD") {
      // Lógica de tarjeta
    } else if (payment.type == "PAYPAL") {
      // Lógica de PayPal
    } else if (payment.type == "BITCOIN") {  // ← Agregado: modificamos la clase
      // Lógica de Bitcoin
    }
  }
}

// Con SOLID: extender sin modificar
interface PaymentMethod {
  void process(Payment payment);
}

class CreditCardPayment implements PaymentMethod { ... }
class PayPalPayment implements PaymentMethod { ... }
class BitcoinPayment implements PaymentMethod { ... }  // ← Agregado: nueva clase, sin tocar existentes
```

#### 4. **Reusabilidad**
```java
// Sin SOLID: acoplamiento impide reutilización
class InvoiceGenerator {
  void generate(Order order) {
    // Acoplado a MySQL
    MySQLConnection db = new MySQLConnection();
    OrderData data = db.query("SELECT * FROM orders WHERE id = " + order.id);

    // Acoplado a PDF
    PDFDocument pdf = new PDFDocument();
    pdf.write(data);
  }
}
// No puedes reutilizar esto con PostgreSQL o para generar HTML

// Con SOLID: desacoplado y reutilizable
class InvoiceGenerator {
  private OrderRepository repository;  // Interface
  private DocumentFormatter formatter;  // Interface

  void generate(Order order) {
    OrderData data = repository.findById(order.id);
    Document doc = formatter.format(data);
  }
}
// Funciona con cualquier BD y cualquier formato
```

### Beneficios Indirectos

```
✓ Menos bugs (responsabilidades claras)
✓ Onboarding más rápido (código comprensible)
✓ Colaboración efectiva (módulos independientes)
✓ Refactoring seguro (cambios aislados)
✓ Evolución del sistema (extensiones sin romper)
```

### Costo de NO Aplicar SOLID

```
Semana 1: "Voy rápido, no necesito esto"
Mes 1: "Algunos bugs, pero manejable"
Mes 3: "Cada cambio toma más tiempo"
Mes 6: "Miedo de tocar el código"
Año 1: "Reescritura completa necesaria"

El costo aumenta exponencialmente con el tiempo
```

---

## 4. S - Single Responsibility Principle (SRP)

### Definición

> **Una clase debe tener una, y solo una, razón para cambiar.**

O dicho de otra forma:

> **Una clase debe tener una única responsabilidad.**

### El Problema

```java
// ❌ VIOLACIÓN DE SRP: Clase con múltiples responsabilidades
class Employee {
  private String name;
  private double salary;

  // Responsabilidad 1: Cálculo de salario
  public double calculatePay() {
    // Lógica de cálculo de pago
    return salary * 1.1;
  }

  // Responsabilidad 2: Persistencia
  public void save() {
    // Lógica de guardar en base de datos
    Database.save(this);
  }

  // Responsabilidad 3: Reporting
  public String generateReport() {
    // Lógica de generar reporte
    return "Employee: " + name + ", Salary: " + salary;
  }
}
```

**Problemas:**

```
❌ Cambios en el cálculo de salario pueden afectar el reporte
❌ Cambios en la base de datos pueden romper el cálculo
❌ Difícil de testear (necesitas BD para testear cálculos)
❌ Múltiples equipos modificando la misma clase (contabilidad, IT, HR)
❌ Violación del principio "una razón para cambiar"
```

### La Solución

```java
// ✅ CUMPLE SRP: Una responsabilidad por clase

// Responsabilidad 1: Solo datos del empleado
class Employee {
  private String name;
  private double salary;

  public Employee(String name, double salary) {
    this.name = name;
    this.salary = salary;
  }

  public String getName() { return name; }
  public double getSalary() { return salary; }
}

// Responsabilidad 2: Solo cálculo de salario
class PayrollCalculator {
  public double calculatePay(Employee employee) {
    return employee.getSalary() * 1.1;
  }
}

// Responsabilidad 3: Solo persistencia
class EmployeeRepository {
  public void save(Employee employee) {
    Database.save(employee);
  }
}

// Responsabilidad 4: Solo reporting
class EmployeeReportGenerator {
  public String generate(Employee employee) {
    return "Employee: " + employee.getName() +
           ", Salary: " + employee.getSalary();
  }
}
```

**Beneficios:**

```
✓ Cambiar cálculo de pago no afecta persistencia
✓ Cambiar BD no afecta reportes
✓ Cada clase es fácil de entender
✓ Fácil de testear (sin dependencias)
✓ Equipos pueden trabajar independientemente
```

### Ejemplo Real: Sistema de E-commerce

```java
// ❌ MAL: Clase Dios (God Class)
class Order {
  private List<Item> items;
  private Customer customer;

  // Cálculo
  public double calculateTotal() { ... }
  public double calculateTax() { ... }
  public double calculateShipping() { ... }

  // Validación
  public boolean isValid() { ... }
  public boolean hasStock() { ... }

  // Persistencia
  public void save() { ... }
  public void update() { ... }

  // Notificación
  public void sendConfirmationEmail() { ... }
  public void sendSMS() { ... }

  // Reporting
  public String generateInvoice() { ... }
  public String generateLabel() { ... }
}
// 10+ responsabilidades en una clase!
```

```java
// ✅ BIEN: Responsabilidades separadas

class Order {
  private String id;
  private List<Item> items;
  private Customer customer;
  // Solo datos y getters
}

class OrderCalculator {
  public Money calculateTotal(Order order) { ... }
  public Money calculateTax(Order order) { ... }
  public Money calculateShipping(Order order) { ... }
}

class OrderValidator {
  public ValidationResult validate(Order order) { ... }
  public boolean hasStock(Order order) { ... }
}

class OrderRepository {
  public void save(Order order) { ... }
  public Order findById(String id) { ... }
}

class OrderNotificationService {
  public void sendConfirmation(Order order) { ... }
  public void sendSMS(Order order) { ... }
}

class InvoiceGenerator {
  public Invoice generate(Order order) { ... }
}

class ShippingLabelGenerator {
  public Label generate(Order order) { ... }
}
```

### Cómo Identificar Violaciones de SRP

**Pregúntate:**

```
1. ¿Cuántas razones tiene esta clase para cambiar?
   - Si más de una → violación de SRP

2. ¿Puedo describir la clase sin usar "Y"?
   - "Esta clase calcula impuestos Y envía emails" → violación
   - "Esta clase calcula impuestos" → OK

3. ¿Cuántos equipos/departamentos modificarían esta clase?
   - Múltiples equipos → violación de SRP

4. ¿Tiene más de 5-7 métodos públicos?
   - Muchos métodos → posible violación

5. ¿El nombre de la clase es vago? (Manager, Handler, Util)
   - Nombres vagos → probablemente hace mucho
```

### Cuándo Aplicar SRP

```
✓ Clases de negocio (modelos, servicios)
✓ Código que cambia frecuentemente
✓ Código complejo
✓ Proyectos de larga duración

Puedes relajar SRP en:
- Prototipos descartables
- Scripts simples
- DTOs/POJOs simples
```

### SRP en Diferentes Niveles

```
MÉTODO: Una función, una tarea
  ✓ calculateTotal()
  ✗ calculateTotalAndSendEmail()

CLASE: Una clase, una responsabilidad
  ✓ InvoiceCalculator
  ✗ InvoiceManager (hace todo)

MÓDULO: Un módulo, un concepto
  ✓ /payments (solo pagos)
  ✗ /utils (cajón de sastre)

SERVICIO: Un servicio, un bounded context
  ✓ Payment Service
  ✗ Everything Service
```

---

## 5. O - Open/Closed Principle (OCP)

### Definición

> **Las entidades de software (clases, módulos, funciones) deben estar abiertas para extensión, pero cerradas para modificación.**

Dicho simple:

> **Deberías poder agregar funcionalidad nueva sin modificar código existente.**

### El Problema

```java
// ❌ VIOLACIÓN DE OCP: Modificar código existente para extender
class DiscountCalculator {
  public double calculate(Order order, String customerType) {
    if (customerType.equals("REGULAR")) {
      return order.getTotal() * 0.0;  // Sin descuento
    } else if (customerType.equals("VIP")) {
      return order.getTotal() * 0.1;  // 10% descuento
    } else if (customerType.equals("EMPLOYEE")) {  // ← MODIFICACIÓN
      return order.getTotal() * 0.2;  // 20% descuento
    }
    // ¿Qué pasa si necesitamos PARTNER, STUDENT, SENIOR?
    // Seguimos modificando esta clase...
    return 0;
  }
}
```

**Problemas:**

```
❌ Cada nuevo tipo de cliente requiere modificar la clase
❌ Riesgo de romper casos existentes
❌ Necesitas entender toda la lógica para agregar un caso
❌ Testing: re-testear todos los casos cada vez
❌ Violación de "cerrado para modificación"
```

### La Solución

```java
// ✅ CUMPLE OCP: Extensión sin modificación

// 1. Abstracción (cerrada para modificación)
interface DiscountStrategy {
  double calculate(Order order);
}

// 2. Implementaciones concretas (extensiones)
class RegularCustomerDiscount implements DiscountStrategy {
  @Override
  public double calculate(Order order) {
    return 0;  // Sin descuento
  }
}

class VIPCustomerDiscount implements DiscountStrategy {
  @Override
  public double calculate(Order order) {
    return order.getTotal() * 0.1;  // 10%
  }
}

class EmployeeDiscount implements DiscountStrategy {
  @Override
  public double calculate(Order order) {
    return order.getTotal() * 0.2;  // 20%
  }
}

// 3. Agregar nuevos tipos SIN modificar existentes
class PartnerDiscount implements DiscountStrategy {  // ← NUEVA CLASE
  @Override
  public double calculate(Order order) {
    return order.getTotal() * 0.15;  // 15%
  }
}

// 4. Uso
class OrderService {
  private DiscountStrategy discountStrategy;

  public double calculateFinalPrice(Order order) {
    double discount = discountStrategy.calculate(order);
    return order.getTotal() - discount;
  }
}
```

**Beneficios:**

```
✓ Agregar nuevo tipo = crear nueva clase (sin tocar existentes)
✓ Código existente permanece intacto (cerrado para modificación)
✓ Extensible infinitamente (abierto para extensión)
✓ Cada estrategia es testeable independientemente
✓ Sin riesgo de romper funcionalidad existente
```

### Ejemplo Real: Sistema de Notificaciones

```java
// ❌ MAL: Modificar para agregar canales
class NotificationService {
  public void send(String message, String channel) {
    if (channel.equals("EMAIL")) {
      // Enviar email
      SMTP.send(message);
    } else if (channel.equals("SMS")) {
      // Enviar SMS
      Twilio.send(message);
    } else if (channel.equals("PUSH")) {  // ← Modificación
      // Enviar push notification
      FCM.send(message);
    }
    // ¿Y si queremos Slack, WhatsApp, Telegram?
  }
}
```

```java
// ✅ BIEN: Extensión sin modificación

interface NotificationChannel {
  void send(String message);
}

class EmailChannel implements NotificationChannel {
  @Override
  public void send(String message) {
    SMTP.send(message);
  }
}

class SMSChannel implements NotificationChannel {
  @Override
  public void send(String message) {
    Twilio.send(message);
  }
}

class PushNotificationChannel implements NotificationChannel {
  @Override
  public void send(String message) {
    FCM.send(message);
  }
}

// Agregar Slack: solo nueva clase
class SlackChannel implements NotificationChannel {  // ← Sin tocar existentes
  @Override
  public void send(String message) {
    SlackAPI.send(message);
  }
}

class NotificationService {
  private List<NotificationChannel> channels;

  public void send(String message) {
    for (NotificationChannel channel : channels) {
      channel.send(message);
    }
  }
}
```

### Técnicas para Lograr OCP

#### 1. **Abstracción (Interfaces/Abstract Classes)**

```java
// Define contrato estable
interface PaymentProcessor {
  void process(Payment payment);
}

// Extensiones concretas
class StripeProcessor implements PaymentProcessor { ... }
class PayPalProcessor implements PaymentProcessor { ... }
```

#### 2. **Herencia**

```java
abstract class Report {
  public final void generate() {  // Template method
    prepareData();
    formatReport();
    saveReport();
  }

  protected abstract void formatReport();  // Punto de extensión
  private void prepareData() { ... }
  private void saveReport() { ... }
}

class PDFReport extends Report {
  @Override
  protected void formatReport() {
    // Formato PDF
  }
}

class ExcelReport extends Report {
  @Override
  protected void formatReport() {
    // Formato Excel
  }
}
```

#### 3. **Composición**

```java
class EmailSender {
  private EmailFormatter formatter;  // Inyectado
  private EmailTransport transport;  // Inyectado

  public void send(Email email) {
    String formatted = formatter.format(email);
    transport.send(formatted);
  }
}

// Extensión: cambiar comportamiento sin modificar EmailSender
EmailSender sender = new EmailSender(
  new HTMLFormatter(),    // o new PlainTextFormatter()
  new SMTPTransport()     // o new SendGridTransport()
);
```

#### 4. **Design Patterns que Implementan OCP**

```
✓ Strategy Pattern
✓ Template Method Pattern
✓ Decorator Pattern
✓ Factory Pattern
✓ Observer Pattern
```

### Cuándo Aplicar OCP

```
✓ Código que cambia frecuentemente
✓ Múltiples variantes de un comportamiento
✓ Plugins/extensiones
✓ Frameworks y librerías

Puedes relajar OCP:
- Código que casi nunca cambia
- Prototipos
- Casos muy simples (2-3 opciones fijas)
```

### OCP y el Principio de Anticipación

**Regla de Oro:**

```
❌ NO diseñes para extensión "por las dudas"
✅ Diseña para extensión cuando:
   1. Ya necesitas la segunda variante
   2. Sabes que vendrán más variantes
   3. El costo de cambiar después es alto
```

**Ejemplo:**

```java
// Primer iteración: simple
class EmailSender {
  public void send(String to, String subject, String body) {
    SMTP.send(to, subject, body);
  }
}

// Segunda iteración: necesitas SMS también → AHORA aplica OCP
interface MessageSender {
  void send(Message message);
}

class EmailSender implements MessageSender { ... }
class SMSSender implements MessageSender { ... }
```

---

## 6. L - Liskov Substitution Principle (LSP)

### Definición

> **Los objetos de una clase derivada deben poder reemplazar objetos de la clase base sin alterar el comportamiento correcto del programa.**

Dicho simple:

> **Si una clase B hereda de una clase A, deberías poder usar B en cualquier lugar donde uses A, sin sorpresas.**

### El Problema

```java
// ❌ VIOLACIÓN DE LSP
class Rectangle {
  protected int width;
  protected int height;

  public void setWidth(int width) {
    this.width = width;
  }

  public void setHeight(int height) {
    this.height = height;
  }

  public int getArea() {
    return width * height;
  }
}

class Square extends Rectangle {
  @Override
  public void setWidth(int width) {
    this.width = width;
    this.height = width;  // ← Sorpresa! Cambiar ancho también cambia alto
  }

  @Override
  public void setHeight(int height) {
    this.width = height;  // ← Sorpresa! Cambiar alto también cambia ancho
    this.height = height;
  }
}

// Código que usa Rectangle
void testArea(Rectangle rect) {
  rect.setWidth(5);
  rect.setHeight(4);
  assertEquals(20, rect.getArea());  // Espera 5 * 4 = 20
}

// Test con Square
Square square = new Square();
testArea(square);  // ❌ FALLA! Área = 16 (4 * 4), no 20
```

**Problema:**

```
❌ Square NO puede sustituir a Rectangle
❌ El comportamiento cambia inesperadamente
❌ Rompe la expectativa de que ancho y alto son independientes
❌ Violación de LSP
```

### La Solución

```java
// ✅ CUMPLE LSP: Abstracción común sin herencia forzada

interface Shape {
  int getArea();
}

class Rectangle implements Shape {
  private int width;
  private int height;

  public Rectangle(int width, int height) {
    this.width = width;
    this.height = height;
  }

  public void setWidth(int width) {
    this.width = width;
  }

  public void setHeight(int height) {
    this.height = height;
  }

  @Override
  public int getArea() {
    return width * height;
  }
}

class Square implements Shape {
  private int side;

  public Square(int side) {
    this.side = side;
  }

  public void setSide(int side) {
    this.side = side;
  }

  @Override
  public int getArea() {
    return side * side;
  }
}

// Uso: trabaja con la abstracción
void printArea(Shape shape) {
  System.out.println("Area: " + shape.getArea());
}

printArea(new Rectangle(5, 4));  // ✓ Funciona
printArea(new Square(5));        // ✓ Funciona
```

### Ejemplo Real: Sistema de Vuelo

```java
// ❌ VIOLACIÓN DE LSP
class Bird {
  public void fly() {
    System.out.println("Flying...");
  }
}

class Penguin extends Bird {
  @Override
  public void fly() {
    throw new UnsupportedOperationException("Penguins can't fly!");
    // ← Sorpresa! Rompe el contrato de Bird
  }
}

// Código que usa Bird
void migrateBirds(List<Bird> birds) {
  for (Bird bird : birds) {
    bird.fly();  // ❌ Explota con Penguin
  }
}
```

```java
// ✅ CUMPLE LSP: Modelado correcto

interface Bird {
  void eat();
  void sleep();
}

interface FlyingBird extends Bird {
  void fly();
}

class Sparrow implements FlyingBird {
  @Override
  public void fly() {
    System.out.println("Sparrow flying");
  }

  @Override
  public void eat() { ... }

  @Override
  public void sleep() { ... }
}

class Penguin implements Bird {  // No FlyingBird
  @Override
  public void eat() { ... }

  @Override
  public void sleep() { ... }

  public void swim() {  // Capacidad única
    System.out.println("Penguin swimming");
  }
}

// Uso correcto
void migrateBirds(List<FlyingBird> birds) {  // Solo aves voladoras
  for (FlyingBird bird : birds) {
    bird.fly();  // ✓ Todas pueden volar
  }
}
```

### Reglas de LSP

#### Regla 1: **Precondiciones no pueden ser más fuertes en subclases**

```java
// ❌ MAL
class Account {
  public void withdraw(double amount) {
    // Precondición: amount > 0
    if (amount <= 0) throw new IllegalArgumentException();
    // ...
  }
}

class SavingsAccount extends Account {
  @Override
  public void withdraw(double amount) {
    // Precondición MÁS FUERTE: amount > 0 AND amount < 1000
    if (amount <= 0 || amount >= 1000) {
      throw new IllegalArgumentException();  // ← Más restrictivo
    }
    // ...
  }
}

// Problema: código que funciona con Account falla con SavingsAccount
Account account = new SavingsAccount();
account.withdraw(1500);  // ❌ Falla, pero debería funcionar según Account
```

```java
// ✅ BIEN
class Account {
  public void withdraw(double amount) {
    if (amount <= 0) throw new IllegalArgumentException();
    // ...
  }
}

class SavingsAccount extends Account {
  @Override
  public void withdraw(double amount) {
    // Mismas precondiciones (o más débiles)
    if (amount <= 0) throw new IllegalArgumentException();

    // Validaciones adicionales como lógica de negocio
    if (amount >= 1000) {
      System.out.println("Warning: large withdrawal");
    }
    // ...
  }
}
```

#### Regla 2: **Postcondiciones no pueden ser más débiles en subclases**

```java
// ❌ MAL
class FileProcessor {
  // Postcondición: archivo siempre cerrado después de procesar
  public void process(File file) {
    file.open();
    // ... procesar ...
    file.close();  // Garantizado
  }
}

class FastFileProcessor extends FileProcessor {
  @Override
  public void process(File file) {
    file.open();
    // ... procesar ...
    // ❌ NO cierra el archivo (postcondición más débil)
  }
}
```

#### Regla 3: **Invariantes de la clase base deben preservarse**

```java
// ❌ MAL
class Stack {
  private List<Integer> items = new ArrayList<>();

  // Invariante: top() siempre retorna el último elemento agregado
  public void push(int item) {
    items.add(item);
  }

  public int pop() {
    return items.remove(items.size() - 1);
  }

  public int top() {
    return items.get(items.size() - 1);
  }
}

class RandomStack extends Stack {
  @Override
  public int pop() {
    // ❌ Rompe invariante: retorna elemento aleatorio
    int randomIndex = new Random().nextInt(items.size());
    return items.remove(randomIndex);
  }
}
```

### Cómo Identificar Violaciones de LSP

**Señales de alerta:**

```
❌ Subclase lanza excepciones que la base no lanza
❌ Subclase retorna null cuando la base nunca retorna null
❌ Subclase tiene métodos vacíos o que lanzan "not implemented"
❌ Necesitas usar instanceof para verificar el tipo
❌ Subclase tiene comportamiento sorprendente/inesperado
❌ Tests que pasan con la base fallan con la subclase
```

### LSP y el Uso de instanceof

```java
// ❌ Señal de violación de LSP
void processShape(Shape shape) {
  if (shape instanceof Circle) {
    // Hacer algo especial con círculo
  } else if (shape instanceof Square) {
    // Hacer algo diferente con cuadrado
  }
}

// Si necesitas instanceof, probablemente:
// 1. Tu jerarquía está mal diseñada
// 2. Estás violando LSP
// 3. Deberías usar polimorfismo
```

```java
// ✅ MEJOR: Polimorfismo
interface Shape {
  void draw();
}

class Circle implements Shape {
  @Override
  public void draw() {
    // Dibujar círculo
  }
}

class Square implements Shape {
  @Override
  public void draw() {
    // Dibujar cuadrado
  }
}

void processShape(Shape shape) {
  shape.draw();  // Polimorfismo, sin instanceof
}
```

---

## 7. I - Interface Segregation Principle (ISP)

### Definición

> **Los clientes no deberían ser forzados a depender de interfaces que no usan.**

Dicho simple:

> **Es mejor tener muchas interfaces pequeñas y específicas que una interfaz grande y general.**

### El Problema

```java
// ❌ VIOLACIÓN DE ISP: Interface "gorda" (Fat Interface)
interface Worker {
  void work();
  void eat();
  void sleep();
  void getPaid();
  void takeVacation();
  void attendMeeting();
}

// Robot trabajador: ¿necesita comer, dormir, vacaciones?
class RobotWorker implements Worker {
  @Override
  public void work() {
    // ✓ OK, los robots trabajan
  }

  @Override
  public void eat() {
    // ❌ Los robots no comen
    throw new UnsupportedOperationException();
  }

  @Override
  public void sleep() {
    // ❌ Los robots no duermen
    throw new UnsupportedOperationException();
  }

  @Override
  public void getPaid() {
    // ❌ Los robots no cobran
    throw new UnsupportedOperationException();
  }

  @Override
  public void takeVacation() {
    // ❌ Los robots no vacacionan
    throw new UnsupportedOperationException();
  }

  @Override
  public void attendMeeting() {
    // ✓ OK, pueden ir a reuniones
  }
}
```

**Problemas:**

```
❌ RobotWorker forzado a implementar métodos que no usa
❌ Implementación con excepciones o métodos vacíos
❌ Interface sobrecargada
❌ Cambios en métodos no relevantes obligan a actualizar la clase
❌ Acoplamiento innecesario
```

### La Solución

```java
// ✅ CUMPLE ISP: Interfaces segregadas (pequeñas y específicas)

interface Workable {
  void work();
}

interface Eatable {
  void eat();
}

interface Sleepable {
  void sleep();
}

interface Payable {
  void getPaid();
}

interface Vacationable {
  void takeVacation();
}

interface Meetable {
  void attendMeeting();
}

// Humano: implementa todas las que necesita
class HumanWorker implements Workable, Eatable, Sleepable,
                             Payable, Vacationable, Meetable {
  @Override
  public void work() { ... }

  @Override
  public void eat() { ... }

  @Override
  public void sleep() { ... }

  @Override
  public void getPaid() { ... }

  @Override
  public void takeVacation() { ... }

  @Override
  public void attendMeeting() { ... }
}

// Robot: solo implementa las que necesita
class RobotWorker implements Workable, Meetable {
  @Override
  public void work() { ... }  // ✓ Relevante

  @Override
  public void attendMeeting() { ... }  // ✓ Relevante

  // ✓ No implementa Eatable, Sleepable, Payable, Vacationable
}
```

**Beneficios:**

```
✓ Cada clase solo implementa lo que necesita
✓ Sin métodos vacíos o excepciones
✓ Interfaces claras y enfocadas
✓ Bajo acoplamiento
✓ Fácil de entender y mantener
```

### Ejemplo Real: Sistema de Impresión

```java
// ❌ MAL: Interface monolítica
interface MultiFunctionDevice {
  void print(Document doc);
  void scan(Document doc);
  void fax(Document doc);
  void staple(Document doc);
  void photocopy(Document doc);
}

// Impresora simple: solo imprime
class SimplePrinter implements MultiFunctionDevice {
  @Override
  public void print(Document doc) {
    // ✓ OK
  }

  @Override
  public void scan(Document doc) {
    // ❌ No puede escanear
    throw new UnsupportedOperationException();
  }

  @Override
  public void fax(Document doc) {
    // ❌ No puede enviar fax
    throw new UnsupportedOperationException();
  }

  @Override
  public void staple(Document doc) {
    // ❌ No tiene grapadora
    throw new UnsupportedOperationException();
  }

  @Override
  public void photocopy(Document doc) {
    // ❌ No puede fotocopiar
    throw new UnsupportedOperationException();
  }
}
```

```java
// ✅ BIEN: Interfaces segregadas

interface Printer {
  void print(Document doc);
}

interface Scanner {
  void scan(Document doc);
}

interface Fax {
  void fax(Document doc);
}

interface Stapler {
  void staple(Document doc);
}

interface Photocopier {
  void photocopy(Document doc);
}

// Impresora simple: solo lo que necesita
class SimplePrinter implements Printer {
  @Override
  public void print(Document doc) {
    System.out.println("Printing: " + doc.getName());
  }
}

// Impresora multifunción: combina varias
class MultiFunctionPrinter implements Printer, Scanner, Fax {
  @Override
  public void print(Document doc) { ... }

  @Override
  public void scan(Document doc) { ... }

  @Override
  public void fax(Document doc) { ... }
}

// Impresora profesional: todas las capacidades
class ProfessionalPrinter implements Printer, Scanner, Fax,
                                      Stapler, Photocopier {
  @Override
  public void print(Document doc) { ... }

  @Override
  public void scan(Document doc) { ... }

  @Override
  public void fax(Document doc) { ... }

  @Override
  public void staple(Document doc) { ... }

  @Override
  public void photocopy(Document doc) { ... }
}
```

### Ejemplo Real: Repositorio de Datos

```java
// ❌ MAL: Repository genérico con todo
interface Repository<T> {
  T findById(Long id);
  List<T> findAll();
  T save(T entity);
  void delete(T entity);
  List<T> search(String query);
  List<T> findByDateRange(Date start, Date end);
  void exportToCSV(String filename);
  void importFromCSV(String filename);
  List<T> findWithPagination(int page, int size);
  long count();
}

// Read-only repository: solo lee
class ReadOnlyUserRepository implements Repository<User> {
  @Override
  public User findById(Long id) {
    // ✓ OK
    return database.find(id);
  }

  @Override
  public List<User> findAll() {
    // ✓ OK
    return database.findAll();
  }

  @Override
  public User save(User entity) {
    // ❌ Read-only, no debería tener esto
    throw new UnsupportedOperationException();
  }

  @Override
  public void delete(User entity) {
    // ❌ Read-only, no debería tener esto
    throw new UnsupportedOperationException();
  }

  // ... más métodos irrelevantes
}
```

```java
// ✅ BIEN: Interfaces segregadas

interface Readable<T> {
  T findById(Long id);
  List<T> findAll();
}

interface Writable<T> {
  T save(T entity);
  void delete(T entity);
}

interface Searchable<T> {
  List<T> search(String query);
}

interface Exportable {
  void exportToCSV(String filename);
}

interface Importable {
  void importFromCSV(String filename);
}

interface Paginable<T> {
  List<T> findWithPagination(int page, int size);
  long count();
}

// Repositories específicos combinan solo lo que necesitan

class ReadOnlyUserRepository implements Readable<User>, Searchable<User> {
  @Override
  public User findById(Long id) { ... }

  @Override
  public List<User> findAll() { ... }

  @Override
  public List<User> search(String query) { ... }
}

class FullUserRepository implements Readable<User>, Writable<User>,
                                     Searchable<User>, Paginable<User> {
  @Override
  public User findById(Long id) { ... }

  @Override
  public List<User> findAll() { ... }

  @Override
  public User save(User entity) { ... }

  @Override
  public void delete(User entity) { ... }

  @Override
  public List<User> search(String query) { ... }

  @Override
  public List<User> findWithPagination(int page, int size) { ... }

  @Override
  public long count() { ... }
}
```

### Cómo Identificar Violaciones de ISP

**Señales de alerta:**

```
❌ Implementaciones con throw new UnsupportedOperationException()
❌ Métodos vacíos o con comentarios "not implemented"
❌ Interface con > 7-10 métodos
❌ Interface con métodos no relacionados
❌ Clases que implementan interface pero solo usan 20-30% de métodos
❌ Nombre de interface vago (Manager, Handler, Service)
```

### ISP vs SRP

```
SRP: Una clase, una responsabilidad
ISP: Una interface, una funcionalidad cohesiva

Son complementarios:
- SRP se aplica a implementaciones
- ISP se aplica a abstracciones
```

### Cuándo Aplicar ISP

```
✓ Interfaces públicas (APIs, libraries)
✓ Interfaces con múltiples implementaciones diferentes
✓ Código que cambia frecuentemente
✓ Cuando detectas UnsupportedOperationException

Puedes relajar ISP:
- Interfaces internas simples
- Interfaces con 2-3 métodos muy relacionados
- Prototipos
```

---

## 8. D - Dependency Inversion Principle (DIP)

### Definición

> **A. Los módulos de alto nivel no deben depender de módulos de bajo nivel. Ambos deben depender de abstracciones.**
>
> **B. Las abstracciones no deben depender de detalles. Los detalles deben depender de abstracciones.**

Dicho simple:

> **Depende de abstracciones (interfaces), no de implementaciones concretas.**

### El Problema

```java
// ❌ VIOLACIÓN DE DIP: Alto nivel depende de bajo nivel

// Módulo de BAJO nivel (detalle concreto)
class MySQLDatabase {
  public void save(String data) {
    // Lógica específica de MySQL
    System.out.println("Saving to MySQL: " + data);
  }
}

// Módulo de ALTO nivel (lógica de negocio)
class UserService {
  private MySQLDatabase database;  // ← Depende de detalle concreto

  public UserService() {
    this.database = new MySQLDatabase();  // ← Acoplamiento fuerte
  }

  public void createUser(String username) {
    // Lógica de negocio
    String data = "User: " + username;

    // Dependencia directa de MySQL
    database.save(data);
  }
}
```

**Problemas:**

```
❌ UserService está acoplado a MySQL
❌ Cambiar a PostgreSQL requiere modificar UserService
❌ Imposible testear sin MySQL real
❌ UserService (alto nivel) depende de MySQLDatabase (bajo nivel)
❌ Violación del DIP
```

### La Solución

```java
// ✅ CUMPLE DIP: Ambos dependen de abstracción

// Abstracción (interface)
interface Database {
  void save(String data);
}

// Módulo de BAJO nivel (implementación concreta)
class MySQLDatabase implements Database {
  @Override
  public void save(String data) {
    System.out.println("Saving to MySQL: " + data);
  }
}

class PostgreSQLDatabase implements Database {
  @Override
  public void save(String data) {
    System.out.println("Saving to PostgreSQL: " + data);
  }
}

// Módulo de ALTO nivel (lógica de negocio)
class UserService {
  private Database database;  // ← Depende de abstracción

  public UserService(Database database) {  // ← Inyección de dependencia
    this.database = database;
  }

  public void createUser(String username) {
    String data = "User: " + username;
    database.save(data);  // ← Llama a abstracción
  }
}

// Uso
Database db = new MySQLDatabase();  // O PostgreSQLDatabase
UserService service = new UserService(db);
service.createUser("john");
```

**Beneficios:**

```
✓ UserService independiente de la BD específica
✓ Fácil cambiar de MySQL a PostgreSQL (solo en configuración)
✓ Testeable con mocks
✓ Bajo acoplamiento
✓ Ambos módulos dependen de abstracción (Database)
```

### Visualización de DIP

**Sin DIP:**

```
┌─────────────────┐
│  UserService    │ (Alto nivel)
│  (lógica)       │
└────────┬────────┘
         │ depende de
         ↓
┌────────────────────┐
│  MySQLDatabase     │ (Bajo nivel)
│  (detalle técnico) │
└────────────────────┘

Problema: Alto nivel conoce detalles de bajo nivel
```

**Con DIP:**

```
┌─────────────────┐
│  UserService    │ (Alto nivel)
└────────┬────────┘
         │ depende de
         ↓
    ┌─────────┐
    │Database │ (Abstracción)
    │interface│
    └────┬────┘
         ↑ implementa
         │
┌────────┴───────────┐
│  MySQLDatabase     │ (Bajo nivel)
└────────────────────┘

Solución: Ambos dependen de abstracción
La flecha de dependencia se "invirtió"
```

### Ejemplo Real: Sistema de Notificaciones

```java
// ❌ SIN DIP
class OrderService {
  private SendGridEmailSender emailSender;  // ← Acoplado a SendGrid

  public OrderService() {
    this.emailSender = new SendGridEmailSender();
  }

  public void placeOrder(Order order) {
    // Lógica de negocio
    processPayment(order);

    // Notificación acoplada
    emailSender.send(
      order.getCustomerEmail(),
      "Order Confirmed",
      "Your order has been placed"
    );
  }
}

// Problemas:
// - ¿Cambiar a Mailchimp? → Modificar OrderService
// - ¿Testear sin SendGrid? → Imposible
// - ¿Agregar SMS? → Modificar OrderService
```

```java
// ✅ CON DIP

// Abstracción
interface NotificationService {
  void sendNotification(String recipient, String subject, String message);
}

// Implementaciones concretas
class EmailNotificationService implements NotificationService {
  private EmailProvider provider;  // SendGrid, Mailchimp, etc.

  public EmailNotificationService(EmailProvider provider) {
    this.provider = provider;
  }

  @Override
  public void sendNotification(String recipient, String subject, String message) {
    provider.sendEmail(recipient, subject, message);
  }
}

class SMSNotificationService implements NotificationService {
  @Override
  public void sendNotification(String recipient, String subject, String message) {
    // Enviar SMS
    Twilio.sendSMS(recipient, message);
  }
}

// Alto nivel depende de abstracción
class OrderService {
  private NotificationService notificationService;  // ← Abstracción

  public OrderService(NotificationService notificationService) {  // ← DI
    this.notificationService = notificationService;
  }

  public void placeOrder(Order order) {
    processPayment(order);

    // Usa abstracción
    notificationService.sendNotification(
      order.getCustomerEmail(),
      "Order Confirmed",
      "Your order has been placed"
    );
  }
}

// Configuración (Composition Root)
NotificationService emailService = new EmailNotificationService(new SendGridProvider());
OrderService orderService = new OrderService(emailService);

// Fácil cambiar a SMS
NotificationService smsService = new SMSNotificationService();
OrderService orderService2 = new OrderService(smsService);

// Test con mock
NotificationService mockService = mock(NotificationService.class);
OrderService testService = new OrderService(mockService);
```

### DIP y Dependency Injection (DI)

**Son conceptos relacionados pero diferentes:**

```
Dependency Inversion Principle (DIP):
- Principio de diseño
- "Depende de abstracciones, no de concreciones"
- WHAT: define la dirección de dependencias

Dependency Injection (DI):
- Técnica de implementación
- "Inyecta dependencias desde afuera"
- HOW: técnica para lograr DIP
```

**Técnicas de DI:**

#### 1. **Constructor Injection** (Recomendado)

```java
class UserService {
  private final Database database;
  private final EmailService emailService;

  // Dependencias inyectadas en constructor
  public UserService(Database database, EmailService emailService) {
    this.database = database;
    this.emailService = emailService;
  }
}

// Ventajas:
// ✓ Dependencias obligatorias explícitas
// ✓ Inmutabilidad (final)
// ✓ Fácil de testear
```

#### 2. **Setter Injection**

```java
class UserService {
  private Database database;

  // Dependencia inyectada vía setter
  public void setDatabase(Database database) {
    this.database = database;
  }
}

// Ventajas:
// ✓ Dependencias opcionales
// ✓ Permite cambiar dependencia en runtime

// Desventajas:
// ✗ Puede quedar sin inicializar
// ✗ Mutable
```

#### 3. **Interface Injection** (Menos común)

```java
interface DatabaseInjectable {
  void injectDatabase(Database database);
}

class UserService implements DatabaseInjectable {
  private Database database;

  @Override
  public void injectDatabase(Database database) {
    this.database = database;
  }
}
```

### DIP en Acción: Clean Architecture

```
┌──────────────────────────────────────┐
│  Use Cases (Alto nivel)              │
│  - Lógica de aplicación              │
└────────┬─────────────────────────────┘
         │ depende de
         ↓
    ┌────────────────┐
    │ Repositories   │ (Abstracción/Interface)
    │ EmailService   │
    └────┬───────────┘
         ↑ implementa
         │
┌────────┴─────────────────────────────┐
│  Infrastructure (Bajo nivel)         │
│  - JpaRepository                     │
│  - SendGridEmailService              │
└──────────────────────────────────────┘

DIP permite que Use Cases (core) no dependan de Infrastructure
```

### Ejemplo Completo: Sistema de Pagos

```java
// ❌ SIN DIP
class CheckoutService {
  public void checkout(Cart cart) {
    // Acoplado a Stripe
    StripeAPI stripe = new StripeAPI("sk_key_123");
    stripe.charge(cart.getTotal(), cart.getPaymentDetails());

    // Acoplado a MySQL
    MySQLConnection db = new MySQLConnection("localhost");
    db.execute("INSERT INTO orders ...");

    // Acoplado a SendGrid
    SendGridClient email = new SendGridClient("api_key_456");
    email.send("Order confirmed");
  }
}
```

```java
// ✅ CON DIP

// Abstracciones
interface PaymentGateway {
  PaymentResult charge(Money amount, PaymentDetails details);
}

interface OrderRepository {
  void save(Order order);
}

interface NotificationService {
  void notify(String message);
}

// Implementaciones concretas (bajo nivel)
class StripePaymentGateway implements PaymentGateway {
  @Override
  public PaymentResult charge(Money amount, PaymentDetails details) {
    StripeAPI stripe = new StripeAPI("sk_key_123");
    return stripe.charge(amount.getValue(), details);
  }
}

class DatabaseOrderRepository implements OrderRepository {
  @Override
  public void save(Order order) {
    MySQLConnection db = new MySQLConnection("localhost");
    db.execute("INSERT INTO orders ...");
  }
}

class EmailNotificationService implements NotificationService {
  @Override
  public void notify(String message) {
    SendGridClient email = new SendGridClient("api_key_456");
    email.send(message);
  }
}

// Alto nivel depende de abstracciones
class CheckoutService {
  private final PaymentGateway paymentGateway;
  private final OrderRepository orderRepository;
  private final NotificationService notificationService;

  public CheckoutService(
      PaymentGateway paymentGateway,
      OrderRepository orderRepository,
      NotificationService notificationService) {
    this.paymentGateway = paymentGateway;
    this.orderRepository = orderRepository;
    this.notificationService = notificationService;
  }

  public void checkout(Cart cart) {
    // Usa abstracciones
    PaymentResult result = paymentGateway.charge(
      cart.getTotal(),
      cart.getPaymentDetails()
    );

    if (result.isSuccess()) {
      Order order = new Order(cart);
      orderRepository.save(order);
      notificationService.notify("Order confirmed");
    }
  }
}

// Configuración (Composition Root)
PaymentGateway gateway = new StripePaymentGateway();
OrderRepository repo = new DatabaseOrderRepository();
NotificationService notification = new EmailNotificationService();

CheckoutService service = new CheckoutService(gateway, repo, notification);
```

**Beneficios:**

```
✓ Fácil cambiar Stripe → PayPal (solo config)
✓ Fácil cambiar MySQL → PostgreSQL (solo config)
✓ Fácil cambiar SendGrid → Mailchimp (solo config)
✓ Testeable con mocks
✓ CheckoutService no conoce detalles técnicos
```

### Cuándo Aplicar DIP

```
✓ Lógica de negocio (siempre)
✓ Servicios de alto nivel
✓ Código con múltiples implementaciones posibles
✓ Integraciones con sistemas externos

Puedes relajar DIP:
- Scripts simples
- Prototipos
- Clases de configuración
- Main/bootstrap
```

---

## 9. SOLID en la Práctica

### Aplicando SOLID Juntos

Los principios SOLID no se usan aislados, se complementan:

```
Ejemplo: Sistema de E-commerce

SRP: Separa Order, OrderCalculator, OrderRepository, OrderNotifier
OCP: PaymentMethod extensible (CreditCard, PayPal, Bitcoin)
LSP: Todos los PaymentMethod son intercambiables
ISP: Interfaces específicas (Payable, Refundable, Trackable)
DIP: OrderService depende de interfaces, no de implementaciones
```

### Ejemplo Completo: Sistema de Reporte

```java
// ✅ Aplicando todos los principios SOLID

// ===== SRP: Cada clase una responsabilidad =====

// Responsabilidad: modelo de datos
class Report {
  private String title;
  private List<String> data;

  public Report(String title, List<String> data) {
    this.title = title;
    this.data = data;
  }

  public String getTitle() { return title; }
  public List<String> getData() { return data; }
}

// ===== DIP: Abstracciones =====

// Alto nivel define interfaces
interface ReportDataSource {
  List<String> fetchData();
}

interface ReportFormatter {
  String format(Report report);
}

interface ReportDelivery {
  void deliver(String content, String recipient);
}

// ===== OCP + LSP: Extensible e intercambiable =====

// Fuentes de datos extensibles
class DatabaseDataSource implements ReportDataSource {
  @Override
  public List<String> fetchData() {
    return database.query("SELECT * FROM sales");
  }
}

class APIDataSource implements ReportDataSource {
  @Override
  public List<String> fetchData() {
    return api.get("/data");
  }
}

// Formateadores extensibles
class PDFFormatter implements ReportFormatter {
  @Override
  public String format(Report report) {
    return "<pdf>" + report.getTitle() + "</pdf>";
  }
}

class HTMLFormatter implements ReportFormatter {
  @Override
  public String format(Report report) {
    return "<html><h1>" + report.getTitle() + "</h1></html>";
  }
}

class ExcelFormatter implements ReportFormatter {
  @Override
  public String format(Report report) {
    return "<excel>" + report.getTitle() + "</excel>";
  }
}

// ===== ISP: Interfaces segregadas =====

// En lugar de una interface grande ReportDelivery con todo:
interface EmailDeliverable {
  void sendEmail(String content, String recipient);
}

interface PrintDeliverable {
  void print(String content);
}

interface CloudDeliverable {
  void uploadToCloud(String content, String path);
}

class EmailDelivery implements EmailDeliverable {
  @Override
  public void sendEmail(String content, String recipient) {
    EmailService.send(recipient, "Report", content);
  }
}

class PrintDelivery implements PrintDeliverable {
  @Override
  public void print(String content) {
    Printer.print(content);
  }
}

// ===== SRP + DIP: Servicio de alto nivel =====

class ReportGenerator {
  private final ReportDataSource dataSource;
  private final ReportFormatter formatter;
  private final EmailDeliverable delivery;

  public ReportGenerator(
      ReportDataSource dataSource,
      ReportFormatter formatter,
      EmailDeliverable delivery) {
    this.dataSource = dataSource;
    this.formatter = formatter;
    this.delivery = delivery;
  }

  public void generateAndSend(String recipient) {
    // 1. Obtener datos (abstracción)
    List<String> data = dataSource.fetchData();

    // 2. Crear reporte
    Report report = new Report("Sales Report", data);

    // 3. Formatear (abstracción)
    String formatted = formatter.format(report);

    // 4. Entregar (abstracción)
    delivery.sendEmail(formatted, recipient);
  }
}

// ===== Uso: Configuración =====

// Configuración 1: Reporte PDF por email desde BD
ReportGenerator pdfEmailReport = new ReportGenerator(
  new DatabaseDataSource(),
  new PDFFormatter(),
  new EmailDelivery()
);
pdfEmailReport.generateAndSend("manager@company.com");

// Configuración 2: Reporte Excel por email desde API
ReportGenerator excelEmailReport = new ReportGenerator(
  new APIDataSource(),
  new ExcelFormatter(),
  new EmailDelivery()
);
excelEmailReport.generateAndSend("director@company.com");

// ===== Testing: Fácil con mocks =====

@Test
void testReportGeneration() {
  // Mocks
  ReportDataSource mockDataSource = mock(ReportDataSource.class);
  ReportFormatter mockFormatter = mock(ReportFormatter.class);
  EmailDeliverable mockDelivery = mock(EmailDeliverable.class);

  when(mockDataSource.fetchData()).thenReturn(Arrays.asList("data1", "data2"));
  when(mockFormatter.format(any())).thenReturn("formatted report");

  // Test
  ReportGenerator generator = new ReportGenerator(
    mockDataSource,
    mockFormatter,
    mockDelivery
  );

  generator.generateAndSend("test@test.com");

  // Verificaciones
  verify(mockDataSource).fetchData();
  verify(mockFormatter).format(any());
  verify(mockDelivery).sendEmail("formatted report", "test@test.com");
}
```

### Refactoring a SOLID: Paso a Paso

**Código inicial (violando todos los principios):**

```java
// ❌ Código "espagueti"
class OrderManager {
  public void processOrder(int orderId) {
    // Violación SRP: múltiples responsabilidades
    // Violación DIP: dependencia directa de MySQL
    MySQLConnection db = new MySQLConnection();
    ResultSet rs = db.query("SELECT * FROM orders WHERE id = " + orderId);

    // Violación OCP: lógica hardcoded
    double total = rs.getDouble("total");
    if (rs.getString("payment_method").equals("credit_card")) {
      StripeAPI.charge(total);
    } else if (rs.getString("payment_method").equals("paypal")) {
      PayPalAPI.charge(total);
    }

    // Violación DIP: dependencia directa de SendGrid
    SendGridClient.send("Order processed");

    // Violación SRP: logging mezclado
    System.out.println("Order " + orderId + " processed");
  }
}
```

**Paso 1: Aplicar SRP (separar responsabilidades)**

```java
class Order {
  private int id;
  private double total;
  private String paymentMethod;
  // Solo datos
}

class OrderRepository {
  public Order findById(int id) { ... }
  // Solo persistencia
}

class PaymentProcessor {
  public void process(Order order) { ... }
  // Solo pagos
}

class NotificationService {
  public void notify(String message) { ... }
  // Solo notificaciones
}
```

**Paso 2: Aplicar DIP (abstracciones)**

```java
interface OrderRepository {
  Order findById(int id);
}

interface PaymentProcessor {
  void process(Payment payment);
}

interface NotificationService {
  void notify(String message);
}
```

**Paso 3: Aplicar OCP (extensibilidad)**

```java
interface PaymentMethod {
  void charge(double amount);
}

class CreditCardPayment implements PaymentMethod { ... }
class PayPalPayment implements PaymentMethod { ... }
```

**Paso 4: Servicio con DI**

```java
class OrderService {
  private final OrderRepository repository;
  private final PaymentProcessor paymentProcessor;
  private final NotificationService notificationService;

  public OrderService(
      OrderRepository repository,
      PaymentProcessor paymentProcessor,
      NotificationService notificationService) {
    this.repository = repository;
    this.paymentProcessor = paymentProcessor;
    this.notificationService = notificationService;
  }

  public void processOrder(int orderId) {
    Order order = repository.findById(orderId);
    paymentProcessor.process(order.getPayment());
    notificationService.notify("Order processed");
  }
}
```

---

## 10. SOLID y Arquitecturas

### SOLID en Clean Architecture

```
Clean Architecture SE BASA en SOLID:

Entities (Core):
- SRP: Cada entidad una responsabilidad
- OCP: Extensible sin modificar
- LSP: Jerarquías correctas

Use Cases:
- SRP: Cada use case una operación
- DIP: Dependen de interfaces (puertos)

Interface Adapters:
- ISP: Interfaces específicas por cliente
- DIP: Implementan interfaces de Use Cases

Frameworks & Drivers:
- OCP: Intercambiables
- DIP: Dependen de abstracciones superiores
```

### SOLID en Hexagonal Architecture

```
Domain (Core):
- SRP: Entidades con responsabilidad única
- OCP: Abierto a extensión

Ports (Interfaces):
- ISP: Interfaces pequeñas y específicas
- DIP: Dominio define interfaces

Adapters:
- OCP: Múltiples adaptadores para mismo puerto
- LSP: Todos los adaptadores intercambiables
- DIP: Dependen de puertos
```

### SOLID en Microservicios

```
Cada Microservicio:
- SRP: Un bounded context, una responsabilidad
- OCP: Extensible sin modificar otros servicios

Comunicación:
- DIP: Servicios dependen de contratos (APIs), no de implementaciones
- ISP: APIs específicas por cliente (API Gateway pattern)
```

---

## 11. Errores Comunes

### Error #1: Sobre-aplicar SOLID

```java
// ❌ OVERKILL: Abstracción innecesaria
interface StringConcatenator {
  String concatenate(String a, String b);
}

class DefaultStringConcatenator implements StringConcatenator {
  @Override
  public String concatenate(String a, String b) {
    return a + b;  // Operación trivial
  }
}

// ✅ MEJOR: No todo necesita abstracción
class UserGreeting {
  public String greet(String name) {
    return "Hello, " + name;  // Simple y directo
  }
}
```

**Regla:**
No apliques SOLID "por las dudas". Aplícalo cuando:
- Haya complejidad real
- Múltiples implementaciones posibles
- Código que cambia frecuentemente

### Error #2: Abstracciones Prematuras

```java
// ❌ Premature abstraction
interface UserService {
  void createUser(User user);
}

class UserServiceImpl implements UserService {
  // Solo una implementación
  // Nunca habrá otra
  // Interface innecesaria
}

// ✅ MEJOR: Espera a necesitar la abstracción
class UserService {
  public void createUser(User user) {
    // Implementación directa
  }
}

// Cuando necesites segunda implementación → ENTONCES abstrae
```

### Error #3: ISP Llevado al Extremo

```java
// ❌ TOO GRANULAR
interface Printable {
  void print();
}

interface Displayable {
  void display();
}

interface Loggable {
  void log();
}

interface Serializable {
  void serialize();
}

// Clase implementa 20 interfaces minúsculas
class Report implements Printable, Displayable, Loggable,
                        Serializable, Exportable, Importable,
                        Validateable, Transformable, ... {
  // Complejidad innecesaria
}

// ✅ MEJOR: Balance
interface Report {
  void print();
  void display();  // Métodos cohesivos juntos
}

interface Persistable {
  void save();
  void load();  // Operaciones relacionadas
}
```

### Error #4: Confundir DIP con DI Frameworks

```
DIP (principio) ≠ Spring/Guice/Dagger (herramientas)

DIP: Principio de diseño (depende de abstracciones)
DI Framework: Herramienta que facilita inyección

Puedes aplicar DIP sin frameworks:
```

```java
// DIP sin framework
interface Database {
  void save(String data);
}

class UserService {
  private Database database;

  public UserService(Database database) {  // Manual DI
    this.database = database;
  }
}

// Manual wiring
Database db = new MySQLDatabase();
UserService service = new UserService(db);
```

### Error #5: LSP Violado con Excepciones

```java
// ❌ VIOLACIÓN SUTIL
interface Account {
  void withdraw(double amount);  // No lanza excepciones según contrato
}

class SavingsAccount implements Account {
  @Override
  public void withdraw(double amount) {
    if (amount > 1000) {
      // ❌ Lanza excepción que base no lanzaba
      throw new WithdrawalLimitExceededException();
    }
    // ...
  }
}

// ✅ MEJOR: Consistencia en contratos
interface Account {
  WithdrawalResult withdraw(double amount);  // Explícito en la interface
}

class WithdrawalResult {
  private boolean success;
  private String errorMessage;
  // ...
}
```

---

## 12. Balance y Pragmatismo

### El Principio 80/20

```
80% del valor viene de aplicar SOLID en:
✓ Lógica de negocio
✓ Servicios de aplicación
✓ Código que cambia frecuentemente

20% del esfuerzo en:
- DTOs simples
- Configuración
- Scripts
- Código que nunca cambia
```

### Cuándo Relajar SOLID

```
Está OK no aplicar SOLID en:
✓ Prototipos descartables
✓ Scripts de uso único
✓ Código trivial (getters/setters)
✓ Configuración
✓ Tests (pueden romper DIP para simplicidad)
✓ Bootstrap/Main
```

### Refactoring Gradual

```
No refactorices todo el código legacy a SOLID de golpe.

Estrategia:
1. Identifica puntos de dolor (bugs frecuentes, cambios difíciles)
2. Aplica SOLID en esas áreas primero
3. Cuando toques código legacy, mejóralo ("boy scout rule")
4. Código nuevo: sigue SOLID desde el inicio
5. Migración gradual y segura
```

### SOLID vs Simplicidad

```
SOLID no siempre = más simple

Ejemplo: Script de 50 líneas
- Sin SOLID: 1 archivo, 50 líneas, fácil de entender
- Con SOLID: 10 archivos, 200 líneas, interfaces, DI

Para scripts simples: KISS > SOLID
Para aplicaciones complejas: SOLID > KISS inicial

Elige según contexto
```

---

## 13. Preguntas Frecuentes

### ¿Debo aplicar los 5 principios siempre?

**No necesariamente.**

```
Prioridad:
1. SRP - Siempre que sea posible (fundamental)
2. DIP - Muy importante (testabilidad)
3. OCP - Cuando haya variantes
4. ISP - En interfaces públicas
5. LSP - Cuando uses herencia

Empieza con SRP y DIP, agrega otros según necesidad
```

### ¿SOLID es solo para Java/C#?

**No, aplica a cualquier lenguaje OOP:**

```
Java/C#: Interfaces, clases abstractas
Python: Duck typing, protocols
JavaScript: Objetos, módulos
TypeScript: Interfaces, tipos

Los principios son independientes del lenguaje
```

### ¿SOLID en lenguajes funcionales?

**Parcialmente:**

```
Lenguajes funcionales (Haskell, F#, Elixir):
- SRP: Funciones puras con una responsabilidad
- OCP: Composición de funciones
- DIP: Higher-order functions, dependency injection

LSP, ISP: Menos relevantes (no hay herencia/interfaces tradicionales)
```

### ¿SOLID hace el código más complejo?

**Inicialmente sí, a largo plazo no:**

```
Código sin SOLID:
- Día 1: Rápido, simple (50 líneas)
- Mes 6: Espagueti, difícil cambiar (1000 líneas mezcladas)
- Año 1: Reescritura necesaria

Código con SOLID:
- Día 1: Más lento, más archivos (200 líneas)
- Mes 6: Organizado, fácil cambiar
- Año 1: Evoluciona sin problemas

Trade-off: complejidad inicial vs mantenibilidad futura
```

### ¿Cuándo empezar a aplicar SOLID?

```
En un proyecto nuevo:
✓ Desde el inicio en lógica de negocio
✓ Relaja en prototipos/MVPs descartables

En proyecto existente:
✓ Nuevas features: sigue SOLID
✓ Código legacy: refactoriza gradualmente cuando lo toques
✓ No refactorices código que funciona y no cambia
```

### ¿SOLID y Clean Code son lo mismo?

**No:**

```
Clean Code:
- Cómo escribir código legible (naming, funciones pequeñas, etc.)
- Nivel: línea de código, función

SOLID:
- Cómo organizar clases y módulos
- Nivel: clase, módulo, sistema

Son complementarios:
Clean Code + SOLID = Código de calidad
```

### ¿Puedo violar SOLID si tengo una razón?

**Sí, son principios, no leyes:**

```
Razones válidas para violar SOLID:
✓ Performance crítica (medida, no asumida)
✓ Deadline extremo (tech debt consciente)
✓ Código trivial
✓ Restricciones de framework/plataforma

Pero:
- Documenta la razón
- Registra como tech debt
- Planifica refactorización futura
```

---

## Resumen Final

### Los 5 Principios en Una Frase

```
S - Single Responsibility
    → Una clase, una razón para cambiar

O - Open/Closed
    → Abierto a extensión, cerrado a modificación

L - Liskov Substitution
    → Las subclases deben ser sustituibles por su clase base

I - Interface Segregation
    → Interfaces pequeñas y específicas, no monolíticas

D - Dependency Inversion
    → Depende de abstracciones, no de concreciones
```

### Checklist SOLID

**Al escribir una clase, pregúntate:**

```
SRP:
□ ¿Puedo describir esta clase sin usar "Y"?
□ ¿Tiene una única razón para cambiar?
□ ¿El nombre es específico (no Manager, Handler, Util)?

OCP:
□ ¿Puedo agregar funcionalidad sin modificar esta clase?
□ ¿Usa abstracciones para puntos de extensión?
□ ¿Evito if/switch para variantes?

LSP:
□ ¿Las subclases pueden reemplazar la clase base sin sorpresas?
□ ¿No tengo métodos vacíos o con UnsupportedOperationException?
□ ¿Las subclases respetan el contrato de la base?

ISP:
□ ¿Las interfaces son pequeñas y cohesivas?
□ ¿Los clientes solo implementan métodos que usan?
□ ¿No tengo "fat interfaces"?

DIP:
□ ¿Las dependencias son interfaces, no clases concretas?
□ ¿Uso inyección de dependencias?
□ ¿El código de alto nivel no conoce detalles de bajo nivel?
```

### Orden de Aprendizaje Recomendado

```
1. SRP → El más fundamental, más fácil de entender
2. DIP → Critical para testabilidad y desacoplamiento
3. OCP → Importante para extensibilidad
4. ISP → Complementa DIP y SRP
5. LSP → El más sutil, aprende último
```

### Principios Relacionados

SOLID es parte de un ecosistema mayor:

```
SOLID
  ↓
GRASP (General Responsibility Assignment Software Patterns)
  ↓
Design Patterns (GoF)
  ↓
Architectural Patterns (Clean, Hexagonal)
  ↓
Domain-Driven Design
```

---

## Recursos Adicionales

### Libros Esenciales

```
📚 "Clean Code" - Robert C. Martin
   → Código limpio a nivel de función/clase

📚 "Clean Architecture" - Robert C. Martin
   → SOLID aplicado a arquitectura

📚 "Agile Software Development" - Robert C. Martin
   → El libro original donde se formalizó SOLID

📚 "Head First Design Patterns"
   → Patrones de diseño que usan SOLID

📚 "Refactoring" - Martin Fowler
   → Cómo refactorizar a SOLID
```

### Artículos y Blogs

```
🔗 The Principles of OOD (Uncle Bob):
   http://butunclebob.com/ArticleS.UncleBob.PrinciplesOfOod

🔗 SOLID Principles Explained (FreeCodeCamp)

🔗 Martin Fowler's Blog:
   https://martinfowler.com
```

---

## Conclusión

### La Esencia de SOLID

**SOLID no es:**
- ❌ Un conjunto de reglas rígidas
- ❌ Algo que se aplica ciegamente
- ❌ Una receta mágica

**SOLID es:**
- ✅ Una guía para diseño de software
- ✅ Un conjunto de principios complementarios
- ✅ Una forma de pensar sobre responsabilidades y dependencias
- ✅ Una inversión en mantenibilidad a largo plazo

### El Objetivo Final

```
Código que sea:
✓ Fácil de entender
✓ Fácil de cambiar
✓ Fácil de extender
✓ Fácil de testear
✓ Robusto ante cambios

SOLID es el camino, no el destino
```

### Última Reflexión

> **"Make it work, make it right, make it fast"** - Kent Beck

```
1. Make it work → Hazlo funcionar primero
2. Make it right → Aplica SOLID para hacerlo mantenible
3. Make it fast → Optimiza si (y solo si) es necesario

No sobre-ingenieríes desde el inicio.
Aplica SOLID cuando la complejidad lo justifique.
```

**El mejor código es el que:**
- ✅ Resuelve el problema
- ✅ Es comprensible
- ✅ Puede evolucionar

**SOLID te ayuda a lograrlo.**

---

**Happy SOLID Coding!** 🎯

---

**Última actualización:** Diciembre 2025
**Versión:** 1.0
**Autor:** Manual de Principios SOLID
**Licencia:** Uso libre para aprendizaje y aplicación profesional
