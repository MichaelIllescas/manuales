# Manual de Domain-Driven Design (DDD)
## La Guía Definitiva para Principiantes

> **Aprende a diseñar software complejo centrado en el dominio de negocio**
>
> Este manual explica Domain-Driven Design de forma simple, práctica y con ejemplos reales, sin asumir conocimientos previos.

---

## 📚 Índice

1. [¿Qué es Domain-Driven Design?](#1-qué-es-domain-driven-design)
2. [El Problema que Resuelve](#2-el-problema-que-resuelve)
3. [Los Dos Lados de DDD](#3-los-dos-lados-de-ddd)
4. [Ubiquitous Language (Lenguaje Ubicuo)](#4-ubiquitous-language-lenguaje-ubicuo)
5. [Building Blocks Tácticos](#5-building-blocks-tácticos)
6. [Patrones Estratégicos](#6-patrones-estratégicos)
7. [Ejemplo Práctico Completo](#7-ejemplo-práctico-completo)
8. [DDD y Arquitecturas (Clean, Hexagonal)](#8-ddd-y-arquitecturas)
9. [DDD en el Proyecto QRest](#9-ddd-en-el-proyecto-qrest)
10. [Ventajas y Desventajas](#10-ventajas-y-desventajas)
11. [Cuándo Usar (y Cuándo No)](#11-cuándo-usar-y-cuándo-no)
12. [Errores Comunes](#12-errores-comunes)
13. [Implementación Práctica](#13-implementación-práctica)
14. [Preguntas Frecuentes](#14-preguntas-frecuentes)

---

## 1. ¿Qué es Domain-Driven Design?

### Definición Simple

**Domain-Driven Design (DDD)** es una metodología de diseño de software propuesta por **Eric Evans** que pone el **dominio del negocio** en el centro del desarrollo.

```
┌─────────────────────────────────────────────┐
│         ENFOQUE TRADICIONAL                 │
│  Tecnología → Framework → Base de Datos     │
│       ↓                                     │
│  "¿Cómo lo construimos?"                    │
└─────────────────────────────────────────────┘

┌─────────────────────────────────────────────┐
│         ENFOQUE DDD                         │
│  Dominio de Negocio → Modelo → Código      │
│       ↓                                     │
│  "¿Qué problema resolvemos?"                │
└─────────────────────────────────────────────┘
```

### Objetivo Principal

```
Crear un modelo de software que refleje fielmente:
✓ El problema del negocio
✓ Las reglas del dominio
✓ El lenguaje de los expertos
✓ La complejidad inherente del dominio
```

### Analogía Simple

Imagina construir un sistema de reservas de hotel:

```
❌ SIN DDD:
- Desarrollador: "Tenemos una tabla 'bookings' con campos..."
- Experto del hotel: "¿Qué es un 'booking'? Nosotros hacemos 'reservas'"
- Desarrollador: "El 'status' puede ser 1, 2 o 3"
- Experto: "Nosotros hablamos de 'confirmada', 'pendiente', 'cancelada'"
→ Modelo desconectado del negocio

✅ CON DDD:
- Todos hablan de "Reserva" (Reservation)
- Todos entienden estados: Confirmada, Pendiente, Cancelada
- El código refleja exactamente el lenguaje del negocio
- Los expertos pueden leer y entender el modelo
→ Modelo alineado con el negocio
```

### Definición Formal

> **DDD es un enfoque de desarrollo de software que:**
> 1. **Pone el dominio del negocio en el centro**
> 2. **Colabora estrechamente con expertos del dominio**
> 3. **Usa un lenguaje compartido (Ubiquitous Language)**
> 4. **Modela la complejidad del dominio con precisión**
> 5. **Separa el dominio de detalles técnicos**

---

## 2. El Problema que Resuelve

### Software Tradicional (Sin DDD)

```java
// ❌ Código desconectado del negocio
@Entity
@Table(name = "hotel_bookings")
public class Booking {
  @Id
  private Long id;

  private String guestName;
  private Date checkIn;
  private Date checkOut;
  private int status;  // ¿Qué significa 1, 2, 3?
  private double price;

  // Getters y setters (modelo anémico)
  public int getStatus() { return status; }
  public void setStatus(int status) { this.status = status; }
}

// Lógica de negocio en el servicio (fuera del dominio)
@Service
public class BookingService {
  public void cancelBooking(Long bookingId) {
    Booking booking = repository.findById(bookingId);

    // ❌ Regla de negocio dispersa
    if (booking.getStatus() == 1) {  // ¿Qué es 1?
      // ¿Puedo cancelar 24 horas antes?
      // ¿Hay penalización?
      // ¿Cómo se calcula el reembolso?
      booking.setStatus(3);  // ¿Qué es 3?
    }

    repository.save(booking);
  }
}
```

**Problemas:**

```
❌ Modelo anémico (solo getters/setters, sin lógica)
❌ Reglas de negocio dispersas en servicios
❌ Lenguaje técnico (status=1, status=3)
❌ Imposible para un experto del hotel entender el código
❌ Lógica de negocio mezclada con infraestructura
❌ No refleja conceptos del dominio (políticas de cancelación, reembolsos)
```

### Con Domain-Driven Design

```java
// ✅ Modelo rico que refleja el dominio
public class Reservation {
  private final ReservationId id;
  private final GuestName guestName;
  private final DateRange stayPeriod;
  private ReservationStatus status;
  private final Money totalPrice;
  private final CancellationPolicy cancellationPolicy;

  // Constructor con validaciones de dominio
  public Reservation(GuestName guestName, DateRange stayPeriod,
                     Money totalPrice, CancellationPolicy policy) {
    if (stayPeriod.isInPast()) {
      throw new InvalidReservationException("Cannot book past dates");
    }
    this.id = ReservationId.generate();
    this.guestName = guestName;
    this.stayPeriod = stayPeriod;
    this.totalPrice = totalPrice;
    this.cancellationPolicy = policy;
    this.status = ReservationStatus.PENDING;
  }

  // Lógica de negocio EN el dominio
  public CancellationResult cancel(LocalDateTime now) {
    if (!this.status.canBeCancelled()) {
      throw new CannotCancelReservationException(
        "Reservation in status " + status + " cannot be cancelled"
      );
    }

    // Regla de negocio: política de cancelación
    RefundAmount refund = cancellationPolicy.calculateRefund(
      totalPrice,
      stayPeriod,
      now
    );

    this.status = ReservationStatus.CANCELLED;

    return new CancellationResult(refund, status);
  }

  public void confirm() {
    if (this.status != ReservationStatus.PENDING) {
      throw new InvalidStateTransitionException(
        "Can only confirm pending reservations"
      );
    }
    this.status = ReservationStatus.CONFIRMED;
  }

  // Getters (sin setters públicos - inmutabilidad cuando es posible)
  public ReservationId getId() { return id; }
  public ReservationStatus getStatus() { return status; }
}

// Value Objects (objetos de valor)
public enum ReservationStatus {
  PENDING, CONFIRMED, CHECKED_IN, CHECKED_OUT, CANCELLED;

  public boolean canBeCancelled() {
    return this == PENDING || this == CONFIRMED;
  }
}

public class DateRange {
  private final LocalDate checkIn;
  private final LocalDate checkOut;

  public DateRange(LocalDate checkIn, LocalDate checkOut) {
    if (checkOut.isBefore(checkIn)) {
      throw new InvalidDateRangeException("Check-out must be after check-in");
    }
    this.checkIn = checkIn;
    this.checkOut = checkOut;
  }

  public boolean isInPast() {
    return checkIn.isBefore(LocalDate.now());
  }

  public int nights() {
    return (int) ChronoUnit.DAYS.between(checkIn, checkOut);
  }
}

// Domain Service (lógica que no pertenece a una entidad específica)
public class CancellationPolicy {
  private final int hoursBeforeCheckIn;
  private final BigDecimal penaltyPercentage;

  public RefundAmount calculateRefund(Money totalPrice,
                                      DateRange stayPeriod,
                                      LocalDateTime now) {
    long hoursUntilCheckIn = ChronoUnit.HOURS.between(
      now,
      stayPeriod.getCheckIn().atStartOfDay()
    );

    if (hoursUntilCheckIn >= hoursBeforeCheckIn) {
      // Cancelación sin penalización
      return new RefundAmount(totalPrice, BigDecimal.ZERO);
    } else {
      // Aplicar penalización
      BigDecimal penalty = totalPrice.multiply(penaltyPercentage);
      BigDecimal refund = totalPrice.subtract(penalty);
      return new RefundAmount(new Money(refund), new Money(penalty));
    }
  }
}
```

**Beneficios:**

```
✓ Modelo rico (lógica de negocio en las entidades)
✓ Lenguaje del negocio (Reservation, confirm(), cancel())
✓ Reglas de negocio encapsuladas en el dominio
✓ Expertos del hotel pueden entender el código
✓ Validaciones de negocio en el modelo
✓ Conceptos del dominio explícitos (CancellationPolicy, RefundAmount)
```

---

## 3. Los Dos Lados de DDD

DDD tiene dos aspectos complementarios:

### DDD Estratégico (Strategic Design)

**Qué es:** El "big picture" - cómo organizar el sistema en módulos de alto nivel.

```
┌─────────────────────────────────────────────────┐
│     SISTEMA COMPLETO DE E-COMMERCE              │
├─────────────────────────────────────────────────┤
│  ┌───────────────┐  ┌──────────────┐           │
│  │ SALES         │  │ INVENTORY    │           │
│  │ (Ventas)      │  │ (Inventario) │           │
│  │               │  │              │           │
│  │ - Order       │  │ - Product    │           │
│  │ - Customer    │  │ - Stock      │           │
│  └───────────────┘  └──────────────┘           │
│                                                 │
│  ┌───────────────┐  ┌──────────────┐           │
│  │ SHIPPING      │  │ BILLING      │           │
│  │ (Envíos)      │  │ (Facturación)│           │
│  │               │  │              │           │
│  │ - Shipment    │  │ - Invoice    │           │
│  │ - Address     │  │ - Payment    │           │
│  └───────────────┘  └──────────────┘           │
└─────────────────────────────────────────────────┘

Cada caja = Bounded Context (contexto delimitado)
```

**Conceptos clave:**

```
✓ Bounded Context: Límites explícitos de modelos
✓ Ubiquitous Language: Lenguaje compartido
✓ Context Mapping: Relaciones entre contextos
✓ Subdomains: Áreas de negocio (Core, Supporting, Generic)
```

### DDD Táctico (Tactical Design)

**Qué es:** Los "building blocks" - cómo modelar el dominio en detalle.

```
┌─────────────────────────────────────────────────┐
│     DENTRO DE UN BOUNDED CONTEXT                │
├─────────────────────────────────────────────────┤
│                                                 │
│  Entities (Entidades)                           │
│  → Objetos con identidad única                  │
│  → Ejemplo: Order, Customer, Product            │
│                                                 │
│  Value Objects (Objetos de Valor)               │
│  → Objetos sin identidad, definidos por valor   │
│  → Ejemplo: Money, Address, DateRange           │
│                                                 │
│  Aggregates (Agregados)                         │
│  → Grupos de entidades tratados como unidad     │
│  → Ejemplo: Order (raíz) + OrderItems           │
│                                                 │
│  Domain Services (Servicios de Dominio)         │
│  → Lógica que no pertenece a una entidad        │
│  → Ejemplo: PricingService, TaxCalculator       │
│                                                 │
│  Repositories (Repositorios)                    │
│  → Abstracción para persistencia                │
│  → Ejemplo: OrderRepository                     │
│                                                 │
│  Domain Events (Eventos de Dominio)             │
│  → Hechos que ocurrieron en el dominio          │
│  → Ejemplo: OrderPlaced, PaymentReceived        │
│                                                 │
└─────────────────────────────────────────────────┘
```

**Relación:**

```
Estratégico: DÓNDE va el código (contextos)
Táctico: CÓMO modelar el código (patrones)

Primero diseñas estratégicamente (contextos)
Luego modelas tácticamente (entidades, value objects, etc.)
```

---

## 4. Ubiquitous Language (Lenguaje Ubicuo)

### Definición

> **Un lenguaje compartido entre desarrolladores y expertos del dominio que se usa consistentemente en el código, documentación, conversaciones y modelos.**

### El Problema

```
DESARROLLADOR dice:               EXPERTO DE NEGOCIO dice:
- "User"                          - "Cliente"
- "Transaction"                   - "Pedido"
- "Status flag"                   - "Estado de la orden"
- "Processing pipeline"           - "Flujo de preparación"

Resultado:
❌ Confusión
❌ Malentendidos
❌ Código desconectado del negocio
❌ Bugs por interpretación incorrecta
```

### La Solución: Ubiquitous Language

```
TODOS dicen lo mismo:
- "Cliente" → Customer (en código)
- "Pedido" → Order (en código)
- "Estado: Pendiente, Confirmado, Enviado" → OrderStatus enum
- "Preparar pedido" → prepareOrder() (método)

Resultado:
✓ Comunicación clara
✓ Código comprensible para expertos
✓ Menos bugs
✓ Dominio explícito
```

### Ejemplo Real: Sistema Bancario

```java
// ❌ SIN Ubiquitous Language
public class Account {
  private double balance;

  public void doTransaction(double amount, int type) {
    if (type == 1) {
      balance += amount;
    } else if (type == 2) {
      balance -= amount;
    }
  }
}

// ¿Qué es "type 1"? ¿Y "type 2"?
// El experto bancario no entiende este código

// ✅ CON Ubiquitous Language
public class BankAccount {
  private Money balance;

  // Lenguaje del dominio: "depositar"
  public void deposit(Money amount) {
    if (amount.isNegativeOrZero()) {
      throw new InvalidDepositException("Deposit must be positive");
    }
    this.balance = this.balance.add(amount);
  }

  // Lenguaje del dominio: "retirar"
  public void withdraw(Money amount) {
    if (amount.isGreaterThan(balance)) {
      throw new InsufficientFundsException();
    }
    this.balance = this.balance.subtract(amount);
  }

  // Lenguaje del dominio: "transferir"
  public void transferTo(BankAccount recipient, Money amount) {
    this.withdraw(amount);  // Usa conceptos del dominio
    recipient.deposit(amount);
  }
}

// El experto bancario puede leer esto y decir:
// "Sí, así funciona un depósito. Así funciona una transferencia."
```

### Reglas del Ubiquitous Language

```
1. MISMO LENGUAJE EN TODO:
   ✓ Conversaciones con expertos
   ✓ Documentación
   ✓ Nombres de clases, métodos, variables
   ✓ Tests
   ✓ Diagramas

2. SI UN EXPERTO NO LO DIRÍA, NO LO USES:
   ❌ processData()
   ❌ handleRequest()
   ❌ doStuff()

   ✓ placeOrder()
   ✓ cancelReservation()
   ✓ approvePayment()

3. SI HAY AMBIGÜEDAD, DISCUTIR CON EXPERTOS:
   - "¿'Cliente' es el que compra o el que paga?"
   - "¿'Pedido' incluye cotizaciones o solo compras confirmadas?"

4. REFINAR EL LENGUAJE CONTINUAMENTE:
   - Descubrir nuevos conceptos → agregar al lenguaje
   - Términos confusos → clarificar con expertos
   - Código anticuado → refactorizar
```

### Beneficios

```
✓ Comunicación eficiente (no traducción mental)
✓ Código autodocumentado
✓ Menos bugs por malentendidos
✓ Expertos pueden validar el modelo
✓ Onboarding más rápido
✓ Conocimiento del negocio preservado en el código
```

---

## 5. Building Blocks Tácticos

### 5.1 Entities (Entidades)

**Definición:**
> **Objetos con identidad única que persiste a través del tiempo, independientemente de sus atributos.**

**Características:**

```
✓ Tiene identidad única (ID)
✓ Puede cambiar sus atributos pero sigue siendo la misma entidad
✓ Tiene ciclo de vida
✓ Contiene lógica de negocio
```

**Ejemplo:**

```java
// ✅ Entity: Customer
public class Customer {
  private final CustomerId id;  // Identidad única
  private CustomerName name;
  private Email email;
  private Address address;

  // Constructor
  public Customer(CustomerId id, CustomerName name, Email email) {
    this.id = id;
    this.name = name;
    this.email = email;
  }

  // Lógica de negocio
  public void updateContactInfo(Email newEmail, Address newAddress) {
    if (!newEmail.isValid()) {
      throw new InvalidEmailException();
    }
    this.email = newEmail;
    this.address = newAddress;
  }

  // Identidad: dos Customer son el mismo si tienen el mismo ID
  @Override
  public boolean equals(Object obj) {
    if (!(obj instanceof Customer)) return false;
    Customer other = (Customer) obj;
    return this.id.equals(other.id);
  }

  @Override
  public int hashCode() {
    return id.hashCode();
  }
}

// Ejemplo de uso:
CustomerId id = new CustomerId("CUST-123");
Customer customer1 = new Customer(id, new CustomerName("John"), new Email("john@example.com"));
Customer customer2 = new Customer(id, new CustomerName("John Doe"), new Email("john.doe@example.com"));

// Son la MISMA entidad (mismo ID) aunque tengan atributos diferentes
customer1.equals(customer2);  // true
```

**¿Cuándo es una Entity?**

```
Pregúntate:
1. ¿Tiene identidad única que importa al negocio?
2. ¿Importa distinguir entre dos instancias aunque tengan los mismos datos?
3. ¿Tiene ciclo de vida (creación, modificación, eliminación)?

Ejemplos de Entities:
✓ Customer (cliente con ID único)
✓ Order (pedido con número único)
✓ Invoice (factura con número único)
✓ User (usuario con ID único)
✓ Product (producto con SKU único)

Ejemplos de NO Entities (probablemente Value Objects):
✗ Money (100 USD es 100 USD, sin identidad)
✗ Address (dirección definida por sus datos)
✗ DateRange (rango de fechas sin identidad)
```

---

### 5.2 Value Objects (Objetos de Valor)

**Definición:**
> **Objetos sin identidad conceptual, definidos únicamente por sus atributos. Son inmutables.**

**Características:**

```
✓ Sin identidad única (ID)
✓ Definido por sus valores
✓ Inmutable (no cambia después de creado)
✓ Puede ser reemplazado (no modificado)
✓ Encapsula lógica relacionada con el valor
```

**Ejemplo:**

```java
// ✅ Value Object: Money
public class Money {
  private final BigDecimal amount;
  private final Currency currency;

  // Constructor
  public Money(BigDecimal amount, Currency currency) {
    if (amount == null || currency == null) {
      throw new IllegalArgumentException("Amount and currency are required");
    }
    this.amount = amount;
    this.currency = currency;
  }

  // Operaciones que retornan NUEVO objeto (inmutabilidad)
  public Money add(Money other) {
    if (!this.currency.equals(other.currency)) {
      throw new CurrencyMismatchException();
    }
    return new Money(this.amount.add(other.amount), this.currency);
  }

  public Money subtract(Money other) {
    if (!this.currency.equals(other.currency)) {
      throw new CurrencyMismatchException();
    }
    return new Money(this.amount.subtract(other.amount), this.currency);
  }

  public Money multiply(BigDecimal factor) {
    return new Money(this.amount.multiply(factor), this.currency);
  }

  // Igualdad basada en valor (no identidad)
  @Override
  public boolean equals(Object obj) {
    if (!(obj instanceof Money)) return false;
    Money other = (Money) obj;
    return this.amount.equals(other.amount) &&
           this.currency.equals(other.currency);
  }

  @Override
  public int hashCode() {
    return Objects.hash(amount, currency);
  }

  // Getters (NO setters - inmutable)
  public BigDecimal getAmount() { return amount; }
  public Currency getCurrency() { return currency; }
}

// Uso:
Money price1 = new Money(new BigDecimal("100"), Currency.USD);
Money price2 = new Money(new BigDecimal("100"), Currency.USD);

price1.equals(price2);  // true (mismo valor)
price1 == price2;       // false (diferentes objetos en memoria)

Money total = price1.add(price2);  // Nuevo objeto Money(200, USD)
// price1 y price2 NO cambiaron (inmutabilidad)
```

**Más ejemplos de Value Objects:**

```java
// Email
public class Email {
  private final String value;

  public Email(String value) {
    if (!isValidEmail(value)) {
      throw new InvalidEmailException("Invalid email format");
    }
    this.value = value.toLowerCase();
  }

  private boolean isValidEmail(String email) {
    return email.matches("^[A-Za-z0-9+_.-]+@(.+)$");
  }

  public String getValue() { return value; }

  @Override
  public boolean equals(Object obj) {
    if (!(obj instanceof Email)) return false;
    return this.value.equals(((Email) obj).value);
  }
}

// Address
public class Address {
  private final String street;
  private final String city;
  private final String country;
  private final PostalCode postalCode;

  public Address(String street, String city, String country, PostalCode postalCode) {
    this.street = street;
    this.city = city;
    this.country = country;
    this.postalCode = postalCode;
  }

  public String fullAddress() {
    return street + ", " + city + ", " + country + " " + postalCode.getValue();
  }

  // Getters, equals, hashCode...
}

// DateRange
public class DateRange {
  private final LocalDate start;
  private final LocalDate end;

  public DateRange(LocalDate start, LocalDate end) {
    if (end.isBefore(start)) {
      throw new InvalidDateRangeException("End date must be after start date");
    }
    this.start = start;
    this.end = end;
  }

  public boolean contains(LocalDate date) {
    return !date.isBefore(start) && !date.isAfter(end);
  }

  public int days() {
    return (int) ChronoUnit.DAYS.between(start, end);
  }

  // Getters, equals, hashCode...
}
```

**¿Cuándo es un Value Object?**

```
Pregúntate:
1. ¿La identidad importa, o solo el valor?
2. ¿Dos objetos con los mismos datos son intercambiables?
3. ¿Debería ser inmutable?

Ejemplos de Value Objects:
✓ Money (100 USD = 100 USD)
✓ Email (john@example.com)
✓ Address (calle, ciudad, país)
✓ Color (RGB values)
✓ Temperature (25°C)
✓ Coordinates (lat, lng)
```

**Ventajas de Value Objects:**

```
✓ Encapsulación: Lógica de validación en un lugar
✓ Inmutabilidad: Thread-safe, sin efectos secundarios
✓ Expresividad: Money vs double, Email vs String
✓ Type safety: No confundir email con username
✓ Reutilización: Mismo Value Object en múltiples entidades
```

---

### 5.3 Aggregates (Agregados)

**Definición:**
> **Un grupo de entidades y value objects tratados como una unidad única para cambios de datos. Tiene una raíz (Aggregate Root) que es el único punto de entrada.**

**Características:**

```
✓ Tiene una raíz (Aggregate Root)
✓ Raíz controla acceso y mantiene invariantes
✓ Referencias externas solo a la raíz
✓ Límite de transaccionalidad
✓ Unidad de consistencia
```

**Visualización:**

```
┌─────────────────────────────────────────┐
│         AGGREGATE: Order                │
│                                         │
│  ┌───────────────────────────────────┐  │
│  │  Order (AGGREGATE ROOT)           │  │  ← Única entrada
│  │  - orderId                        │  │
│  │  - customerId                     │  │
│  │  - status                         │  │
│  │  - total                          │  │
│  └─────────────────┬─────────────────┘  │
│                    │                    │
│       ┌────────────┴──────────────┐     │
│       │                           │     │
│  ┌────▼────────┐          ┌───────▼────┐│
│  │ OrderItem   │          │ OrderItem  ││  ← Internas
│  │ - productId │          │ - quantity ││
│  │ - quantity  │          │ - price    ││
│  │ - price     │          └────────────┘│
│  └─────────────┘                        │
└─────────────────────────────────────────┘

Reglas:
- Acceso solo a través de Order (raíz)
- Order mantiene invariantes (ej: total = suma de items)
- OrderItem no se crea independientemente
```

**Ejemplo:**

```java
// ✅ Aggregate: Order con OrderItems
public class Order {  // AGGREGATE ROOT
  private final OrderId id;
  private final CustomerId customerId;
  private OrderStatus status;
  private final List<OrderItem> items;  // Entidades internas
  private Money total;

  // Constructor
  public Order(OrderId id, CustomerId customerId) {
    this.id = id;
    this.customerId = customerId;
    this.status = OrderStatus.PENDING;
    this.items = new ArrayList<>();
    this.total = Money.ZERO;
  }

  // Método público: única forma de agregar items
  public void addItem(Product product, int quantity) {
    // Validación de invariante
    if (this.status != OrderStatus.PENDING) {
      throw new OrderAlreadyProcessedException(
        "Cannot add items to processed order"
      );
    }

    if (quantity <= 0) {
      throw new InvalidQuantityException("Quantity must be positive");
    }

    // Crear item interno
    OrderItem item = new OrderItem(product.getId(), quantity, product.getPrice());
    this.items.add(item);

    // Mantener invariante: total = suma de items
    recalculateTotal();
  }

  public void removeItem(ProductId productId) {
    if (this.status != OrderStatus.PENDING) {
      throw new OrderAlreadyProcessedException();
    }

    items.removeIf(item -> item.getProductId().equals(productId));
    recalculateTotal();
  }

  public void confirm() {
    if (items.isEmpty()) {
      throw new EmptyOrderException("Cannot confirm empty order");
    }

    this.status = OrderStatus.CONFIRMED;
  }

  // Invariante: total debe ser suma de items
  private void recalculateTotal() {
    this.total = items.stream()
        .map(OrderItem::subtotal)
        .reduce(Money.ZERO, Money::add);
  }

  // Getters (items es lista inmutable hacia afuera)
  public List<OrderItem> getItems() {
    return Collections.unmodifiableList(items);
  }

  public Money getTotal() { return total; }
  public OrderStatus getStatus() { return status; }
}

// Entidad interna (NO aggregate root)
class OrderItem {
  private final ProductId productId;
  private int quantity;
  private final Money unitPrice;

  // Constructor package-private (solo Order puede crear)
  OrderItem(ProductId productId, int quantity, Money unitPrice) {
    this.productId = productId;
    this.quantity = quantity;
    this.unitPrice = unitPrice;
  }

  Money subtotal() {
    return unitPrice.multiply(BigDecimal.valueOf(quantity));
  }

  ProductId getProductId() { return productId; }
}

// Uso:
Order order = new Order(OrderId.generate(), customerId);
order.addItem(product1, 2);  // ✓ A través de la raíz
order.addItem(product2, 1);
order.confirm();

// ❌ NO HACER: crear OrderItem directamente
// OrderItem item = new OrderItem(...);  // No es posible (package-private)
```

**Reglas de Aggregates:**

```
1. RAÍZ ÚNICA:
   - Solo la raíz tiene identidad global
   - Referencias externas solo a la raíz
   - ❌ NO referenciar OrderItem desde fuera del Order

2. INVARIANTES:
   - La raíz es responsable de mantener invariantes
   - Ejemplo: Order garantiza que total = suma de items
   - Toda operación debe dejar el agregado en estado válido

3. LÍMITE DE TRANSACCIÓN:
   - Un agregado = una transacción
   - Modificar múltiples agregados = eventual consistency

4. TAMAÑO:
   - Agregados pequeños (mejor performance)
   - Solo incluir lo que DEBE cambiar junto

5. REFERENCIAS ENTRE AGREGADOS:
   - Por ID, no por objeto
   - Ejemplo: Order tiene CustomerId, no Customer objeto
```

**¿Cómo identificar límites de Aggregates?**

```
Pregúntate:
1. ¿Qué debe cambiar junto en una transacción?
2. ¿Qué invariantes deben mantenerse siempre?
3. ¿Cuál es la mínima unidad de consistencia?

Ejemplos:
✓ Order + OrderItems (cambian juntos)
✓ ShoppingCart + CartItems
✓ Reservation + RoomAssignment

✗ Order + Customer (diferentes agregados)
✗ Order + Product (diferentes agregados)
```

---

### 5.4 Domain Services (Servicios de Dominio)

**Definición:**
> **Lógica de negocio que no pertenece naturalmente a una entidad o value object.**

**Cuándo usar:**

```
✓ Operación que involucra múltiples agregados
✓ Lógica que no es responsabilidad natural de una entidad
✓ Cálculos complejos del dominio
✓ Reglas de negocio sin estado
```

**Ejemplo:**

```java
// ✅ Domain Service: Transferencia entre cuentas
public class MoneyTransferService {

  // Operación que involucra dos agregados (cuentas)
  public TransferResult transfer(
      BankAccount fromAccount,
      BankAccount toAccount,
      Money amount,
      TransferReason reason) {

    // Validaciones de dominio
    if (!fromAccount.canWithdraw(amount)) {
      return TransferResult.failure("Insufficient funds");
    }

    if (fromAccount.equals(toAccount)) {
      return TransferResult.failure("Cannot transfer to same account");
    }

    // Regla de negocio: límite de transferencia
    if (amount.isGreaterThan(DAILY_TRANSFER_LIMIT)) {
      return TransferResult.failure("Exceeds daily transfer limit");
    }

    // Ejecutar transferencia (afecta dos agregados)
    fromAccount.withdraw(amount, "Transfer to " + toAccount.getAccountNumber());
    toAccount.deposit(amount, "Transfer from " + fromAccount.getAccountNumber());

    // Crear evento de dominio
    TransferCompleted event = new TransferCompleted(
      fromAccount.getId(),
      toAccount.getId(),
      amount,
      reason
    );

    return TransferResult.success(event);
  }
}

// ✅ Domain Service: Cálculo de impuestos
public class TaxCalculationService {
  private final TaxRateRepository taxRateRepository;

  public TaxCalculationService(TaxRateRepository taxRateRepository) {
    this.taxRateRepository = taxRateRepository;
  }

  // Lógica compleja que no pertenece a Order
  public Money calculateTax(Order order, Address shippingAddress) {
    TaxRate rate = taxRateRepository.findByRegion(shippingAddress.getRegion());

    Money subtotal = order.getSubtotal();

    // Reglas de negocio específicas de impuestos
    if (order.includesTaxExemptItems()) {
      Money taxableAmount = order.getTaxableItemsTotal();
      return taxableAmount.multiply(rate.getPercentage());
    }

    return subtotal.multiply(rate.getPercentage());
  }
}

// ✅ Domain Service: Validación de disponibilidad
public class RoomAvailabilityService {
  private final ReservationRepository reservationRepository;

  public boolean isRoomAvailable(RoomId roomId, DateRange period) {
    List<Reservation> existingReservations =
      reservationRepository.findByRoomAndPeriod(roomId, period);

    // Lógica de negocio: verificar solapamiento
    for (Reservation reservation : existingReservations) {
      if (reservation.overlapsWith(period)) {
        return false;
      }
    }

    return true;
  }
}
```

**Domain Service vs Application Service:**

```
DOMAIN SERVICE:
✓ Lógica de negocio pura
✓ En la capa de dominio
✓ Sin dependencias de infraestructura
✓ Stateless (sin estado)
✓ Ejemplo: TaxCalculationService

APPLICATION SERVICE:
✓ Orquestación de casos de uso
✓ En la capa de aplicación
✓ Coordina dominio + infraestructura
✓ Puede tener dependencias de repositorios, etc.
✓ Ejemplo: PlaceOrderUseCase

Ejemplo:
```

```java
// Domain Service (capa de dominio)
public class PricingService {
  public Money calculatePrice(Product product, Customer customer) {
    Money basePrice = product.getPrice();

    // Lógica de negocio: descuentos por tipo de cliente
    if (customer.isVIP()) {
      return basePrice.multiply(new BigDecimal("0.9"));  // 10% descuento
    }

    return basePrice;
  }
}

// Application Service (capa de aplicación)
public class PlaceOrderService {
  private final OrderRepository orderRepository;
  private final PricingService pricingService;  // ← Usa domain service
  private final EmailService emailService;

  public OrderId placeOrder(PlaceOrderCommand command) {
    // 1. Obtener datos
    Customer customer = customerRepository.findById(command.getCustomerId());
    Product product = productRepository.findById(command.getProductId());

    // 2. Usar domain service
    Money finalPrice = pricingService.calculatePrice(product, customer);

    // 3. Crear orden (entidad de dominio)
    Order order = new Order(customer.getId());
    order.addItem(product, command.getQuantity(), finalPrice);
    order.confirm();

    // 4. Persistir
    orderRepository.save(order);

    // 5. Notificar (infraestructura)
    emailService.sendOrderConfirmation(customer.getEmail(), order);

    return order.getId();
  }
}
```

---

### 5.5 Repositories (Repositorios)

**Definición:**
> **Abstracción que encapsula la lógica de acceso a datos para los agregados, dando la ilusión de una colección en memoria.**

**Características:**

```
✓ Interface en la capa de dominio
✓ Implementación en la capa de infraestructura
✓ Uno por aggregate root
✓ Métodos orientados al dominio (no SQL)
✓ Abstrae persistencia
```

**Ejemplo:**

```java
// ✅ Repository (interface en dominio)
public interface OrderRepository {
  // Métodos expresados en lenguaje del dominio

  Order findById(OrderId id);

  List<Order> findByCustomer(CustomerId customerId);

  List<Order> findPendingOrders();

  List<Order> findByDateRange(LocalDate start, LocalDate end);

  void save(Order order);

  void delete(Order order);
}

// Implementación (en infraestructura)
@Repository
public class JpaOrderRepository implements OrderRepository {

  @PersistenceContext
  private EntityManager entityManager;

  @Override
  public Order findById(OrderId id) {
    OrderEntity entity = entityManager.find(OrderEntity.class, id.getValue());

    if (entity == null) {
      throw new OrderNotFoundException(id);
    }

    return orderMapper.toDomain(entity);  // Entity → Domain
  }

  @Override
  public List<Order> findPendingOrders() {
    String jpql = "SELECT o FROM OrderEntity o WHERE o.status = :status";

    List<OrderEntity> entities = entityManager.createQuery(jpql, OrderEntity.class)
        .setParameter("status", "PENDING")
        .getResultList();

    return entities.stream()
        .map(orderMapper::toDomain)
        .collect(Collectors.toList());
  }

  @Override
  public void save(Order order) {
    OrderEntity entity = orderMapper.toEntity(order);  // Domain → Entity
    entityManager.merge(entity);
  }
}
```

**Reglas de Repositories:**

```
1. UNO POR AGGREGATE ROOT:
   ✓ OrderRepository (para Order aggregate)
   ✗ OrderItemRepository (OrderItem es parte de Order)

2. MÉTODOS EN LENGUAJE DE DOMINIO:
   ✓ findPendingOrders()
   ✓ findByCustomer(customerId)
   ✗ findByStatusEquals1()

3. NO EXPONER DETALLES DE PERSISTENCIA:
   ✓ Repository expone colección conceptual
   ✗ No exponer queries SQL directamente

4. IMPLEMENTACIÓN EN INFRAESTRUCTURA:
   - Interface: capa de dominio
   - Implementación: capa de infraestructura
```

---

### 5.6 Domain Events (Eventos de Dominio)

**Definición:**
> **Algo que sucedió en el dominio que es de interés para otros componentes del sistema.**

**Características:**

```
✓ Pasado (algo que YA ocurrió)
✓ Inmutable
✓ Nombre en pasado (OrderPlaced, PaymentReceived)
✓ Contiene datos relevantes del evento
```

**Ejemplo:**

```java
// ✅ Domain Event
public class OrderPlaced {
  private final OrderId orderId;
  private final CustomerId customerId;
  private final Money totalAmount;
  private final LocalDateTime occurredAt;

  public OrderPlaced(OrderId orderId, CustomerId customerId, Money totalAmount) {
    this.orderId = orderId;
    this.customerId = customerId;
    this.totalAmount = totalAmount;
    this.occurredAt = LocalDateTime.now();
  }

  // Getters (inmutable)
  public OrderId getOrderId() { return orderId; }
  public CustomerId getCustomerId() { return customerId; }
  public Money getTotalAmount() { return totalAmount; }
  public LocalDateTime getOccurredAt() { return occurredAt; }
}

// Entidad que produce eventos
public class Order {
  private final OrderId id;
  private OrderStatus status;
  private final List<DomainEvent> domainEvents = new ArrayList<>();

  public void confirm() {
    if (this.status != OrderStatus.PENDING) {
      throw new InvalidStateTransitionException();
    }

    this.status = OrderStatus.CONFIRMED;

    // Registrar evento
    this.domainEvents.add(new OrderConfirmed(this.id, LocalDateTime.now()));
  }

  public List<DomainEvent> getDomainEvents() {
    return Collections.unmodifiableList(domainEvents);
  }

  public void clearDomainEvents() {
    domainEvents.clear();
  }
}

// Handlers de eventos
@Component
public class OrderPlacedHandler {
  private final EmailService emailService;
  private final InventoryService inventoryService;

  @EventHandler
  public void handle(OrderPlaced event) {
    // Enviar confirmación al cliente
    emailService.sendOrderConfirmation(event.getCustomerId(), event.getOrderId());

    // Reservar inventario
    inventoryService.reserveItems(event.getOrderId());
  }
}
```

**Beneficios:**

```
✓ Desacoplamiento entre componentes
✓ Auditoría (histórico de eventos)
✓ Event Sourcing posible
✓ Reacciones asíncronas
✓ Integración entre bounded contexts
```

---

## 6. Patrones Estratégicos

### 6.1 Bounded Context (Contexto Delimitado)

**Definición:**
> **Un límite explícito dentro del cual un modelo de dominio particular es válido.**

**El Problema:**

```
En un e-commerce grande:

"Product" significa diferentes cosas en diferentes áreas:

SALES (Ventas):
- Product tiene precio, descripción, imágenes
- "Product" = lo que se vende

INVENTORY (Inventario):
- Product tiene SKU, ubicación en almacén, stock
- "Product" = lo que se almacena

SHIPPING (Envíos):
- Product tiene peso, dimensiones, categoría de envío
- "Product" = lo que se envía

CATALOG (Catálogo):
- Product tiene categorías, tags, reviews
- "Product" = lo que se muestra

¿Cómo modelar "Product" en el sistema?
```

**La Solución: Bounded Contexts**

```
┌─────────────────────────────────────────────┐
│         BOUNDED CONTEXTS                    │
├─────────────────────────────────────────────┤
│                                             │
│  ┌──────────────┐      ┌─────────────────┐ │
│  │ SALES        │      │ INVENTORY       │ │
│  │ CONTEXT      │      │ CONTEXT         │ │
│  │              │      │                 │ │
│  │ Product:     │      │ Product:        │ │
│  │ - price      │      │ - sku           │ │
│  │ - name       │      │ - stock         │ │
│  │ - currency   │      │ - location      │ │
│  └──────────────┘      └─────────────────┘ │
│                                             │
│  ┌──────────────┐      ┌─────────────────┐ │
│  │ SHIPPING     │      │ CATALOG         │ │
│  │ CONTEXT      │      │ CONTEXT         │ │
│  │              │      │                 │ │
│  │ Product:     │      │ Product:        │ │
│  │ - weight     │      │ - description   │ │
│  │ - dimensions │      │ - images        │ │
│  └──────────────┘      └─────────────────┘ │
│                                             │
└─────────────────────────────────────────────┘

Cada contexto tiene su PROPIO modelo de Product
Los modelos son diferentes pero VÁLIDOS en su contexto
```

**Ejemplo en Código:**

```java
// SALES Context
package com.example.sales.domain;

public class Product {
  private ProductId id;
  private ProductName name;
  private Money price;
  private String description;

  // Lógica relevante para ventas
  public Money calculateDiscountedPrice(DiscountPolicy policy) { ... }
}

// INVENTORY Context
package com.example.inventory.domain;

public class Product {
  private SKU sku;
  private int stockLevel;
  private WarehouseLocation location;

  // Lógica relevante para inventario
  public void decreaseStock(int quantity) { ... }
  public boolean isInStock() { ... }
}

// SHIPPING Context
package com.example.shipping.domain;

public class ShippableItem {  // Diferente nombre!
  private SKU sku;
  private Weight weight;
  private Dimensions dimensions;
  private ShippingCategory category;

  // Lógica relevante para envíos
  public ShippingCost calculateShippingCost(Address destination) { ... }
}
```

**Beneficios:**

```
✓ Modelos más simples y enfocados
✓ Equipos pueden trabajar independientemente
✓ Términos claros en cada contexto
✓ Evolución independiente
✓ Reducción de acoplamiento
```

---

### 6.2 Context Mapping (Mapeo de Contextos)

**Definición:**
> **Definir las relaciones y traducciones entre diferentes bounded contexts.**

**Patrones de Relación:**

#### 1. **Shared Kernel (Núcleo Compartido)**

```
Dos contextos comparten parte del modelo

┌──────────────┐         ┌──────────────┐
│  Context A   │         │  Context B   │
│              │         │              │
│  ┌────────┐  │         │  ┌────────┐  │
│  │ Shared │◄─┼─────────┼─►│ Shared │  │
│  │ Kernel │  │         │  │ Kernel │  │
│  └────────┘  │         │  └────────┘  │
└──────────────┘         └──────────────┘

Ejemplo: Money, Currency, common Value Objects
```

#### 2. **Customer-Supplier (Cliente-Proveedor)**

```
Un contexto (Supplier) provee servicios a otro (Customer)

┌──────────────┐         ┌──────────────┐
│  INVENTORY   │─────────►│    SALES     │
│  (Supplier)  │         │  (Customer)  │
│              │         │              │
│ Provides API │         │ Consumes API │
└──────────────┘         └──────────────┘

Sales depende de Inventory para verificar stock
```

#### 3. **Conformist (Conformista)**

```
Un contexto se adapta completamente al modelo de otro

┌──────────────┐         ┌──────────────┐
│  LEGACY      │─────────►│   NEW APP    │
│  SYSTEM      │         │ (Conformist) │
│              │         │              │
│ Fixed API    │         │ Adapts to it │
└──────────────┘         └──────────────┘
```

#### 4. **Anti-Corruption Layer (ACL)**

```
Capa que traduce entre contextos incompatibles

┌──────────────┐    ┌────┐    ┌──────────────┐
│  External    │───►│ACL │───►│  Our Domain  │
│  System      │    └────┘    │              │
│              │              │              │
│ (Their model)│              │ (Our model)  │
└──────────────┘              └──────────────┘

ACL traduce y protege nuestro modelo
```

**Ejemplo de ACL:**

```java
// External System (modelo que no controlamos)
public class ExternalPaymentResponse {
  public int status;  // 1=success, 2=failed, 3=pending
  public double amt;
  public String curr;
}

// Anti-Corruption Layer
public class PaymentGatewayAdapter {

  // Traduce de modelo externo a nuestro dominio
  public PaymentResult toPaymentResult(ExternalPaymentResponse response) {
    PaymentStatus status = translateStatus(response.status);
    Money amount = new Money(
      new BigDecimal(response.amt),
      Currency.valueOf(response.curr)
    );

    return new PaymentResult(status, amount);
  }

  private PaymentStatus translateStatus(int externalStatus) {
    switch (externalStatus) {
      case 1: return PaymentStatus.APPROVED;
      case 2: return PaymentStatus.DECLINED;
      case 3: return PaymentStatus.PENDING;
      default: throw new UnknownPaymentStatusException();
    }
  }
}

// Nuestro dominio (protegido del modelo externo)
public class PaymentResult {
  private final PaymentStatus status;
  private final Money amount;

  // Modelo limpio en nuestro lenguaje ubicuo
}
```

---

### 6.3 Subdomains (Subdominios)

**Definición:**
> **Áreas del negocio que dividen el dominio completo.**

**Tipos:**

#### 1. **Core Domain (Dominio Central)**

```
El corazón del negocio, lo que da ventaja competitiva

Características:
✓ Diferenciador estratégico
✓ Complejo
✓ Cambia frecuentemente
✓ Requiere máxima atención

Ejemplo en Netflix:
CORE: Recommendation Engine (algoritmo de recomendaciones)
→ Su ventaja competitiva
→ Máxima inversión en diseño y desarrollo

Ejemplo en Amazon:
CORE: Fulfillment & Logistics
→ Lo que los diferencia
→ Complejidad del dominio
```

#### 2. **Supporting Subdomain (Subdominio de Soporte)**

```
Necesario pero no diferenciador

Características:
✓ Soporta el core domain
✓ Específico del negocio
✓ No es ventaja competitiva
✓ Puede desarrollarse internamente

Ejemplo en Netflix:
SUPPORTING: User Profile Management
→ Necesario pero no su diferenciador
→ Desarrollo interno simple

Ejemplo en Amazon:
SUPPORTING: Product Reviews System
→ Importante pero no su ventaja clave
```

#### 3. **Generic Subdomain (Subdominio Genérico)**

```
Necesario pero genérico (no específico del negocio)

Características:
✓ Solucionable con producto estándar
✓ No específico del negocio
✓ Candidato para comprar/usar librería

Ejemplo en Netflix:
GENERIC: Authentication & Authorization
→ Usar Auth0, Okta, o similar
→ No desarrollar desde cero

Ejemplo en Amazon:
GENERIC: Email Service
→ Usar SendGrid, AWS SES
→ No construir servicio de email propio
```

**Estrategia de Inversión:**

```
Core Domain:
✓ 80% del esfuerzo de diseño
✓ DDD completo
✓ Mejor talento
✓ Tests exhaustivos
✓ Evolución continua

Supporting Subdomain:
✓ 15% del esfuerzo
✓ DDD simplificado
✓ Puede ser CRUD
✓ Tests básicos

Generic Subdomain:
✓ 5% del esfuerzo
✓ Comprar/usar librería
✓ Integración solamente
```

---

## 7. Ejemplo Práctico Completo

### Escenario: Sistema de Biblioteca

**Requisito:** Diseñar el módulo de préstamos de libros con DDD.

### Paso 1: Identificar el Ubiquitous Language

```
Conversación con bibliotecario (experto del dominio):

"Cuando un MIEMBRO quiere tomar prestado un LIBRO, verificamos si tiene
PRÉSTAMOS VENCIDOS. Si todo está bien, registramos un PRÉSTAMO con
FECHA DE VENCIMIENTO. El miembro debe DEVOLVER el libro antes de esa fecha,
si no, se le aplica una MULTA."

Términos identificados:
- Miembro (Member)
- Libro (Book)
- Préstamo (Loan)
- Préstamos Vencidos (Overdue Loans)
- Fecha de Vencimiento (Due Date)
- Devolver (Return)
- Multa (Fine)
```

### Paso 2: Identificar Entities, Value Objects y Aggregates

```
ENTITIES (tienen identidad):
- Member (miembro con ID único)
- Book (libro con ISBN)
- Loan (préstamo con ID único)

VALUE OBJECTS (sin identidad, definidos por valor):
- MemberName
- ISBN
- LoanPeriod (fecha inicio + vencimiento)
- Fine (monto de multa)

AGGREGATES:
- Member (raíz) + Loans
- Book (raíz independiente)
```

### Paso 3: Modelar el Dominio

```java
// ===== VALUE OBJECTS =====

public class ISBN {
  private final String value;

  public ISBN(String value) {
    if (!isValidISBN(value)) {
      throw new InvalidISBNException("Invalid ISBN format");
    }
    this.value = value;
  }

  private boolean isValidISBN(String isbn) {
    return isbn.matches("^\\d{13}$");  // ISBN-13
  }

  public String getValue() { return value; }

  @Override
  public boolean equals(Object obj) {
    if (!(obj instanceof ISBN)) return false;
    return this.value.equals(((ISBN) obj).value);
  }
}

public class LoanPeriod {
  private final LocalDate startDate;
  private final LocalDate dueDate;
  private static final int DEFAULT_LOAN_DAYS = 14;

  public LoanPeriod(LocalDate startDate) {
    this.startDate = startDate;
    this.dueDate = startDate.plusDays(DEFAULT_LOAN_DAYS);
  }

  public boolean isOverdue(LocalDate currentDate) {
    return currentDate.isAfter(dueDate);
  }

  public int daysOverdue(LocalDate currentDate) {
    if (!isOverdue(currentDate)) return 0;
    return (int) ChronoUnit.DAYS.between(dueDate, currentDate);
  }

  public LocalDate getStartDate() { return startDate; }
  public LocalDate getDueDate() { return dueDate; }
}

public class Fine {
  private final Money amount;
  private static final Money DAILY_FINE_RATE = new Money(new BigDecimal("0.50"), Currency.USD);

  public static Fine calculateFor(int daysOverdue) {
    if (daysOverdue <= 0) {
      return new Fine(Money.ZERO);
    }

    BigDecimal totalFine = DAILY_FINE_RATE.getAmount()
        .multiply(BigDecimal.valueOf(daysOverdue));

    return new Fine(new Money(totalFine, Currency.USD));
  }

  private Fine(Money amount) {
    this.amount = amount;
  }

  public Money getAmount() { return amount; }
}

// ===== ENTITIES =====

public class Book {  // ENTITY
  private final ISBN isbn;
  private final String title;
  private final String author;
  private BookStatus status;

  public Book(ISBN isbn, String title, String author) {
    this.isbn = isbn;
    this.title = title;
    this.author = author;
    this.status = BookStatus.AVAILABLE;
  }

  // Lógica de dominio
  public void markAsLoaned() {
    if (this.status != BookStatus.AVAILABLE) {
      throw new BookNotAvailableException("Book is not available for loan");
    }
    this.status = BookStatus.LOANED;
  }

  public void markAsAvailable() {
    this.status = BookStatus.AVAILABLE;
  }

  public ISBN getIsbn() { return isbn; }
  public String getTitle() { return title; }
  public BookStatus getStatus() { return status; }

  @Override
  public boolean equals(Object obj) {
    if (!(obj instanceof Book)) return false;
    return this.isbn.equals(((Book) obj).isbn);
  }
}

public enum BookStatus {
  AVAILABLE, LOANED, RESERVED, LOST
}

public class Loan {  // ENTITY (parte del aggregate Member)
  private final LoanId id;
  private final ISBN bookIsbn;
  private final LoanPeriod period;
  private LoanStatus status;

  Loan(ISBN bookIsbn, LoanPeriod period) {  // Package-private
    this.id = LoanId.generate();
    this.bookIsbn = bookIsbn;
    this.period = period;
    this.status = LoanStatus.ACTIVE;
  }

  boolean isOverdue(LocalDate currentDate) {
    return status == LoanStatus.ACTIVE && period.isOverdue(currentDate);
  }

  void returnBook(LocalDate returnDate) {
    if (this.status != LoanStatus.ACTIVE) {
      throw new LoanAlreadyReturnedException();
    }
    this.status = LoanStatus.RETURNED;
  }

  Fine calculateFine(LocalDate currentDate) {
    if (!isOverdue(currentDate)) {
      return Fine.calculateFor(0);
    }

    int daysOverdue = period.daysOverdue(currentDate);
    return Fine.calculateFor(daysOverdue);
  }

  ISBN getBookIsbn() { return bookIsbn; }
  LoanStatus getStatus() { return status; }
}

enum LoanStatus {
  ACTIVE, RETURNED, LOST
}

// ===== AGGREGATE ROOT =====

public class Member {  // AGGREGATE ROOT
  private final MemberId id;
  private final MemberName name;
  private final List<Loan> loans;
  private MembershipStatus status;

  public Member(MemberId id, MemberName name) {
    this.id = id;
    this.name = name;
    this.loans = new ArrayList<>();
    this.status = MembershipStatus.ACTIVE;
  }

  // Lógica de dominio: prestar libro
  public Loan borrowBook(Book book) {
    // Invariante: miembro activo
    if (this.status != MembershipStatus.ACTIVE) {
      throw new MemberNotActiveException("Member is not active");
    }

    // Invariante: no tener préstamos vencidos
    if (hasOverdueLoans()) {
      throw new HasOverdueLoansException("Member has overdue loans");
    }

    // Invariante: límite de préstamos simultáneos
    int activeLoansCount = countActiveLoans();
    if (activeLoansCount >= 5) {
      throw new LoanLimitExceededException("Member has reached loan limit");
    }

    // Crear préstamo
    LoanPeriod period = new LoanPeriod(LocalDate.now());
    Loan loan = new Loan(book.getIsbn(), period);
    this.loans.add(loan);

    // Cambiar estado del libro
    book.markAsLoaned();

    return loan;
  }

  // Lógica de dominio: devolver libro
  public ReturnResult returnBook(ISBN bookIsbn, Book book) {
    Loan loan = findActiveLoan(bookIsbn)
        .orElseThrow(() -> new LoanNotFoundException("No active loan for this book"));

    LocalDate returnDate = LocalDate.now();

    // Calcular multa si hay
    Fine fine = loan.calculateFine(returnDate);

    // Marcar préstamo como devuelto
    loan.returnBook(returnDate);

    // Cambiar estado del libro
    book.markAsAvailable();

    return new ReturnResult(fine);
  }

  // Consultas de invariantes
  public boolean hasOverdueLoans() {
    LocalDate today = LocalDate.now();
    return loans.stream()
        .anyMatch(loan -> loan.isOverdue(today));
  }

  private int countActiveLoans() {
    return (int) loans.stream()
        .filter(loan -> loan.getStatus() == LoanStatus.ACTIVE)
        .count();
  }

  private Optional<Loan> findActiveLoan(ISBN bookIsbn) {
    return loans.stream()
        .filter(loan -> loan.getStatus() == LoanStatus.ACTIVE)
        .filter(loan -> loan.getBookIsbn().equals(bookIsbn))
        .findFirst();
  }

  public MemberId getId() { return id; }
  public MemberName getName() { return name; }
  public List<Loan> getLoans() { return Collections.unmodifiableList(loans); }
}

enum MembershipStatus {
  ACTIVE, SUSPENDED, EXPIRED
}

public class ReturnResult {
  private final Fine fine;

  public ReturnResult(Fine fine) {
    this.fine = fine;
  }

  public boolean hasFine() {
    return fine.getAmount().isGreaterThan(Money.ZERO);
  }

  public Fine getFine() { return fine; }
}

// ===== REPOSITORIES =====

public interface MemberRepository {
  Member findById(MemberId id);
  void save(Member member);
  List<Member> findMembersWithOverdueLoans();
}

public interface BookRepository {
  Book findByIsbn(ISBN isbn);
  List<Book> findAvailableBooks();
  void save(Book book);
}

// ===== USE CASE =====

public class BorrowBookService {
  private final MemberRepository memberRepository;
  private final BookRepository bookRepository;

  public BorrowBookService(MemberRepository memberRepository,
                           BookRepository bookRepository) {
    this.memberRepository = memberRepository;
    this.bookRepository = bookRepository;
  }

  public LoanResult borrowBook(MemberId memberId, ISBN isbn) {
    // 1. Obtener agregados
    Member member = memberRepository.findById(memberId);
    Book book = bookRepository.findByIsbn(isbn);

    // 2. Ejecutar lógica de dominio
    Loan loan = member.borrowBook(book);  // ← Lógica en el dominio

    // 3. Persistir cambios
    memberRepository.save(member);
    bookRepository.save(book);

    // 4. Retornar resultado
    return new LoanResult(loan.getId(), loan.getPeriod().getDueDate());
  }
}
```

### Paso 4: Domain Events

```java
// Eventos de dominio

public class BookBorrowed {
  private final MemberId memberId;
  private final ISBN bookIsbn;
  private final LocalDate dueDate;
  private final LocalDateTime occurredAt;

  public BookBorrowed(MemberId memberId, ISBN bookIsbn, LocalDate dueDate) {
    this.memberId = memberId;
    this.bookIsbn = bookIsbn;
    this.dueDate = dueDate;
    this.occurredAt = LocalDateTime.now();
  }

  // Getters...
}

public class BookReturned {
  private final MemberId memberId;
  private final ISBN bookIsbn;
  private final Fine fine;
  private final LocalDateTime occurredAt;

  // Constructor y getters...
}

// Handlers
@Component
public class BookBorrowedHandler {
  private final EmailService emailService;

  @EventHandler
  public void handle(BookBorrowed event) {
    // Enviar recordatorio de devolución
    emailService.sendLoanConfirmation(
      event.getMemberId(),
      event.getBookIsbn(),
      event.getDueDate()
    );
  }
}
```

---

## 8. DDD y Arquitecturas

### DDD + Clean Architecture

```
DDD y Clean Architecture se complementan perfectamente:

┌────────────────────────────────────────────┐
│         CLEAN ARCHITECTURE                 │
├────────────────────────────────────────────┤
│                                            │
│  ┌──────────────────────────────────────┐  │
│  │  ENTITIES (Clean Architecture)       │  │
│  │  ═════════════════════════            │  │
│  │  DDD: Entities + Value Objects       │  │
│  │       + Aggregates                   │  │
│  └──────────────────────────────────────┘  │
│                                            │
│  ┌──────────────────────────────────────┐  │
│  │  USE CASES (Clean Architecture)      │  │
│  │  ═════════════════════════            │  │
│  │  DDD: Application Services           │  │
│  │       + Domain Services              │  │
│  └──────────────────────────────────────┘  │
│                                            │
│  ┌──────────────────────────────────────┐  │
│  │  INTERFACE ADAPTERS                  │  │
│  │  ═════════════════════════            │  │
│  │  DDD: Repositories (interface)       │  │
│  │       + Anti-Corruption Layers       │  │
│  └──────────────────────────────────────┘  │
│                                            │
│  ┌──────────────────────────────────────┐  │
│  │  FRAMEWORKS & DRIVERS                │  │
│  │  ═════════════════════════            │  │
│  │  DDD: Repository implementations     │  │
│  │       + Infrastructure               │  │
│  └──────────────────────────────────────┘  │
│                                            │
└────────────────────────────────────────────┘

DDD define QUÉ (Entities, Value Objects, Aggregates)
Clean Architecture define DÓNDE (capas, dependencias)
```

### DDD + Hexagonal Architecture

```
┌────────────────────────────────────────────┐
│      HEXAGONAL ARCHITECTURE + DDD          │
├────────────────────────────────────────────┤
│                                            │
│  ┌──────────────────────────────────────┐  │
│  │         DOMAIN (Hexagon Core)        │  │
│  │  ═════════════════════════            │  │
│  │  DDD Building Blocks:                │  │
│  │  - Entities                          │  │
│  │  - Value Objects                     │  │
│  │  - Aggregates                        │  │
│  │  - Domain Services                   │  │
│  │  - Domain Events                     │  │
│  └──────────────────────────────────────┘  │
│                                            │
│  ┌──────────────────────────────────────┐  │
│  │     PORTS (Interfaces)               │  │
│  │  ═════════════════════════            │  │
│  │  DDD: Repository interfaces          │  │
│  │                                      │  │
│  └──────────────────────────────────────┘  │
│                                            │
│  ┌──────────────────────────────────────┐  │
│  │     ADAPTERS                         │  │
│  │  ═════════════════════════            │  │
│  │  DDD: Repository implementations     │  │
│  │       Anti-Corruption Layers         │  │
│  └──────────────────────────────────────┘  │
│                                            │
└────────────────────────────────────────────┘
```

---

## 9. DDD en el Proyecto QRest

Basándonos en el análisis del proyecto QRest (del CLAUDE.md), veamos cómo se aplica DDD:

### Bounded Contexts en QRest

```
┌─────────────────────────────────────────────┐
│            QREST SYSTEM                     │
├─────────────────────────────────────────────┤
│                                             │
│  ┌────────────┐  ┌───────────────┐         │
│  │ PRODUCTS   │  │ CATEGORIES    │         │
│  │ Context    │  │ Context       │         │
│  │            │  │               │         │
│  │ - Product  │  │ - Category    │         │
│  │ - Image    │  │               │         │
│  └────────────┘  └───────────────┘         │
│                                             │
│  ┌────────────┐  ┌───────────────┐         │
│  │ ORDERS     │  │ TABLES        │         │
│  │ Context    │  │ Context       │         │
│  │            │  │               │         │
│  │ - Order    │  │ - Table       │         │
│  │ - OrderItem│  │ - QR Code     │         │
│  └────────────┘  └───────────────┘         │
│                                             │
└─────────────────────────────────────────────┘
```

### Entities y Value Objects en QRest

```java
// ORDERS Context

// Entity
public class Order {
  private final OrderId id;  // Value Object
  private final TableId tableId;  // Value Object (referencia a otro aggregate)
  private OrderStatus status;  // Enum (Value Object)
  private final List<OrderItem> items;  // Entities internas

  // Lógica de dominio
  public void addItem(ProductId productId, int quantity, Money price) {
    if (this.status != OrderStatus.PENDING) {
      throw new CannotModifyProcessedOrderException();
    }

    OrderItem item = new OrderItem(productId, quantity, price);
    this.items.add(item);
  }

  public void confirm() {
    if (items.isEmpty()) {
      throw new EmptyOrderException("Cannot confirm empty order");
    }
    this.status = OrderStatus.CONFIRMED;
  }
}

// Entity interna (parte del aggregate Order)
class OrderItem {
  private final ProductId productId;
  private int quantity;
  private final Money unitPrice;

  OrderItem(ProductId productId, int quantity, Money unitPrice) {
    this.productId = productId;
    this.quantity = quantity;
    this.unitPrice = unitPrice;
  }

  Money subtotal() {
    return unitPrice.multiply(BigDecimal.valueOf(quantity));
  }
}

// PRODUCTS Context

// Entity
public class Product {
  private final ProductId id;
  private ProductName name;
  private Money price;
  private CategoryId categoryId;  // Referencia a otro aggregate
  private ProductImage image;  // Value Object

  public void updatePrice(Money newPrice) {
    if (newPrice.isNegativeOrZero()) {
      throw new InvalidPriceException("Price must be positive");
    }
    this.price = newPrice;
  }
}

// Value Object
public class ProductImage {
  private final String fileName;
  private final String contentType;
  private final long sizeInBytes;
  private static final long MAX_SIZE = 2_097_152;  // 2 MB

  public ProductImage(String fileName, String contentType, long sizeInBytes) {
    if (sizeInBytes > MAX_SIZE) {
      throw new ImageTooLargeException("Image size exceeds 2MB limit");
    }

    if (!isValidContentType(contentType)) {
      throw new InvalidImageTypeException("Only JPEG and PNG allowed");
    }

    this.fileName = fileName;
    this.contentType = contentType;
    this.sizeInBytes = sizeInBytes;
  }

  private boolean isValidContentType(String contentType) {
    return contentType.equals("image/jpeg") || contentType.equals("image/png");
  }
}

// TABLES Context

// Entity
public class Table {
  private final TableId id;
  private TableNumber number;
  private TableStatus status;
  private QRCode qrCode;  // Value Object

  public void markAsOccupied() {
    if (this.status == TableStatus.OCCUPIED) {
      throw new TableAlreadyOccupiedException();
    }
    this.status = TableStatus.OCCUPIED;
  }

  public void markAsAvailable() {
    this.status = TableStatus.AVAILABLE;
  }
}

// Value Object
public class QRCode {
  private final String code;
  private final byte[] imageData;

  public static QRCode generate(TableNumber tableNumber) {
    String code = "QREST-TABLE-" + tableNumber.getValue();
    byte[] imageData = QRCodeGenerator.generateQRImage(code);
    return new QRCode(code, imageData);
  }

  private QRCode(String code, byte[] imageData) {
    this.code = code;
    this.imageData = imageData;
  }
}
```

### Repositories en QRest

```java
// Domain layer (application/ports/out/)
public interface OrderRepository {
  Order findById(OrderId id);
  List<Order> findByTable(TableId tableId);
  List<Order> findByStatus(OrderStatus status);
  void save(Order order);
}

// Infrastructure layer (infrastructure/persistence/)
@Repository
public class JpaOrderRepository implements OrderRepository {

  @PersistenceContext
  private EntityManager entityManager;

  @Override
  public Order findById(OrderId id) {
    OrderEntity entity = entityManager.find(OrderEntity.class, id.getValue());
    return orderMapper.toDomain(entity);
  }

  @Override
  public void save(Order order) {
    OrderEntity entity = orderMapper.toEntity(order);
    entityManager.merge(entity);
  }
}
```

### Domain Events en QRest

```java
// Eventos de dominio
public class OrderPlaced {
  private final OrderId orderId;
  private final TableId tableId;
  private final LocalDateTime occurredAt;

  // Constructor y getters...
}

public class OrderStatusChanged {
  private final OrderId orderId;
  private final OrderStatus oldStatus;
  private final OrderStatus newStatus;
  private final LocalDateTime occurredAt;

  // Constructor y getters...
}

// Handlers (con WebSocket para tiempo real)
@Component
public class OrderStatusChangedHandler {
  private final WebSocketNotificationService webSocketService;

  @EventHandler
  public void handle(OrderStatusChanged event) {
    // Notificar en tiempo real via WebSocket
    webSocketService.notifyOrderStatusChange(
      event.getOrderId(),
      event.getNewStatus()
    );
  }
}
```

---

## 10. Ventajas y Desventajas

### ✅ Ventajas

#### 1. **Modelo Alineado con el Negocio**

```
✓ Expertos pueden leer y validar el código
✓ Reduce malentendidos
✓ Conocimiento del negocio preservado en el código
✓ Facilita comunicación entre técnicos y expertos
```

#### 2. **Lógica de Negocio Centralizada**

```
✓ Reglas de negocio en el dominio (no dispersas)
✓ Fácil encontrar y modificar lógica
✓ Entidades ricas (no anémicas)
✓ Comportamiento junto con datos
```

#### 3. **Mantenibilidad**

```
✓ Código organizado por conceptos de negocio
✓ Bounded contexts limitan complejidad
✓ Cambios localizados
✓ Evolución del modelo guiada por el negocio
```

#### 4. **Escalabilidad del Equipo**

```
✓ Equipos pueden trabajar en bounded contexts diferentes
✓ Menos conflictos de merge
✓ Onboarding más rápido (modelo autodocumentado)
✓ Lenguaje ubicuo facilita colaboración
```

#### 5. **Testabilidad**

```
✓ Lógica de dominio testeable sin BD
✓ Value objects inmutables (predecibles)
✓ Aggregates como unidades de test
✓ Mocks simples (interfaces de repositorios)
```

---

### ❌ Desventajas

#### 1. **Curva de Aprendizaje**

```
❌ Muchos conceptos nuevos (aggregates, value objects, etc.)
❌ Requiere entender patrones tácticos y estratégicos
❌ Cambio de mentalidad (de CRUD a modelo rico)
❌ Tiempo de formación del equipo
```

#### 2. **Complejidad Inicial**

```
❌ Más clases que CRUD tradicional
❌ Más archivos y estructura
❌ Overhead inicial alto para features simples
```

Ejemplo:
```
CRUD simple:
- 1 Entity, 1 Controller, 1 Service = 3 archivos

DDD:
- 1 Entity, 2-3 Value Objects, 1 Aggregate,
  1 Repository interface, 1 Repository impl,
  1 Domain Service, 1 Application Service
  = 8-10 archivos
```

#### 3. **Over-engineering en Proyectos Simples**

```
❌ CRUD básico no necesita DDD completo
❌ Todo el boilerplate puede ser overkill
❌ Trade-off entre pureza y pragmatismo
```

#### 4. **Requiere Acceso a Expertos del Dominio**

```
❌ Sin expertos del dominio, DDD pierde valor
❌ Expertos ocupados = bloqueo
❌ Difícil en proyectos genéricos (sin dominio claro)
```

#### 5. **Performance Overhead (Potencial)**

```
❌ Mapeo entre capas (Domain ↔ Infrastructure)
❌ Creación de muchos objetos (Value Objects)
❌ Aggregates pueden ser grandes

(En la práctica, el overhead suele ser mínimo)
```

---

## 11. Cuándo Usar (y Cuándo No)

### ✅ USA DDD Cuando:

```
✓ Dominio complejo con reglas de negocio intrincadas
✓ Proyecto de larga duración (> 1 año)
✓ Equipo tiene acceso a expertos del dominio
✓ Lógica de negocio es el diferenciador (no solo CRUD)
✓ Dominio cambia frecuentemente
✓ Proyecto grande con múltiples equipos
✓ Core domain claro y estratégico
```

**Ejemplos:**
```
✓ Sistema bancario (reglas complejas de préstamos, tasas, etc.)
✓ E-commerce con lógica compleja (pricing, promociones, inventario)
✓ Sistema de seguros (pólizas, claims, underwriting)
✓ Plataforma de trading (órdenes, estrategias, riesgo)
✓ Sistema de salud (pacientes, diagnósticos, tratamientos)
```

### ❌ NO Uses DDD Cuando:

```
✗ CRUD ultra-simple (3 tablas, sin lógica)
✗ Proyecto pequeño (< 6 meses)
✗ Sin acceso a expertos del dominio
✗ Dominio trivial o genérico
✗ Equipo sin experiencia (aprender primero lo básico)
✗ Deadline extremo (no hay tiempo para diseño)
✗ MVP descartable
```

**Ejemplos:**
```
✗ Blog personal
✗ To-Do list simple
✗ CRUD de contactos
✗ Dashboard de métricas básico
✗ Landing page con formulario
```

### 🤔 Evalúa Caso por Caso

**Checklist de Decisión:**

```markdown
Responde SÍ/NO:

1. [ ] ¿El dominio tiene lógica de negocio compleja (no solo CRUD)?
2. [ ] ¿Tienes acceso regular a expertos del dominio?
3. [ ] ¿El proyecto durará más de 1 año?
4. [ ] ¿El equipo tiene > 3 developers?
5. [ ] ¿La lógica de negocio es el diferenciador del producto?
6. [ ] ¿El dominio cambia con frecuencia?
7. [ ] ¿El equipo está dispuesto a aprender DDD?
8. [ ] ¿Puedes invertir tiempo inicial en diseño?

RESULTADO:
- 7-8 SÍ: Definitivamente usa DDD
- 5-6 SÍ: Muy recomendable
- 3-4 SÍ: Considera DDD lite (solo building blocks tácticos)
- 1-2 SÍ: Probablemente no lo necesites
- 0 SÍ: NO uses DDD
```

### DDD Lite vs DDD Completo

```
DDD LITE (solo táctico):
✓ Entities + Value Objects
✓ Aggregates básicos
✓ Repositories
✗ Sin bounded contexts
✗ Sin context mapping
✗ Sin eventos de dominio complejos

Cuándo: Proyecto mediano, dominio moderadamente complejo

DDD COMPLETO (táctico + estratégico):
✓ Todo lo de DDD Lite
✓ Bounded Contexts
✓ Context Mapping
✓ Ubiquitous Language riguroso
✓ Domain Events
✓ Event Sourcing (opcional)

Cuándo: Proyecto grande, dominio muy complejo
```

---

## 12. Errores Comunes

### Error #1: Modelo Anémico

```java
// ❌ MAL: Entidad anémica (solo getters/setters)
public class Order {
  private Long id;
  private String status;
  private double total;

  // Solo getters y setters, sin lógica
  public String getStatus() { return status; }
  public void setStatus(String status) { this.status = status; }
  public double getTotal() { return total; }
  public void setTotal(double total) { this.total = total; }
}

// Lógica en el servicio (fuera del dominio)
public class OrderService {
  public void confirmOrder(Long orderId) {
    Order order = repository.findById(orderId);

    // ❌ Lógica que debería estar en Order
    if (order.getStatus().equals("PENDING")) {
      order.setStatus("CONFIRMED");
      order.setTotal(calculateTotal(order));
    }

    repository.save(order);
  }
}

// ✅ BIEN: Entidad rica con lógica
public class Order {
  private final OrderId id;
  private OrderStatus status;  // Enum, no String
  private Money total;

  // Lógica de dominio EN la entidad
  public void confirm() {
    if (this.status != OrderStatus.PENDING) {
      throw new InvalidStateTransitionException(
        "Can only confirm pending orders"
      );
    }
    this.status = OrderStatus.CONFIRMED;
  }

  // Getters (sin setters públicos)
  public OrderStatus getStatus() { return status; }
  public Money getTotal() { return total; }
}
```

---

### Error #2: No Usar Value Objects

```java
// ❌ MAL: Usar tipos primitivos
public class Product {
  private String email;  // ¿Es válido? ¿Cómo se valida?
  private double price;  // ¿Moneda? ¿Puede ser negativo?
  private String currency;

  public void setPrice(double price) {
    this.price = price;  // ❌ No valida
  }
}

// ✅ BIEN: Value Objects con validación
public class Product {
  private Email email;
  private Money price;

  public void updatePrice(Money newPrice) {
    // Money ya está validado (no negativo, tiene moneda)
    this.price = newPrice;
  }
}

public class Email {
  private final String value;

  public Email(String value) {
    if (!isValid(value)) {
      throw new InvalidEmailException();
    }
    this.value = value.toLowerCase();
  }

  private boolean isValid(String email) {
    return email.matches("^[A-Za-z0-9+_.-]+@(.+)$");
  }
}

public class Money {
  private final BigDecimal amount;
  private final Currency currency;

  public Money(BigDecimal amount, Currency currency) {
    if (amount.compareTo(BigDecimal.ZERO) < 0) {
      throw new NegativeAmountException();
    }
    this.amount = amount;
    this.currency = currency;
  }
}
```

---

### Error #3: Aggregates Demasiado Grandes

```java
// ❌ MAL: Aggregate muy grande
public class Customer {  // AGGREGATE ROOT
  private CustomerId id;
  private String name;
  private List<Order> orders;  // ❌ Todos los pedidos del cliente
  private List<Address> addresses;
  private PaymentMethods paymentMethods;
  private List<Review> reviews;
  private ShoppingCart cart;
  // ...
}

// Problemas:
// - Performance (cargar TODO el customer)
// - Transacciones grandes
// - Cambios en orders afectan customer

// ✅ BIEN: Aggregates pequeños
public class Customer {  // AGGREGATE ROOT
  private CustomerId id;
  private CustomerName name;
  private Email email;
  // Solo datos propios
}

public class Order {  // AGGREGATE ROOT SEPARADO
  private OrderId id;
  private CustomerId customerId;  // ← Referencia por ID
  private List<OrderItem> items;
}

// Customer y Order son agregados separados
// Se pueden modificar independientemente
```

---

### Error #4: Ignorar Ubiquitous Language

```java
// ❌ MAL: Lenguaje técnico
public class DataProcessor {
  public void processRequest(RequestDTO dto) {
    EntityRecord record = repository.findByPK(dto.getId());

    if (record.getStatusFlag() == 1) {
      record.setStatusFlag(2);
    }

    repository.update(record);
  }
}

// Experto de negocio no entiende esto

// ✅ BIEN: Ubiquitous Language
public class OrderService {
  public void confirmOrder(OrderId orderId) {
    Order order = orderRepository.findById(orderId);

    order.confirm();  // Lenguaje del negocio

    orderRepository.save(order);
  }
}

// Todos entienden "confirm order"
```

---

### Error #5: Bounded Contexts Mal Definidos

```java
// ❌ MAL: Un solo "User" para todo
public class User {  // Usado en TODOS los contextos
  private Long id;

  // Sales
  private List<Order> orders;
  private ShoppingCart cart;

  // Shipping
  private Address shippingAddress;

  // Marketing
  private List<Campaign> campaigns;
  private EmailPreferences emailPrefs;

  // Support
  private List<Ticket> tickets;

  // ... todo mezclado
}

// ✅ BIEN: Modelos específicos por contexto

// SALES Context
package com.example.sales.domain;
public class Customer {
  private CustomerId id;
  private Email email;
  // Solo lo relevante para ventas
}

// SHIPPING Context
package com.example.shipping.domain;
public class Recipient {
  private RecipientId id;
  private Address address;
  // Solo lo relevante para envíos
}

// MARKETING Context
package com.example.marketing.domain;
public class Subscriber {
  private SubscriberId id;
  private Email email;
  private EmailPreferences preferences;
  // Solo lo relevante para marketing
}
```

---

### Error #6: Lógica de Dominio en Application Services

```java
// ❌ MAL: Lógica de negocio en application service
@Service
public class PlaceOrderService {

  public void placeOrder(PlaceOrderCommand command) {
    Order order = new Order(command.getCustomerId());

    // ❌ Lógica de negocio aquí (debería estar en Order)
    for (OrderItemDTO item : command.getItems()) {
      if (item.getQuantity() <= 0) {
        throw new InvalidQuantityException();
      }

      if (item.getPrice() < 0) {
        throw new InvalidPriceException();
      }

      order.addItem(item.getProductId(), item.getQuantity(), item.getPrice());
    }

    // Más lógica de negocio aquí
    double total = 0;
    for (OrderItem item : order.getItems()) {
      total += item.getQuantity() * item.getUnitPrice();
    }
    order.setTotal(total);

    orderRepository.save(order);
  }
}

// ✅ BIEN: Lógica de negocio en el dominio
@Service
public class PlaceOrderService {

  public void placeOrder(PlaceOrderCommand command) {
    Order order = new Order(command.getCustomerId());

    // Delegar al dominio
    for (OrderItemDTO item : command.getItems()) {
      Money price = new Money(item.getPrice(), Currency.USD);
      order.addItem(item.getProductId(), item.getQuantity(), price);
    }

    // Order internamente calcula el total
    order.confirm();

    orderRepository.save(order);
  }
}

// La lógica está en Order
public class Order {
  private Money total;

  public void addItem(ProductId productId, int quantity, Money unitPrice) {
    if (quantity <= 0) {
      throw new InvalidQuantityException();
    }

    OrderItem item = new OrderItem(productId, quantity, unitPrice);
    this.items.add(item);

    recalculateTotal();  // ← Lógica de negocio
  }

  private void recalculateTotal() {
    this.total = items.stream()
        .map(OrderItem::subtotal)
        .reduce(Money.ZERO, Money::add);
  }
}
```

---

## 13. Implementación Práctica

### Paso a Paso para Implementar DDD

#### Paso 1: Identificar el Dominio

```
1. Hablar con expertos del dominio
2. Identificar conceptos clave
3. Crear glosario de términos
4. Identificar subdominios (Core, Supporting, Generic)

Ejemplo:
Core: Gestión de pedidos y pagos
Supporting: Notificaciones
Generic: Autenticación
```

#### Paso 2: Definir Bounded Contexts

```
1. Identificar límites naturales del dominio
2. Un contexto por área de negocio cohesiva
3. Definir relaciones entre contextos

Ejemplo:
- Sales Context
- Inventory Context
- Shipping Context
```

#### Paso 3: Modelar Building Blocks Tácticos

```
Para cada bounded context:

1. Identificar Entities (objetos con identidad)
2. Identificar Value Objects (sin identidad)
3. Agrupar en Aggregates (límites transaccionales)
4. Definir Repositories (uno por aggregate root)
5. Extraer Domain Services (lógica multi-aggregate)
```

#### Paso 4: Estructura de Directorios

```
src/
├── domain/                      # DOMINIO (core)
│   ├── model/
│   │   ├── Order.java           # Entity
│   │   ├── OrderItem.java       # Entity interna
│   │   ├── OrderId.java         # Value Object
│   │   ├── Money.java           # Value Object
│   │   └── OrderStatus.java     # Enum
│   │
│   ├── service/
│   │   └── PricingService.java  # Domain Service
│   │
│   ├── event/
│   │   └── OrderPlaced.java     # Domain Event
│   │
│   └── exception/
│       └── InvalidOrderException.java
│
├── application/                 # APLICACIÓN
│   ├── ports/
│   │   ├── in/                  # Use cases
│   │   │   ├── PlaceOrderUseCase.java
│   │   │   ├── PlaceOrderCommand.java
│   │   │   └── OrderDTO.java
│   │   │
│   │   └── out/                 # Puertos de salida
│   │       ├── OrderRepository.java
│   │       └── PaymentGateway.java
│   │
│   └── service/
│       └── PlaceOrderService.java  # Application Service
│
└── infrastructure/              # INFRAESTRUCTURA
    ├── persistence/
    │   ├── entity/
    │   │   └── OrderEntity.java    # JPA Entity
    │   │
    │   ├── repository/
    │   │   └── JpaOrderRepository.java
    │   │
    │   └── mapper/
    │       └── OrderMapper.java    # Domain ↔ Entity
    │
    └── web/
        ├── controller/
        │   └── OrderController.java
        │
        └── dto/
            └── PlaceOrderRequest.java
```

#### Paso 5: Implementar Domain Model

```java
// 1. Entities y Value Objects
public class Order {
  private final OrderId id;
  private OrderStatus status;
  private final List<OrderItem> items;
  private Money total;

  // Lógica de negocio
  public void addItem(...) { }
  public void confirm() { }
}

// 2. Aggregates
// Order es aggregate root
// OrderItem es parte del aggregate

// 3. Repositories (interface en domain)
public interface OrderRepository {
  Order findById(OrderId id);
  void save(Order order);
}

// 4. Domain Services
public class PricingService {
  public Money calculatePrice(...) { }
}
```

#### Paso 6: Implementar Application Layer

```java
@Service
public class PlaceOrderService implements PlaceOrderUseCase {
  private final OrderRepository orderRepository;
  private final PricingService pricingService;

  @Override
  public OrderId execute(PlaceOrderCommand command) {
    // Orquestación
    Order order = new Order(command.getCustomerId());

    for (OrderItemCommand item : command.getItems()) {
      Money price = pricingService.calculatePrice(...);
      order.addItem(item.getProductId(), item.getQuantity(), price);
    }

    order.confirm();
    orderRepository.save(order);

    return order.getId();
  }
}
```

#### Paso 7: Implementar Infrastructure

```java
@Repository
public class JpaOrderRepository implements OrderRepository {

  @Override
  public Order findById(OrderId id) {
    OrderEntity entity = entityManager.find(...);
    return orderMapper.toDomain(entity);
  }

  @Override
  public void save(Order order) {
    OrderEntity entity = orderMapper.toEntity(order);
    entityManager.merge(entity);
  }
}
```

---

## 14. Preguntas Frecuentes

### ¿DDD es lo mismo que Hexagonal/Clean Architecture?

**No:**

```
DDD: Metodología de diseño del DOMINIO
- Cómo modelar entidades, value objects, aggregates
- Cómo organizar el negocio (bounded contexts)
- QUÉ poner en el dominio

Hexagonal/Clean: Patrones ARQUITECTÓNICOS
- Cómo estructurar el código en capas
- Separación de concerns
- DÓNDE poner el código

Relación:
DDD + Hexagonal/Clean = Perfecta combinación
- DDD define el contenido del dominio
- Hexagonal/Clean define la estructura del proyecto
```

### ¿Necesito usar todos los building blocks?

**No:**

```
Puedes usar:
✓ Solo Entities + Value Objects (DDD lite)
✓ Agregar Aggregates cuando necesites límites transaccionales
✓ Domain Events cuando necesites reacciones
✓ Bounded Contexts solo en sistemas grandes

No es todo o nada
Empieza simple, agrega complejidad según necesites
```

### ¿DDD funciona con bases de datos NoSQL?

**Sí:**

```
DDD es independiente de la tecnología de persistencia

Funciona con:
✓ SQL (PostgreSQL, MySQL)
✓ NoSQL (MongoDB, DynamoDB)
✓ Event Store (Event Sourcing)
✓ In-memory
✓ Cualquier almacenamiento

El dominio no conoce la BD (Dependency Inversion)
```

### ¿Value Objects siempre deben ser inmutables?

**Sí, siempre:**

```
Razones:
✓ Evitan efectos secundarios
✓ Thread-safe
✓ Predecibles
✓ Pueden compartirse sin riesgo

Ejemplo:
Money money1 = new Money(100, USD);
Money money2 = money1.add(new Money(50, USD));

// money1 NO cambia (inmutabilidad)
// money2 es un NUEVO objeto
```

### ¿Cuándo crear un Domain Service vs lógica en Entity?

**Regla:**

```
Lógica en ENTITY cuando:
✓ Es responsabilidad natural de esa entidad
✓ Usa principalmente datos de esa entidad
✓ Ejemplo: Order.confirm()

Lógica en DOMAIN SERVICE cuando:
✓ Involucra múltiples agregados
✓ No pertenece naturalmente a una entidad
✓ Es cálculo complejo del dominio
✓ Ejemplo: TransferMoneyService (entre dos cuentas)
```

### ¿DDD requiere Event Sourcing?

**No:**

```
DDD ≠ Event Sourcing

Event Sourcing es un patrón de persistencia opcional

Puedes hacer DDD con:
✓ CRUD tradicional (estado actual en BD)
✓ Event Sourcing (eventos en lugar de estado)

Event Sourcing es compatible con DDD pero NO obligatorio
```

### ¿Cómo mapear entre Domain y Database entities?

**Opciones:**

```
1. MANUAL (control total):
   OrderEntity toEntity(Order order) {
     OrderEntity entity = new OrderEntity();
     entity.setId(order.getId().getValue());
     // ...
     return entity;
   }

2. MAPSTRUCT (generación automática):
   @Mapper
   interface OrderMapper {
     OrderEntity toEntity(Order order);
     Order toDomain(OrderEntity entity);
   }

3. MODELMAPPER (reflexión):
   ModelMapper mapper = new ModelMapper();
   OrderEntity entity = mapper.map(order, OrderEntity.class);

Recomendación: Manual o MapStruct (type-safe)
```

### ¿DDD es solo para backend?

**Mayormente sí, pero:**

```
Backend:
✓ DDD completo
✓ Entities, Value Objects, Aggregates
✓ Domain logic

Frontend:
Puede aplicar conceptos:
✓ Value Objects (validación)
✓ Ubiquitous Language (mismos nombres)
✓ DTOs específicos

Pero NO:
✗ Aggregates complejos
✗ Domain Services pesados
✗ Persistencia del dominio

El frontend consume el dominio del backend
```

---

## Resumen Final

### La Esencia de DDD

**DDD NO es:**
- ❌ Un framework
- ❌ Solo patrones tácticos (entities, value objects)
- ❌ Obligatorio usar todo
- ❌ Solo para proyectos enormes

**DDD ES:**
- ✅ Una filosofía de diseño
- ✅ Poner el negocio primero
- ✅ Colaboración con expertos del dominio
- ✅ Lenguaje compartido
- ✅ Modelo que refleja el negocio

### Los Pilares de DDD

```
1. UBIQUITOUS LANGUAGE
   → Mismo lenguaje en código, conversaciones, docs

2. BOUNDED CONTEXTS
   → Límites explícitos de modelos

3. MODELO RICO
   → Lógica de negocio en el dominio

4. BUILDING BLOCKS
   → Entities, Value Objects, Aggregates, etc.

5. SEPARACIÓN DE CONCERNS
   → Dominio separado de infraestructura
```

### Checklist DDD

```
Al modelar una feature:

ESTRATÉGICO:
□ ¿Identifiqué el bounded context correcto?
□ ¿Estoy usando ubiquitous language?
□ ¿El modelo refleja el negocio?

TÁCTICO:
□ ¿Identifiqué entities vs value objects correctamente?
□ ¿Los aggregates tienen límites claros?
□ ¿La lógica de negocio está en el dominio (no en services)?
□ ¿Los value objects son inmutables?
□ ¿Las referencias entre aggregates son por ID?

IMPLEMENTACIÓN:
□ ¿El dominio no depende de infraestructura?
□ ¿Los repositorios son abstracciones en el dominio?
□ ¿Estoy mapeando entre domain model y persistence model?
```

### Cuando Empezar

```
Fase 1: Aprender conceptos (este manual)
Fase 2: Practicar con ejemplos pequeños
Fase 3: Aplicar DDD lite en proyecto real
Fase 4: Expandir a DDD completo según necesidad
Fase 5: Refinar continuamente el modelo

No intentes aplicar todo de una vez
DDD es un viaje, no un destino
```

---

## Recursos Adicionales

### Libros Esenciales

```
📚 "Domain-Driven Design" - Eric Evans (2003)
   → El libro original, la biblia de DDD
   → "The Blue Book"

📚 "Implementing Domain-Driven Design" - Vaughn Vernon (2013)
   → DDD práctico, paso a paso
   → "The Red Book"

📚 "Domain-Driven Design Distilled" - Vaughn Vernon (2016)
   → Versión condensada, perfecto para empezar
   → Lectura rápida (< 200 páginas)

📚 "Patterns, Principles, and Practices of DDD" - Scott Millett
   → DDD con C# y .NET
   → Ejemplos prácticos

📚 "Learning Domain-Driven Design" - Vlad Khononov (2021)
   → Libro moderno y actualizado
   → Explica DDD en contexto de microservicios
```

### Cursos y Videos

```
🎥 "Domain-Driven Design Fundamentals" - Pluralsight
   (Julie Lerman & Steve Smith)

🎥 Eric Evans - DDD talks en conferencias

🎥 "Strategic Domain Driven Design" - DDD Europe

🔗 DDD Community: https://www.dddsample.org/
```

### Comunidad

```
🌐 DDD Community: https://www.domain-driven-design.org/
🌐 DDD Europe: https://dddeurope.com/
🐦 Twitter: #DDD #DomainDrivenDesign
💬 Discord/Slack: DDD communities
```

---

## Conclusión

### El Propósito de DDD

> **"El software no es sobre tecnología. Es sobre resolver problemas de negocio."** - Eric Evans

```
DDD nos recuerda:
✓ El código debe reflejar el negocio
✓ La complejidad está en el dominio, no en la tecnología
✓ Colaborar con expertos es esencial
✓ Un buen modelo es más importante que un buen framework
```

### La Mentalidad DDD

```
ANTES (mindset técnico):
"¿Cómo implemento esto con Spring Boot?"
"¿Qué tablas necesito en MySQL?"
"¿Qué endpoints REST creo?"

DESPUÉS (mindset DDD):
"¿Qué problema de negocio estoy resolviendo?"
"¿Cómo lo describe el experto del dominio?"
"¿Qué conceptos del dominio debo modelar?"
"¿Qué reglas de negocio debo implementar?"

La tecnología es secundaria
El dominio es primario
```

### Palabras Finales

**DDD es una inversión:**
- Costo inicial: Tiempo de aprendizaje y diseño
- Retorno: Código mantenible, alineado con el negocio, evolucionable

**No es magia:**
- Requiere práctica
- Requiere disciplina
- Requiere colaboración

**Pero vale la pena:**
- En proyectos complejos
- De larga duración
- Donde el dominio importa

---

**El mejor código es el que:**
- ✅ Resuelve el problema del negocio
- ✅ Los expertos pueden leer
- ✅ Puede evolucionar con el negocio

**DDD te ayuda a lograrlo.**

---

**Happy Domain-Driven Design!** 🎯

---

**Última actualización:** Diciembre 2025
**Versión:** 1.0
**Autor:** Manual de Domain-Driven Design
**Licencia:** Uso libre para aprendizaje y aplicación profesional
