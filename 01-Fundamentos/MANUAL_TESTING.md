# Manual Completo de Testing de Software
## Guía Profesional: De Principiante a Experto

> Manual profesional sobre testing, pruebas automatizadas y estrategias de calidad para proyectos de software modernos

---

## 📚 Índice

1. [Introducción](#1-introducción)
2. [El Problema que Resuelve](#2-el-problema-que-resuelve)
3. [Conceptos Fundamentales](#3-conceptos-fundamentales)
4. [La Pirámide de Testing](#4-la-pirámide-de-testing)
5. [Tests Unitarios (Unit Tests)](#5-tests-unitarios-unit-tests)
6. [Tests de Integración](#6-tests-de-integración)
7. [Tests End-to-End (E2E)](#7-tests-end-to-end-e2e)

---

## 🎯 Lo que Cubre este Manual

Este manual incluye los **fundamentos esenciales del testing**:

✅ **Introducción y conceptos** - Qué es testing, por qué es importante, ROI
✅ **La Pirámide de Testing** - Distribución correcta de tests, anti-patrones
✅ **Tests Unitarios** - Ejemplos completos en Java (JUnit 5) y JavaScript (Jest)
✅ **Tests de Integración** - Spring Boot Test, Testcontainers, MockMvc
✅ **Tests E2E** - Cypress y Playwright con flujos completos

### 📝 Contenido Adicional Disponible

Si necesitas temas avanzados, puedo agregar:
- Mocking y Stubbing (Mock vs Stub vs Spy, Mockito, Jest)
- Test-Driven Development (TDD)
- Behavior-Driven Development (BDD)
- Testing en Arquitecturas Modernas (Clean, Hexagonal, DDD)
- Herramientas y Configuración
- Estrategias por Tipo de Proyecto
- Cobertura de Código
- Testing en CI/CD
- Contract Testing
- Errores Comunes y FAQs

---

## 1. Introducción

### 1.1 ¿Qué es Testing?

**Testing (pruebas de software)** es el proceso de ejecutar un programa o sistema con la intención de encontrar errores, verificar que cumple los requisitos y asegurar que funciona correctamente bajo diferentes condiciones.

```
Testing NO es:
❌ Solo escribir código que verifica otro código
❌ Una pérdida de tiempo que retrasa entregas
❌ Responsabilidad exclusiva de QA testers
❌ Algo que se hace solo al final del desarrollo

Testing SÍ es:
✅ Especificación ejecutable del comportamiento esperado
✅ Documentación viva del sistema
✅ Red de seguridad para refactorizar con confianza
✅ Herramienta de diseño (especialmente con TDD)
✅ Reducción de costos a largo plazo
```

### 1.2 QA vs Testing vs Debugging

```
┌─────────────────────────────────────────────────────┐
│  QA (Quality Assurance)                             │
│  → Procesos y prevención                            │
│  → Define estándares, revisiones de código          │
│  → Asegura calidad en todo el ciclo de vida        │
└─────────────────────────────────────────────────────┘
                      ↓
┌─────────────────────────────────────────────────────┐
│  Testing (Pruebas)                                  │
│  → Detección de defectos                           │
│  → Ejecuta pruebas para encontrar errores          │
│  → Verifica que funciona según especificación      │
└─────────────────────────────────────────────────────┘
                      ↓
┌─────────────────────────────────────────────────────┐
│  Debugging (Depuración)                             │
│  → Corrección de defectos encontrados              │
│  → Identifica la causa raíz del error              │
│  → Implementa la solución                          │
└─────────────────────────────────────────────────────┘
```

### 1.3 ¿Por Qué es Importante?

**Estadísticas reales:**

```
Costo de arreglar un bug según etapa:

Durante desarrollo:        $100
Durante testing:           $1,500
En producción:            $10,000+
Con daño reputacional:    $100,000+

Ejemplo real:
- Bug en Amazon (1999): Error de precio causó pérdidas de $3.7M
- Bug en Knight Capital (2012): Error de trading perdió $440M en 45 min
- Bug en Heartbleed (2014): Vulnerabilidad expuso millones de passwords
```

**ROI del testing:**

```
Proyecto SIN testing:
├── Bugs en producción: 40-50 por release
├── Tiempo arreglando bugs: 60% del tiempo del equipo
├── Cliente insatisfecho: Alta rotación
└── Costo mantenimiento: 80% del presupuesto

Proyecto CON testing:
├── Bugs en producción: 2-5 por release
├── Tiempo arreglando bugs: 10-15% del tiempo
├── Cliente satisfecho: Renovaciones
└── Costo mantenimiento: 20% del presupuesto
```

---

## 2. El Problema que Resuelve

### 2.1 Problemas Sin Testing

**Problema 1: "Funciona en mi máquina"**

```java
// ❌ Sin tests - No sabes si funciona en otros ambientes
public class PaymentService {
    public void processPayment(Order order) {
        // Código que funciona en desarrollo
        // Pero falla en producción con datos reales
        double total = order.getTotal();
        creditCardGateway.charge(total); // Puede fallar
    }
}

// Usuario en producción:
// Error: NullPointerException en creditCardGateway
// Error: NumberFormatException con total = "1,234.56"
```

**Problema 2: Regresiones**

```java
// Mes 1: Implementas feature A
public double calculateDiscount(double price) {
    return price * 0.10; // 10% descuento
}

// Mes 3: Cambias lógica para feature B
public double calculateDiscount(double price, boolean isVIP) {
    if (isVIP) return price * 0.20;
    return price * 0.05; // ❌ Cambió de 10% a 5%
}

// Resultado: Feature A se rompió sin que nadie se dé cuenta
// Clientes regulares ahora tienen 5% en vez de 10%
```

**Problema 3: Miedo a Refactorizar**

```java
// ❌ Código legacy sin tests
public void processOrder(Order order) {
    // 500 líneas de código espagueti
    // Nadie se atreve a tocarlo
    // "If it works, don't touch it"

    // Resultado: Deuda técnica crece
    // Código cada vez más difícil de mantener
}

// ✅ Código con tests
public void processOrder(Order order) {
    // Puedes refactorizar con confianza
    // Tests te dicen si rompiste algo
}
```

### 2.2 Beneficios Concretos del Testing

```
1. DETECCIÓN TEMPRANA DE BUGS
   Antes:  Bug encontrado por cliente en producción
           → $10,000 de costo + reputación dañada

   Ahora:  Bug encontrado por test en desarrollo
           → $100 de costo + 10 minutos arreglarlo

2. CONFIANZA PARA CAMBIAR CÓDIGO
   Antes:  "No toques eso, puede romper algo"
           → Deuda técnica crece

   Ahora:  "Refactoriza tranquilo, los tests te cubren"
           → Código mejorable constantemente

3. DOCUMENTACIÓN VIVA
   Antes:  Documentación desactualizada en Word
           → Nadie la lee, nadie la mantiene

   Ahora:  Tests como especificación ejecutable
           → Siempre actualizada, siempre correcta

4. DISEÑO MEJORADO
   Antes:  Código acoplado, difícil de testear
           → Refuerza malas prácticas

   Ahora:  TDD fuerza código desacoplado
           → SOLID principles naturalmente

5. DEPLOYMENT SEGURO
   Antes:  Deploys los viernes con miedo
           → "No deployamos viernes"

   Ahora:  Deploys diarios con confianza
           → CI/CD automatizado
```

---

## 3. Conceptos Fundamentales

### 3.1 Anatomía de un Test

```java
@Test
public void shouldCalculateDiscountForVIPCustomer() {
    // 1. ARRANGE (Preparar)
    // Configura el estado inicial y las dependencias
    Customer customer = new Customer("John", CustomerType.VIP);
    Order order = new Order(customer, 100.0);
    DiscountCalculator calculator = new DiscountCalculator();

    // 2. ACT (Actuar)
    // Ejecuta la acción que quieres probar
    double discount = calculator.calculate(order);

    // 3. ASSERT (Afirmar)
    // Verifica que el resultado es el esperado
    assertEquals(20.0, discount); // VIP = 20% descuento
}
```

**Patrón AAA (Arrange-Act-Assert):**

```
ARRANGE (Preparar):
├── Crear objetos necesarios
├── Configurar mocks/stubs
├── Establecer estado inicial
└── Preparar datos de prueba

ACT (Actuar):
├── Ejecutar el método bajo prueba
├── Una sola acción principal
└── Capturar resultado/excepción

ASSERT (Afirmar):
├── Verificar resultado esperado
├── Verificar estado final
├── Verificar interacciones (con mocks)
└── Verificar excepciones (si aplica)
```

### 3.2 Características de un Buen Test

```java
// ❌ MAL TEST
@Test
public void test1() {
    var c = new Customer();
    var o = new Order();
    var d = calc(o);
    assertTrue(d > 0);
}

Problemas:
├── Nombre poco descriptivo ("test1")
├── Variables con nombres crípticos
├── Assertion vaga (d > 0, ¿cuánto debería ser?)
├── No se entiende qué está probando
└── No se puede mantener

// ✅ BUEN TEST
@Test
public void shouldApply20PercentDiscountForVIPCustomerWithOrderOver100() {
    // Arrange
    Customer vipCustomer = new Customer("Alice", CustomerType.VIP);
    Order orderOf150 = new Order(vipCustomer, 150.0);
    DiscountCalculator calculator = new DiscountCalculator();

    // Act
    double actualDiscount = calculator.calculate(orderOf150);

    // Assert
    double expectedDiscount = 30.0; // 20% de 150
    assertEquals(expectedDiscount, actualDiscount, 0.01);
}

Virtudes:
├── Nombre descriptivo (se lee como especificación)
├── Variables con nombres claros
├── Assertion específica (30.0 esperado)
├── Se entiende perfectamente qué prueba
└── Fácil de mantener
```

**Propiedades F.I.R.S.T:**

```
F - FAST (Rápido)
    ✅ Milisegundos o segundos, no minutos
    ✅ Sin I/O innecesario (DB, red, archivos)
    ❌ Tests lentos no se ejecutan frecuentemente

I - INDEPENDENT (Independiente)
    ✅ Cada test es autocontenido
    ✅ No depende del orden de ejecución
    ❌ Tests que fallan si se ejecutan solos

R - REPEATABLE (Repetible)
    ✅ Mismo resultado siempre
    ✅ Sin dependencias de fecha/hora actual
    ❌ Tests que fallan aleatoriamente (flaky)

S - SELF-VALIDATING (Auto-validable)
    ✅ Pass o fail automático
    ✅ Sin verificación manual
    ❌ Tests que requieren inspección visual

T - TIMELY (Oportuno)
    ✅ Escritos antes o junto con el código
    ✅ No como "tarea para después"
    ❌ Tests escritos meses después
```

### 3.3 Tipos de Tests (Overview)

```
┌──────────────────────────────────────────────────────────┐
│  UNITARIOS                                               │
│  Prueban: Funciones, métodos, clases individuales       │
│  Velocidad: Milisegundos                                │
│  Cantidad: Cientos o miles                              │
│  Ejemplo: "calculateDiscount retorna 20 para VIP"       │
└──────────────────────────────────────────────────────────┘
                          ↓
┌──────────────────────────────────────────────────────────┐
│  INTEGRACIÓN                                             │
│  Prueban: Interacción entre módulos                     │
│  Velocidad: Segundos                                     │
│  Cantidad: Decenas                                       │
│  Ejemplo: "OrderRepository guarda en DB correctamente"   │
└──────────────────────────────────────────────────────────┘
                          ↓
┌──────────────────────────────────────────────────────────┐
│  E2E (End-to-End)                                        │
│  Prueban: Flujos completos como usuario final           │
│  Velocidad: Minutos                                      │
│  Cantidad: Pocos (5-20)                                  │
│  Ejemplo: "Usuario puede completar compra exitosamente" │
└──────────────────────────────────────────────────────────┘
```

---

## 4. La Pirámide de Testing

### 4.1 Estructura de la Pirámide

```
                    ╱╲
                   ╱  ╲
                  ╱ E2E╲         ← Pocos (5-20 tests)
                 ╱──────╲          Lentos (minutos)
                ╱        ╲         Frágiles
               ╱          ╲        Alto costo mantenimiento
              ╱────────────╲
             ╱              ╲
            ╱  Integración  ╲   ← Moderados (20-100 tests)
           ╱────────────────╲     Medios (segundos)
          ╱                  ╲    Estables
         ╱                    ╲
        ╱──────────────────────╲
       ╱                        ╲
      ╱        Unitarios         ╲ ← Muchos (100-1000+ tests)
     ╱──────────────────────────╲   Rápidos (milisegundos)
    ────────────────────────────    Muy estables
                                    Bajo costo
```

### 4.2 Distribución Recomendada

```
Proyecto típico (backend + frontend):

UNITARIOS:           70%    (700 tests)
├── Lógica de negocio
├── Validaciones
├── Cálculos
├── Transformaciones
└── Utilidades

INTEGRACIÓN:         20%    (200 tests)
├── Repository + DB
├── API controllers
├── Servicios externos (mocked)
├── Message queues
└── Cache

E2E:                 10%    (100 tests)
├── Flujos críticos de usuario
├── Checkout/Payments
├── Login/Signup
├── Features principales
└── Smoke tests post-deploy
```

### 4.3 Anti-Patrón: Pirámide Invertida

```
❌ ANTI-PATRÓN (Pirámide Invertida)

      ╱────────────────────╲
     ╱                      ╲
    ╱         E2E            ╲   ← 500 E2E tests
   ╱──────────────────────────╲
  ╱                            ╲
 ╱         Integración          ╲ ← 100 integration
╱──────────────────────────────────╲
                                    ← 20 unit tests
────────────────────────────────────

Problemas:
├── Suite tarda 2-3 horas en correr
├── Tests frágiles (rompen con cambios de UI)
├── Difícil identificar qué falló
├── Alto costo de mantenimiento
└── Equipo deja de ejecutar tests

✅ SOLUCIÓN: Invertir la pirámide
- Mover lógica a tests unitarios
- Reducir E2E a flujos críticos
- Fortalecer tests de integración
```

### 4.4 Ejemplo Práctico de la Pirámide

**Feature:** Sistema de descuentos

```java
// ══════════════════════════════════════════════════════════
// NIVEL 1: TESTS UNITARIOS (70%) - Base de la pirámide
// ══════════════════════════════════════════════════════════

// Test 1: Lógica de cálculo básica
@Test
public void shouldCalculate10PercentDiscountForRegularCustomer() {
    DiscountCalculator calculator = new DiscountCalculator();
    double discount = calculator.calculate(100.0, CustomerType.REGULAR);
    assertEquals(10.0, discount);
}

// Test 2: Caso borde - precio cero
@Test
public void shouldReturn0DiscountWhenPriceIsZero() {
    DiscountCalculator calculator = new DiscountCalculator();
    double discount = calculator.calculate(0.0, CustomerType.VIP);
    assertEquals(0.0, discount);
}

// Test 3: Caso borde - precio negativo
@Test
public void shouldThrowExceptionWhenPriceIsNegative() {
    DiscountCalculator calculator = new DiscountCalculator();
    assertThrows(IllegalArgumentException.class, () -> {
        calculator.calculate(-10.0, CustomerType.REGULAR);
    });
}

// Test 4: VIP customer
@Test
public void shouldCalculate20PercentDiscountForVIPCustomer() {
    DiscountCalculator calculator = new DiscountCalculator();
    double discount = calculator.calculate(100.0, CustomerType.VIP);
    assertEquals(20.0, discount);
}

// Test 5: Premium customer
@Test
public void shouldCalculate15PercentDiscountForPremiumCustomer() {
    DiscountCalculator calculator = new DiscountCalculator();
    double discount = calculator.calculate(100.0, CustomerType.PREMIUM);
    assertEquals(15.0, discount);
}

// ... 10+ tests unitarios más cubriendo todos los casos

// ══════════════════════════════════════════════════════════
// NIVEL 2: TESTS DE INTEGRACIÓN (20%)
// ══════════════════════════════════════════════════════════

@SpringBootTest
@AutoConfigureTestDatabase
public class DiscountServiceIntegrationTest {

    @Autowired
    private DiscountService discountService;

    @Autowired
    private CustomerRepository customerRepository;

    // Test 1: Integración con base de datos
    @Test
    public void shouldApplyDiscountAndSaveToDatabase() {
        // Arrange
        Customer customer = new Customer("John", CustomerType.VIP);
        customerRepository.save(customer);
        Order order = new Order(customer, 100.0);

        // Act
        DiscountedOrder result = discountService.applyDiscount(order);

        // Assert
        assertEquals(80.0, result.getFinalPrice()); // 100 - 20% = 80

        // Verify saved to DB
        Order savedOrder = orderRepository.findById(result.getId()).get();
        assertEquals(80.0, savedOrder.getFinalPrice());
    }

    // Test 2: Integración con servicio externo (mocked)
    @Test
    public void shouldNotifyLoyaltyProgramWhenDiscountApplied() {
        // Mock del servicio externo
        when(loyaltyService.addPoints(any(), anyDouble()))
            .thenReturn(true);

        Customer customer = new Customer("Alice", CustomerType.VIP);
        Order order = new Order(customer, 200.0);

        discountService.applyDiscount(order);

        // Verify interaction
        verify(loyaltyService).addPoints(customer.getId(), 40.0);
    }
}

// ══════════════════════════════════════════════════════════
// NIVEL 3: TESTS E2E (10%) - Punta de la pirámide
// ══════════════════════════════════════════════════════════

@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
public class CheckoutE2ETest {

    @Test
    public void userCanCompleteCheckoutWithVIPDiscount() {
        // Flujo completo como usuario real

        // 1. Login como VIP
        loginAsVIP("alice@example.com", "password");

        // 2. Agregar productos al carrito
        addToCart("Product A", 100.0);
        addToCart("Product B", 50.0);

        // 3. Ir a checkout
        goToCheckout();

        // 4. Verificar descuento aplicado
        assertDiscountShown("30.00"); // 20% de 150
        assertTotalShown("120.00");   // 150 - 30

        // 5. Completar pago
        fillPaymentInfo("4111111111111111", "12/25", "123");
        clickConfirmPurchase();

        // 6. Verificar orden creada
        assertOrderConfirmationShown();
        assertEmailSent("alice@example.com");
    }
}
```

**Distribución:**
- Unitarios: 15 tests (lógica de descuento, validaciones, casos borde)
- Integración: 3 tests (DB, servicios externos, API)
- E2E: 1 test (flujo completo de checkout)

**Tiempo de ejecución:**
- Unitarios: 150ms total (10ms c/u)
- Integración: 5 segundos total
- E2E: 30 segundos total
- **TOTAL: ~35 segundos** ← Ejecutable en cada commit

---

## 5. Tests Unitarios (Unit Tests)

### 5.1 ¿Qué es un Test Unitario?

```
Un test unitario prueba una UNIDAD de código aislada:
├── Una función
├── Un método
├── Una clase (sin sus dependencias reales)
└── Un módulo pequeño

Características:
✅ Rápido (milisegundos)
✅ Aislado (sin dependencias externas)
✅ Determinista (siempre el mismo resultado)
✅ Fácil de entender y mantener
```

### 5.2 Ejemplo Completo: Tests Unitarios en Java

**Clase a testear:**

```java
public class OrderValidator {

    private static final double MIN_ORDER_AMOUNT = 10.0;
    private static final double MAX_ORDER_AMOUNT = 10000.0;

    public ValidationResult validate(Order order) {
        if (order == null) {
            return ValidationResult.error("Order cannot be null");
        }

        if (order.getCustomer() == null) {
            return ValidationResult.error("Customer is required");
        }

        if (order.getItems().isEmpty()) {
            return ValidationResult.error("Order must have at least one item");
        }

        double total = order.getTotal();

        if (total < MIN_ORDER_AMOUNT) {
            return ValidationResult.error(
                "Order total must be at least " + MIN_ORDER_AMOUNT
            );
        }

        if (total > MAX_ORDER_AMOUNT) {
            return ValidationResult.error(
                "Order total cannot exceed " + MAX_ORDER_AMOUNT
            );
        }

        return ValidationResult.success();
    }
}
```

**Tests unitarios (JUnit 5):**

```java
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.BeforeEach;
import static org.junit.jupiter.api.Assertions.*;

@DisplayName("OrderValidator Unit Tests")
public class OrderValidatorTest {

    private OrderValidator validator;
    private Customer defaultCustomer;

    @BeforeEach
    public void setUp() {
        validator = new OrderValidator();
        defaultCustomer = new Customer("John Doe");
    }

    // ═══════════════════════════════════════════════════════
    // CASOS FELICES (Happy Path)
    // ═══════════════════════════════════════════════════════

    @Test
    @DisplayName("Should validate successfully when order is valid")
    public void shouldValidateValidOrder() {
        // Arrange
        Order order = new Order(defaultCustomer);
        order.addItem(new OrderItem("Product A", 50.0));

        // Act
        ValidationResult result = validator.validate(order);

        // Assert
        assertTrue(result.isSuccess());
        assertNull(result.getError());
    }

    // ═══════════════════════════════════════════════════════
    // CASOS NULOS
    // ═══════════════════════════════════════════════════════

    @Test
    @DisplayName("Should fail when order is null")
    public void shouldFailWhenOrderIsNull() {
        // Act
        ValidationResult result = validator.validate(null);

        // Assert
        assertFalse(result.isSuccess());
        assertEquals("Order cannot be null", result.getError());
    }

    @Test
    @DisplayName("Should fail when customer is null")
    public void shouldFailWhenCustomerIsNull() {
        // Arrange
        Order order = new Order(null); // Sin customer
        order.addItem(new OrderItem("Product A", 50.0));

        // Act
        ValidationResult result = validator.validate(order);

        // Assert
        assertFalse(result.isSuccess());
        assertEquals("Customer is required", result.getError());
    }

    // ═══════════════════════════════════════════════════════
    // CASOS VACÍOS
    // ═══════════════════════════════════════════════════════

    @Test
    @DisplayName("Should fail when order has no items")
    public void shouldFailWhenOrderIsEmpty() {
        // Arrange
        Order order = new Order(defaultCustomer);
        // No agregamos items

        // Act
        ValidationResult result = validator.validate(order);

        // Assert
        assertFalse(result.isSuccess());
        assertEquals("Order must have at least one item", result.getError());
    }

    // ═══════════════════════════════════════════════════════
    // CASOS BORDE (Boundary)
    // ═══════════════════════════════════════════════════════

    @Test
    @DisplayName("Should fail when total is below minimum")
    public void shouldFailWhenTotalIsBelowMinimum() {
        // Arrange
        Order order = new Order(defaultCustomer);
        order.addItem(new OrderItem("Cheap Item", 5.0)); // < 10

        // Act
        ValidationResult result = validator.validate(order);

        // Assert
        assertFalse(result.isSuccess());
        assertTrue(result.getError().contains("at least 10.0"));
    }

    @Test
    @DisplayName("Should validate when total is exactly minimum")
    public void shouldValidateWhenTotalIsExactlyMinimum() {
        // Arrange
        Order order = new Order(defaultCustomer);
        order.addItem(new OrderItem("Item", 10.0)); // Exactamente 10

        // Act
        ValidationResult result = validator.validate(order);

        // Assert
        assertTrue(result.isSuccess());
    }

    @Test
    @DisplayName("Should validate when total is exactly maximum")
    public void shouldValidateWhenTotalIsExactlyMaximum() {
        // Arrange
        Order order = new Order(defaultCustomer);
        order.addItem(new OrderItem("Expensive", 10000.0)); // Exactamente 10000

        // Act
        ValidationResult result = validator.validate(order);

        // Assert
        assertTrue(result.isSuccess());
    }

    @Test
    @DisplayName("Should fail when total exceeds maximum")
    public void shouldFailWhenTotalExceedsMaximum() {
        // Arrange
        Order order = new Order(defaultCustomer);
        order.addItem(new OrderItem("Too Expensive", 15000.0)); // > 10000

        // Act
        ValidationResult result = validator.validate(order);

        // Assert
        assertFalse(result.isSuccess());
        assertTrue(result.getError().contains("cannot exceed 10000.0"));
    }

    // ═══════════════════════════════════════════════════════
    // TESTS PARAMETRIZADOS (Múltiples casos con misma lógica)
    // ═══════════════════════════════════════════════════════

    @ParameterizedTest
    @ValueSource(doubles = {10.0, 50.0, 100.0, 5000.0, 10000.0})
    @DisplayName("Should validate orders with valid amounts")
    public void shouldValidateOrdersWithValidAmounts(double amount) {
        // Arrange
        Order order = new Order(defaultCustomer);
        order.addItem(new OrderItem("Product", amount));

        // Act
        ValidationResult result = validator.validate(order);

        // Assert
        assertTrue(result.isSuccess());
    }

    @ParameterizedTest
    @ValueSource(doubles = {0.0, 5.0, 9.99, 10000.01, 20000.0})
    @DisplayName("Should fail for invalid amounts")
    public void shouldFailForInvalidAmounts(double amount) {
        // Arrange
        Order order = new Order(defaultCustomer);
        order.addItem(new OrderItem("Product", amount));

        // Act
        ValidationResult result = validator.validate(order);

        // Assert
        assertFalse(result.isSuccess());
    }
}
```

### 5.3 Ejemplo Completo: Tests Unitarios en JavaScript/Jest

**Función a testear:**

```javascript
// discountCalculator.js
export class DiscountCalculator {
  constructor() {
    this.rules = {
      REGULAR: 0.10,  // 10%
      PREMIUM: 0.15,  // 15%
      VIP: 0.20       // 20%
    };
  }

  calculate(price, customerType) {
    if (price < 0) {
      throw new Error('Price cannot be negative');
    }

    if (price === 0) {
      return 0;
    }

    const discountRate = this.rules[customerType];

    if (discountRate === undefined) {
      throw new Error(`Unknown customer type: ${customerType}`);
    }

    return price * discountRate;
  }

  applyDiscount(price, customerType) {
    const discount = this.calculate(price, customerType);
    return price - discount;
  }
}
```

**Tests (Jest):**

```javascript
// discountCalculator.test.js
import { DiscountCalculator } from './discountCalculator';

describe('DiscountCalculator', () => {
  let calculator;

  beforeEach(() => {
    calculator = new DiscountCalculator();
  });

  // ═══════════════════════════════════════════════════════
  // CASOS FELICES
  // ═══════════════════════════════════════════════════════

  describe('calculate()', () => {
    it('should calculate 10% discount for REGULAR customers', () => {
      // Arrange
      const price = 100;
      const customerType = 'REGULAR';

      // Act
      const result = calculator.calculate(price, customerType);

      // Assert
      expect(result).toBe(10); // 10% de 100
    });

    it('should calculate 20% discount for VIP customers', () => {
      const price = 100;
      const customerType = 'VIP';

      const result = calculator.calculate(price, customerType);

      expect(result).toBe(20); // 20% de 100
    });

    it('should calculate 15% discount for PREMIUM customers', () => {
      const price = 100;
      const customerType = 'PREMIUM';

      const result = calculator.calculate(price, customerType);

      expect(result).toBe(15); // 15% de 100
    });
  });

  // ═══════════════════════════════════════════════════════
  // CASOS BORDE
  // ═══════════════════════════════════════════════════════

  describe('edge cases', () => {
    it('should return 0 when price is 0', () => {
      const result = calculator.calculate(0, 'REGULAR');
      expect(result).toBe(0);
    });

    it('should throw error when price is negative', () => {
      expect(() => {
        calculator.calculate(-10, 'REGULAR');
      }).toThrow('Price cannot be negative');
    });

    it('should throw error for unknown customer type', () => {
      expect(() => {
        calculator.calculate(100, 'UNKNOWN');
      }).toThrow('Unknown customer type: UNKNOWN');
    });

    it('should handle decimal prices correctly', () => {
      const result = calculator.calculate(99.99, 'VIP');
      expect(result).toBeCloseTo(19.998, 2);
    });
  });

  // ═══════════════════════════════════════════════════════
  // TESTS PARAMETRIZADOS con test.each
  // ═══════════════════════════════════════════════════════

  describe('applyDiscount()', () => {
    test.each([
      [100, 'REGULAR', 90],    // 100 - 10% = 90
      [100, 'PREMIUM', 85],    // 100 - 15% = 85
      [100, 'VIP', 80],        // 100 - 20% = 80
      [50, 'REGULAR', 45],     // 50 - 10% = 45
      [200, 'VIP', 160],       // 200 - 20% = 160
    ])(
      'should apply discount: price=%d, type=%s → final=%d',
      (price, type, expectedFinal) => {
        const result = calculator.applyDiscount(price, type);
        expect(result).toBe(expectedFinal);
      }
    );
  });

  // ═══════════════════════════════════════════════════════
  // SNAPSHOT TESTING (para objetos complejos)
  // ═══════════════════════════════════════════════════════

  it('should match snapshot of calculator rules', () => {
    expect(calculator.rules).toMatchSnapshot();
  });
});
```

### 5.4 Patrones Avanzados de Unit Testing

**Pattern 1: Test Fixtures (Datos reutilizables)**

```java
public class OrderTestFixtures {

    public static Customer createDefaultCustomer() {
        return new Customer("John Doe", "john@example.com");
    }

    public static Customer createVIPCustomer() {
        Customer customer = createDefaultCustomer();
        customer.setType(CustomerType.VIP);
        return customer;
    }

    public static Order createOrderWithItems(Customer customer, double... prices) {
        Order order = new Order(customer);
        for (int i = 0; i < prices.length; i++) {
            order.addItem(new OrderItem("Product " + (i+1), prices[i]));
        }
        return order;
    }

    public static Order createValidOrder() {
        return createOrderWithItems(createDefaultCustomer(), 50.0, 30.0);
    }
}

// Uso en tests:
@Test
public void shouldProcessValidOrder() {
    Order order = OrderTestFixtures.createValidOrder();
    // ... test logic
}
```

**Pattern 2: Test Builders**

```java
public class OrderBuilder {
    private Customer customer = new Customer("Default");
    private List<OrderItem> items = new ArrayList<>();

    public OrderBuilder withCustomer(Customer customer) {
        this.customer = customer;
        return this;
    }

    public OrderBuilder withVIPCustomer() {
        this.customer = new Customer("VIP Customer", CustomerType.VIP);
        return this;
    }

    public OrderBuilder withItem(String name, double price) {
        this.items.add(new OrderItem(name, price));
        return this;
    }

    public OrderBuilder withTotalOf(double targetTotal) {
        this.items.add(new OrderItem("Product", targetTotal));
        return this;
    }

    public Order build() {
        Order order = new Order(customer);
        items.forEach(order::addItem);
        return order;
    }
}

// Uso en tests (fluent API):
@Test
public void shouldApplyVIPDiscount() {
    Order order = new OrderBuilder()
        .withVIPCustomer()
        .withItem("Product A", 100.0)
        .withItem("Product B", 50.0)
        .build();

    double discount = calculator.calculate(order);
    assertEquals(30.0, discount); // 20% de 150
}
```

**Pattern 3: Custom Assertions**

```java
public class OrderAssertions {

    public static void assertValidOrder(Order order) {
        assertNotNull(order, "Order should not be null");
        assertNotNull(order.getCustomer(), "Order should have a customer");
        assertFalse(order.getItems().isEmpty(), "Order should have items");
        assertTrue(order.getTotal() > 0, "Order total should be positive");
    }

    public static void assertOrderHasDiscount(Order order, double expectedDiscount) {
        assertValidOrder(order);
        assertEquals(expectedDiscount, order.getDiscount(), 0.01);
    }
}

// Uso:
@Test
public void shouldCreateValidOrder() {
    Order order = orderService.create(customer, items);
    OrderAssertions.assertValidOrder(order);
}
```

---

## 6. Tests de Integración

### 6.1 ¿Qué es un Test de Integración?

```
Tests de integración verifican que MÚLTIPLES componentes
trabajan juntos correctamente:

├── Repository + Base de datos real
├── Controller + Service + Repository
├── Service + API externa (simulada)
├── Message producer + Queue + Consumer
└── Módulo completo con dependencias

Diferencia con unitarios:
❌ Unitario: Repository con DB mockeada
✅ Integración: Repository con DB real (o Testcontainers)
```

### 6.2 Ejemplo: Test de Integración con Spring Boot

```java
@SpringBootTest
@AutoConfigureTestDatabase(replace = Replace.NONE) // Usar DB real
@Testcontainers // Contenedor Docker para DB
public class OrderRepositoryIntegrationTest {

    // Testcontainers - Base de datos en Docker
    @Container
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:15")
        .withDatabaseName("testdb")
        .withUsername("test")
        .withPassword("test");

    @DynamicPropertySource
    static void configureProperties(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", postgres::getJdbcUrl);
        registry.add("spring.datasource.username", postgres::getUsername);
        registry.add("spring.datasource.password", postgres::getPassword);
    }

    @Autowired
    private OrderRepository orderRepository;

    @Autowired
    private CustomerRepository customerRepository;

    @BeforeEach
    public void setUp() {
        orderRepository.deleteAll();
        customerRepository.deleteAll();
    }

    // ═══════════════════════════════════════════════════════
    // TEST 1: CRUD Básico
    // ═══════════════════════════════════════════════════════

    @Test
    public void shouldSaveAndRetrieveOrder() {
        // Arrange
        Customer customer = new Customer("Alice", "alice@example.com");
        customerRepository.save(customer);

        Order order = new Order(customer);
        order.addItem(new OrderItem("Product A", 50.0));
        order.addItem(new OrderItem("Product B", 30.0));

        // Act
        Order savedOrder = orderRepository.save(order);
        Order retrievedOrder = orderRepository.findById(savedOrder.getId())
            .orElseThrow();

        // Assert
        assertNotNull(retrievedOrder.getId());
        assertEquals(customer.getId(), retrievedOrder.getCustomer().getId());
        assertEquals(2, retrievedOrder.getItems().size());
        assertEquals(80.0, retrievedOrder.getTotal());
    }

    // ═══════════════════════════════════════════════════════
    // TEST 2: Queries Complejas
    // ═══════════════════════════════════════════════════════

    @Test
    public void shouldFindOrdersByCustomerAndDateRange() {
        // Arrange
        Customer customer = customerRepository.save(
            new Customer("Bob", "bob@example.com")
        );

        LocalDateTime now = LocalDateTime.now();

        Order order1 = new Order(customer, now.minusDays(5));
        Order order2 = new Order(customer, now.minusDays(2));
        Order order3 = new Order(customer, now.plusDays(1)); // Futuro

        orderRepository.saveAll(List.of(order1, order2, order3));

        // Act
        List<Order> ordersInRange = orderRepository.findByCustomerAndDateRange(
            customer.getId(),
            now.minusDays(7),
            now
        );

        // Assert
        assertEquals(2, ordersInRange.size());
        assertTrue(ordersInRange.contains(order1));
        assertTrue(ordersInRange.contains(order2));
        assertFalse(ordersInRange.contains(order3)); // Futuro no incluido
    }

    // ═══════════════════════════════════════════════════════
    // TEST 3: Transacciones
    // ═══════════════════════════════════════════════════════

    @Test
    public void shouldRollbackTransactionOnError() {
        // Arrange
        Customer customer = customerRepository.save(
            new Customer("Charlie", "charlie@example.com")
        );

        Order validOrder = new Order(customer);
        validOrder.addItem(new OrderItem("Product", 50.0));

        // Act & Assert
        assertThrows(DataIntegrityViolationException.class, () -> {
            orderRepository.save(validOrder);

            // Esto viola constraint (customer no puede ser null en DB)
            Order invalidOrder = new Order(null);
            orderRepository.save(invalidOrder); // Falla aquí
        });

        // Verify rollback - validOrder tampoco debería existir
        List<Order> orders = orderRepository.findAll();
        assertEquals(0, orders.size());
    }

    // ═══════════════════════════════════════════════════════
    // TEST 4: Cascade Operations
    // ═══════════════════════════════════════════════════════

    @Test
    public void shouldCascadeDeleteOrderItems() {
        // Arrange
        Customer customer = customerRepository.save(
            new Customer("Dave", "dave@example.com")
        );

        Order order = new Order(customer);
        order.addItem(new OrderItem("Item 1", 10.0));
        order.addItem(new OrderItem("Item 2", 20.0));

        Order savedOrder = orderRepository.save(order);
        Long orderId = savedOrder.getId();

        // Verify items were saved
        assertEquals(2, savedOrder.getItems().size());

        // Act - Delete order
        orderRepository.deleteById(orderId);

        // Assert - Items should be deleted too (cascade)
        Optional<Order> deletedOrder = orderRepository.findById(orderId);
        assertFalse(deletedOrder.isPresent());

        // Verify items are also gone (cascade delete)
        // (esto depende de tu configuración de cascade en JPA)
    }
}
```

### 6.3 Ejemplo: Test de Controller (MockMvc)

```java
@SpringBootTest
@AutoConfigureMockMvc
public class OrderControllerIntegrationTest {

    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private ObjectMapper objectMapper;

    @MockBean
    private EmailService emailService; // Mock de servicio externo

    @Autowired
    private OrderRepository orderRepository;

    @Autowired
    private CustomerRepository customerRepository;

    private Customer testCustomer;

    @BeforeEach
    public void setUp() {
        orderRepository.deleteAll();
        customerRepository.deleteAll();

        testCustomer = customerRepository.save(
            new Customer("Test User", "test@example.com")
        );
    }

    // ═══════════════════════════════════════════════════════
    // TEST 1: POST - Crear Order
    // ═══════════════════════════════════════════════════════

    @Test
    public void shouldCreateOrderSuccessfully() throws Exception {
        // Arrange
        CreateOrderRequest request = new CreateOrderRequest();
        request.setCustomerId(testCustomer.getId());
        request.setItems(List.of(
            new OrderItemDTO("Product A", 50.0),
            new OrderItemDTO("Product B", 30.0)
        ));

        String requestJson = objectMapper.writeValueAsString(request);

        // Act & Assert
        mockMvc.perform(post("/api/orders")
                .contentType(MediaType.APPLICATION_JSON)
                .content(requestJson))
            .andExpect(status().isCreated())
            .andExpect(jsonPath("$.id").exists())
            .andExpect(jsonPath("$.customer.id").value(testCustomer.getId()))
            .andExpect(jsonPath("$.items.length()").value(2))
            .andExpect(jsonPath("$.total").value(80.0))
            .andExpect(jsonPath("$.status").value("PENDING"));

        // Verify saved to database
        List<Order> orders = orderRepository.findAll();
        assertEquals(1, orders.size());
        assertEquals(80.0, orders.get(0).getTotal());
    }

    // ═══════════════════════════════════════════════════════
    // TEST 2: POST - Validación de errores
    // ═══════════════════════════════════════════════════════

    @Test
    public void shouldReturn400WhenOrderIsInvalid() throws Exception {
        // Arrange - Order sin items (inválido)
        CreateOrderRequest request = new CreateOrderRequest();
        request.setCustomerId(testCustomer.getId());
        request.setItems(Collections.emptyList()); // Sin items

        String requestJson = objectMapper.writeValueAsString(request);

        // Act & Assert
        mockMvc.perform(post("/api/orders")
                .contentType(MediaType.APPLICATION_JSON)
                .content(requestJson))
            .andExpect(status().isBadRequest())
            .andExpect(jsonPath("$.error").value("Order must have at least one item"));

        // Verify NOT saved to database
        List<Order> orders = orderRepository.findAll();
        assertEquals(0, orders.size());
    }

    // ═══════════════════════════════════════════════════════
    // TEST 3: GET - Obtener Order por ID
    // ═══════════════════════════════════════════════════════

    @Test
    public void shouldGetOrderById() throws Exception {
        // Arrange
        Order order = new Order(testCustomer);
        order.addItem(new OrderItem("Product", 100.0));
        Order savedOrder = orderRepository.save(order);

        // Act & Assert
        mockMvc.perform(get("/api/orders/" + savedOrder.getId()))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.id").value(savedOrder.getId()))
            .andExpect(jsonPath("$.total").value(100.0));
    }

    @Test
    public void shouldReturn404WhenOrderNotFound() throws Exception {
        mockMvc.perform(get("/api/orders/99999"))
            .andExpect(status().isNotFound())
            .andExpect(jsonPath("$.error").value("Order not found"));
    }

    // ═══════════════════════════════════════════════════════
    // TEST 4: PUT - Actualizar Order
    // ═══════════════════════════════════════════════════════

    @Test
    public void shouldUpdateOrderStatus() throws Exception {
        // Arrange
        Order order = new Order(testCustomer);
        order.addItem(new OrderItem("Product", 100.0));
        Order savedOrder = orderRepository.save(order);

        UpdateOrderStatusRequest request = new UpdateOrderStatusRequest();
        request.setStatus("CONFIRMED");

        String requestJson = objectMapper.writeValueAsString(request);

        // Act & Assert
        mockMvc.perform(put("/api/orders/" + savedOrder.getId() + "/status")
                .contentType(MediaType.APPLICATION_JSON)
                .content(requestJson))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.status").value("CONFIRMED"));

        // Verify updated in database
        Order updatedOrder = orderRepository.findById(savedOrder.getId())
            .orElseThrow();
        assertEquals(OrderStatus.CONFIRMED, updatedOrder.getStatus());

        // Verify email was sent (mock verification)
        verify(emailService).sendOrderConfirmation(savedOrder.getId());
    }

    // ═══════════════════════════════════════════════════════
    // TEST 5: DELETE - Cancelar Order
    // ═══════════════════════════════════════════════════════

    @Test
    public void shouldCancelOrder() throws Exception {
        // Arrange
        Order order = new Order(testCustomer);
        order.addItem(new OrderItem("Product", 100.0));
        Order savedOrder = orderRepository.save(order);

        // Act & Assert
        mockMvc.perform(delete("/api/orders/" + savedOrder.getId()))
            .andExpect(status().isNoContent());

        // Verify order is cancelled (soft delete o cambio de status)
        Order cancelledOrder = orderRepository.findById(savedOrder.getId())
            .orElseThrow();
        assertEquals(OrderStatus.CANCELLED, cancelledOrder.getStatus());
    }

    // ═══════════════════════════════════════════════════════
    // TEST 6: GET - List con paginación
    // ═══════════════════════════════════════════════════════

    @Test
    public void shouldGetOrdersWithPagination() throws Exception {
        // Arrange - Crear 25 orders
        for (int i = 0; i < 25; i++) {
            Order order = new Order(testCustomer);
            order.addItem(new OrderItem("Product " + i, 10.0 * i));
            orderRepository.save(order);
        }

        // Act & Assert - Page 1
        mockMvc.perform(get("/api/orders?page=0&size=10"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.content.length()").value(10))
            .andExpect(jsonPath("$.totalElements").value(25))
            .andExpect(jsonPath("$.totalPages").value(3))
            .andExpect(jsonPath("$.number").value(0));

        // Page 3 (última)
        mockMvc.perform(get("/api/orders?page=2&size=10"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.content.length()").value(5)) // Solo 5 en última página
            .andExpect(jsonPath("$.number").value(2))
            .andExpect(jsonPath("$.last").value(true));
    }
}
```

### 6.4 Ejemplo: Test de Integración con Testcontainers

```java
@SpringBootTest
@Testcontainers
public class OrderServiceIntegrationTest {

    // Contenedor PostgreSQL
    @Container
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:15");

    // Contenedor Redis
    @Container
    static GenericContainer<?> redis = new GenericContainer<>("redis:7")
        .withExposedPorts(6379);

    @DynamicPropertySource
    static void configureProperties(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", postgres::getJdbcUrl);
        registry.add("spring.datasource.username", postgres::getUsername);
        registry.add("spring.datasource.password", postgres::getPassword);
        registry.add("spring.redis.host", redis::getHost);
        registry.add("spring.redis.port", redis::getFirstMappedPort);
    }

    @Autowired
    private OrderService orderService;

    @Autowired
    private OrderRepository orderRepository;

    @Autowired
    private RedisTemplate<String, Object> redisTemplate;

    @Test
    public void shouldCacheOrderAfterFirstRetrieval() {
        // Arrange
        Customer customer = new Customer("Test");
        Order order = new Order(customer);
        order.addItem(new OrderItem("Product", 100.0));
        Order savedOrder = orderRepository.save(order);

        // Act - First call (hits database)
        Order firstCall = orderService.getById(savedOrder.getId());

        // Delete from database to verify cache is used
        orderRepository.deleteById(savedOrder.getId());

        // Second call (should hit cache, not database)
        Order secondCall = orderService.getById(savedOrder.getId());

        // Assert
        assertNotNull(secondCall); // Found in cache
        assertEquals(firstCall.getId(), secondCall.getId());

        // Verify from Redis directly
        String cacheKey = "order:" + savedOrder.getId();
        Object cached = redisTemplate.opsForValue().get(cacheKey);
        assertNotNull(cached);
    }
}
```

---

## 7. Tests End-to-End (E2E)

### 7.1 ¿Qué es un Test E2E?

```
Tests E2E prueban el sistema COMPLETO como lo haría un usuario real:

├── Frontend + Backend + Base de datos + Servicios externos
├── Simula clicks, inputs, navegación
├── Verifica flujos completos de principio a fin
└── Más lentos pero más realistas

Ejemplo:
"Usuario puede registrarse, login, agregar producto al
carrito, completar checkout y recibir confirmación"
```

### 7.2 Ejemplo: Test E2E con Cypress

```javascript
// cypress/e2e/checkout.cy.js

describe('Checkout Flow', () => {
  beforeEach(() => {
    // Setup - Limpiar DB y crear datos de prueba
    cy.task('db:seed');
    cy.visit('/');
  });

  it('user can complete full checkout process as VIP', () => {
    // ═══════════════════════════════════════════════════════
    // STEP 1: Login como VIP
    // ═══════════════════════════════════════════════════════

    cy.visit('/login');
    cy.get('[data-testid="email-input"]')
      .type('vip@example.com');
    cy.get('[data-testid="password-input"]')
      .type('password123');
    cy.get('[data-testid="login-button"]')
      .click();

    // Verify logged in
    cy.url().should('include', '/dashboard');
    cy.contains('Welcome, VIP User').should('be.visible');

    // ═══════════════════════════════════════════════════════
    // STEP 2: Browse products
    // ═══════════════════════════════════════════════════════

    cy.get('[data-testid="products-link"]').click();
    cy.url().should('include', '/products');

    // ═══════════════════════════════════════════════════════
    // STEP 3: Add products to cart
    // ═══════════════════════════════════════════════════════

    // Add first product
    cy.get('[data-testid="product-card"]')
      .first()
      .within(() => {
        cy.get('[data-testid="product-name"]')
          .should('contain', 'Product A');
        cy.get('[data-testid="product-price"]')
          .should('contain', '$100.00');
        cy.get('[data-testid="add-to-cart-button"]')
          .click();
      });

    // Verify toast notification
    cy.contains('Product added to cart').should('be.visible');

    // Verify cart badge updated
    cy.get('[data-testid="cart-badge"]')
      .should('contain', '1');

    // Add second product
    cy.get('[data-testid="product-card"]')
      .eq(1)
      .within(() => {
        cy.get('[data-testid="add-to-cart-button"]')
          .click();
      });

    cy.get('[data-testid="cart-badge"]')
      .should('contain', '2');

    // ═══════════════════════════════════════════════════════
    // STEP 4: View cart
    // ═══════════════════════════════════════════════════════

    cy.get('[data-testid="cart-icon"]').click();
    cy.url().should('include', '/cart');

    // Verify items in cart
    cy.get('[data-testid="cart-item"]')
      .should('have.length', 2);

    // Verify subtotal
    cy.get('[data-testid="cart-subtotal"]')
      .should('contain', '$150.00');

    // Verify VIP discount applied
    cy.get('[data-testid="cart-discount"]')
      .should('contain', '-$30.00'); // 20% de 150

    // Verify total
    cy.get('[data-testid="cart-total"]')
      .should('contain', '$120.00'); // 150 - 30

    // ═══════════════════════════════════════════════════════
    // STEP 5: Proceed to checkout
    // ═══════════════════════════════════════════════════════

    cy.get('[data-testid="checkout-button"]').click();
    cy.url().should('include', '/checkout');

    // ═══════════════════════════════════════════════════════
    // STEP 6: Fill shipping information
    // ═══════════════════════════════════════════════════════

    cy.get('[data-testid="shipping-address"]')
      .type('123 Main St');
    cy.get('[data-testid="shipping-city"]')
      .type('New York');
    cy.get('[data-testid="shipping-zip"]')
      .type('10001');

    cy.get('[data-testid="continue-to-payment-button"]')
      .click();

    // ═══════════════════════════════════════════════════════
    // STEP 7: Fill payment information
    // ═══════════════════════════════════════════════════════

    // Wait for payment form to load
    cy.get('[data-testid="payment-form"]')
      .should('be.visible');

    // Use test credit card
    cy.get('[data-testid="card-number"]')
      .type('4111111111111111'); // Visa test card
    cy.get('[data-testid="card-expiry"]')
      .type('12/25');
    cy.get('[data-testid="card-cvv"]')
      .type('123');
    cy.get('[data-testid="card-name"]')
      .type('VIP User');

    // ═══════════════════════════════════════════════════════
    // STEP 8: Review and confirm
    // ═══════════════════════════════════════════════════════

    cy.get('[data-testid="review-items"]')
      .should('contain', 'Product A')
      .and('contain', 'Product B');

    cy.get('[data-testid="review-total"]')
      .should('contain', '$120.00');

    // Accept terms
    cy.get('[data-testid="terms-checkbox"]')
      .check();

    // Place order
    cy.get('[data-testid="place-order-button"]')
      .click();

    // ═══════════════════════════════════════════════════════
    // STEP 9: Verify order confirmation
    // ═══════════════════════════════════════════════════════

    // Wait for processing
    cy.get('[data-testid="processing-spinner"]', { timeout: 10000 })
      .should('not.exist');

    // Should redirect to confirmation page
    cy.url().should('include', '/order-confirmation');

    // Verify confirmation message
    cy.contains('Order placed successfully!').should('be.visible');

    // Verify order number
    cy.get('[data-testid="order-number"]')
      .should('exist')
      .invoke('text')
      .should('match', /ORD-\d+/);

    // Verify email sent message
    cy.contains('A confirmation email has been sent to vip@example.com')
      .should('be.visible');

    // ═══════════════════════════════════════════════════════
    // STEP 10: Verify order appears in account
    // ═══════════════════════════════════════════════════════

    cy.get('[data-testid="my-orders-link"]').click();
    cy.url().should('include', '/orders');

    cy.get('[data-testid="order-list"]')
      .find('[data-testid="order-item"]')
      .first()
      .should('contain', '$120.00')
      .and('contain', 'Confirmed');
  });

  // ═══════════════════════════════════════════════════════
  // TEST 2: Error handling
  // ═══════════════════════════════════════════════════════

  it('should show error when payment fails', () => {
    // Login y agregar productos (helpers)
    cy.loginAs('vip@example.com', 'password123');
    cy.addProductToCart('Product A');
    cy.goToCheckout();

    // Fill shipping
    cy.fillShippingInfo({
      address: '123 Main St',
      city: 'New York',
      zip: '10001'
    });

    // Use DECLINED test card
    cy.get('[data-testid="card-number"]')
      .type('4000000000000002'); // Card that will be declined
    cy.get('[data-testid="card-expiry"]').type('12/25');
    cy.get('[data-testid="card-cvv"]').type('123');

    cy.get('[data-testid="place-order-button"]').click();

    // Verify error message
    cy.get('[data-testid="payment-error"]')
      .should('be.visible')
      .and('contain', 'Payment declined');

    // User should still be on checkout page
    cy.url().should('include', '/checkout');

    // Order should NOT be created
    cy.visit('/orders');
    cy.get('[data-testid="order-list"]')
      .should('not.contain', '$100.00');
  });
});
```

### 7.3 Custom Commands en Cypress

```javascript
// cypress/support/commands.js

// Helper para login
Cypress.Commands.add('loginAs', (email, password) => {
  cy.visit('/login');
  cy.get('[data-testid="email-input"]').type(email);
  cy.get('[data-testid="password-input"]').type(password);
  cy.get('[data-testid="login-button"]').click();
  cy.url().should('not.include', '/login');
});

// Helper para agregar al carrito
Cypress.Commands.add('addProductToCart', (productName) => {
  cy.visit('/products');
  cy.contains('[data-testid="product-card"]', productName)
    .find('[data-testid="add-to-cart-button"]')
    .click();
  cy.contains('Product added to cart').should('be.visible');
});

// Helper para ir a checkout
Cypress.Commands.add('goToCheckout', () => {
  cy.get('[data-testid="cart-icon"]').click();
  cy.get('[data-testid="checkout-button"]').click();
  cy.url().should('include', '/checkout');
});

// Helper para llenar info de envío
Cypress.Commands.add('fillShippingInfo', (info) => {
  cy.get('[data-testid="shipping-address"]').type(info.address);
  cy.get('[data-testid="shipping-city"]').type(info.city);
  cy.get('[data-testid="shipping-zip"]').type(info.zip);
  cy.get('[data-testid="continue-to-payment-button"]').click();
});
```

### 7.4 Ejemplo: Test E2E con Playwright

```javascript
// tests/checkout.spec.ts
import { test, expect } from '@playwright/test';

test.describe('Checkout Flow', () => {
  test.beforeEach(async ({ page }) => {
    // Setup
    await page.goto('/');
  });

  test('complete checkout as VIP customer', async ({ page }) => {
    // Login
    await page.click('[data-testid="login-link"]');
    await page.fill('[data-testid="email-input"]', 'vip@example.com');
    await page.fill('[data-testid="password-input"]', 'password123');
    await page.click('[data-testid="login-button"]');

    // Verify logged in
    await expect(page).toHaveURL(/.*dashboard/);
    await expect(page.locator('text=Welcome, VIP User')).toBeVisible();

    // Add product to cart
    await page.click('[data-testid="products-link"]');
    await page.locator('[data-testid="product-card"]').first()
      .locator('[data-testid="add-to-cart-button"]')
      .click();

    // Verify cart badge
    await expect(page.locator('[data-testid="cart-badge"]'))
      .toHaveText('1');

    // Go to cart
    await page.click('[data-testid="cart-icon"]');

    // Verify discount
    await expect(page.locator('[data-testid="cart-discount"]'))
      .toContainText('-$20.00');

    // Checkout
    await page.click('[data-testid="checkout-button"]');

    // Fill shipping
    await page.fill('[data-testid="shipping-address"]', '123 Main St');
    await page.fill('[data-testid="shipping-city"]', 'New York');
    await page.fill('[data-testid="shipping-zip"]', '10001');
    await page.click('[data-testid="continue-to-payment-button"]');

    // Fill payment
    await page.fill('[data-testid="card-number"]', '4111111111111111');
    await page.fill('[data-testid="card-expiry"]', '12/25');
    await page.fill('[data-testid="card-cvv"]', '123');

    // Place order
    await page.check('[data-testid="terms-checkbox"]');
    await page.click('[data-testid="place-order-button"]');

    // Verify confirmation
    await expect(page).toHaveURL(/.*order-confirmation/);
    await expect(page.locator('text=Order placed successfully!'))
      .toBeVisible();

    // Take screenshot for visual verification
    await page.screenshot({ path: 'test-results/order-confirmation.png' });
  });

  test('visual regression test for product page', async ({ page }) => {
    await page.goto('/products');

    // Wait for products to load
    await page.waitForSelector('[data-testid="product-card"]');

    // Visual comparison
    expect(await page.screenshot()).toMatchSnapshot('products-page.png');
  });
});
```

---

Continúa en el siguiente mensaje debido a límite de caracteres...