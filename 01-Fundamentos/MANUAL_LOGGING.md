# Manual Completo de Logging de Software
## Guía Profesional: De Principiante a Experto

> Manual profesional sobre logging, observabilidad y trazabilidad para proyectos de software modernos

---

## 📚 Índice

1. [Introducción](#1-introducción)
2. [El Problema que Resuelve](#2-el-problema-que-resuelve)
3. [Conceptos Fundamentales](#3-conceptos-fundamentales)
4. [Niveles de Logging](#4-niveles-de-logging)
5. [Mejores Prácticas](#5-mejores-prácticas)
6. [Logging Estructurado](#6-logging-estructurado)
7. [Frameworks y Herramientas](#7-frameworks-y-herramientas)
8. [Logging en Producción](#8-logging-en-producción)
9. [Logging Distribuido](#9-logging-distribuido)
10. [Anti-Patrones Comunes](#10-anti-patrones-comunes)

---

## 🎯 Lo que Cubre este Manual

Este manual incluye los **fundamentos esenciales del logging**:

✅ **Introducción y conceptos** - Qué es logging, por qué es crítico, ROI
✅ **Niveles de logging** - TRACE, DEBUG, INFO, WARN, ERROR, FATAL
✅ **Mejores prácticas** - Qué loguear, cómo loguear, cuándo loguear
✅ **Logging estructurado** - JSON, campos estándar, correlación
✅ **Frameworks** - Log4j2, SLF4J, Logback, Winston, Serilog
✅ **Producción** - Agregación, búsqueda, alertas, retención
✅ **Sistemas distribuidos** - Trace IDs, contexto, correlación


## 1. Introducción

### 1.1 ¿Qué es Logging?

**Logging** es el proceso de registrar eventos que ocurren durante la ejecución de una aplicación. Es la "caja negra" de tu software que te permite entender qué pasó, cuándo pasó y por qué pasó.

```
Logging NO es:
❌ Solo usar System.out.println() o console.log()
❌ Opcional o "nice to have"
❌ Solo para debugging en desarrollo
❌ Algo que agregas cuando ya tienes problemas

Logging SÍ es:
✅ Sistema crítico de observabilidad
✅ Primera línea de defensa para diagnosticar problemas
✅ Evidencia forense cuando algo falla
✅ Herramienta de análisis de comportamiento
✅ Requisito para sistemas en producción
```

### 1.2 La Diferencia entre Logging, Monitoring y Debugging

```
┌─────────────────────────────────────────────────────┐
│  LOGGING                                            │
│  → Registro de eventos históricos                  │
│  → "¿Qué pasó?"                                     │
│  → Nivel: Aplicación                                │
│  → Ejemplo: "Usuario X hizo login a las 14:32"     │
└─────────────────────────────────────────────────────┘
                      ↓
┌─────────────────────────────────────────────────────┐
│  MONITORING                                         │
│  → Observación de métricas en tiempo real          │
│  → "¿Qué está pasando AHORA?"                      │
│  → Nivel: Sistema                                   │
│  → Ejemplo: "CPU al 80%, 500 req/seg"              │
└─────────────────────────────────────────────────────┘
                      ↓
┌─────────────────────────────────────────────────────┐
│  DEBUGGING                                          │
│  → Investigación interactiva de código             │
│  → "¿Por qué está fallando esto?"                  │
│  → Nivel: Código                                    │
│  → Ejemplo: Breakpoints, step-through              │
└─────────────────────────────────────────────────────┘
```

### 1.3 ¿Por Qué es Crítico el Logging?

**Casos reales donde el logging salvó el día:**

```
CASO 1: Bug Intermitente en Producción
Problema: "A veces" los pagos fallan, pero no se puede reproducir
Solución: Logs revelaron que fallaba solo cuando el monto tenía
          más de 2 decimales (bug de precisión flotante)
Sin logs: Imposible de diagnosticar
Con logs: Resuelto en 30 minutos

CASO 2: Brecha de Seguridad
Problema: Usuario reporta cargos no autorizados
Solución: Logs de auditoría mostraron:
          - IP sospechosa desde otro país
          - Patrón de intentos de login fallidos
          - Token de sesión robado
Sin logs: Sin evidencia, sin acción legal
Con logs: Usuario compensado, atacante identificado

CASO 3: Performance Degradation
Problema: Aplicación "se pone lenta" al azar
Solución: Logs de timing mostraron que DB queries lentas
          ocurrían cuando cache expiraba
Sin logs: "Debe ser el servidor"
Con logs: Problema de cache identificado y resuelto
```

**Estadísticas:**

```
Tiempo promedio de resolución de incidentes:

SIN logging adecuado:    4-8 horas
CON logging adecuado:    30-60 minutos

Reducción: 75-90% del tiempo

Costo de downtime:
- E-commerce: $5,600 por minuto (Amazon)
- SaaS B2B: $9,000 por minuto
- Fintech: $14,000 por minuto

Ahorro con logging efectivo:
- Detección temprana: 80% menos downtime
- Resolución rápida: 90% menos tiempo
- ROI: 10x-50x la inversión
```

---

## 2. El Problema que Resuelve

### 2.1 Problemas Sin Logging Adecuado

**Problema 1: "No sé qué pasó"**

```java
// ❌ SIN LOGS - Debugging imposible
public void processPayment(Order order) {
    try {
        paymentGateway.charge(order.getTotal());
        order.setStatus(OrderStatus.PAID);
        orderRepository.save(order);
    } catch (Exception e) {
        // Silencio total - No sabemos qué falló
    }
}

// Usuario: "Mi pago falló"
// Dev: "No tengo idea de por qué"
// Resultado: Cliente perdido, reputación dañada

// ✅ CON LOGS - Visibilidad total
public void processPayment(Order order) {
    logger.info("Processing payment for order {}", order.getId());

    try {
        logger.debug("Charging amount: {} to payment gateway", order.getTotal());
        paymentGateway.charge(order.getTotal());

        logger.debug("Updating order status to PAID");
        order.setStatus(OrderStatus.PAID);
        orderRepository.save(order);

        logger.info("Payment processed successfully for order {}", order.getId());
    } catch (PaymentGatewayException e) {
        logger.error("Payment gateway error for order {}: {}",
                    order.getId(), e.getMessage(), e);
        throw new PaymentFailedException("Payment failed", e);
    } catch (Exception e) {
        logger.error("Unexpected error processing payment for order {}",
                    order.getId(), e);
        throw e;
    }
}

// Ahora en logs vemos:
// 2024-12-24 14:32:15 INFO  Processing payment for order ORD-123
// 2024-12-24 14:32:15 DEBUG Charging amount: 150.50 to payment gateway
// 2024-12-24 14:32:16 ERROR Payment gateway error for order ORD-123:
//                           Insufficient funds
```

**Problema 2: "Funciona en desarrollo, falla en producción"**

```javascript
// ❌ SIN LOGS - No visibilidad del entorno
function getUserProfile(userId) {
    const user = database.query(`SELECT * FROM users WHERE id = ${userId}`);
    return user;
}

// Funciona en desarrollo (DB local, datos limpios)
// Falla en producción (DB remota, datos reales)
// Sin logs: "No sé por qué falla en prod"

// ✅ CON LOGS - Contexto completo
function getUserProfile(userId) {
    logger.info('Fetching user profile', {
        userId,
        environment: process.env.NODE_ENV
    });

    try {
        const startTime = Date.now();
        const user = database.query(`SELECT * FROM users WHERE id = ?`, [userId]);
        const duration = Date.now() - startTime;

        logger.info('User profile fetched successfully', {
            userId,
            duration,
            userFound: !!user
        });

        return user;
    } catch (error) {
        logger.error('Error fetching user profile', {
            userId,
            environment: process.env.NODE_ENV,
            dbHost: database.host,
            error: error.message,
            stack: error.stack
        });
        throw error;
    }
}

// Ahora logs revelan:
// - Environment: production
// - DB host: prod-db-replica-2 (ah! es la réplica que tiene problemas)
// - Error: Connection timeout after 30s
```

**Problema 3: "¿Quién hizo qué?"**

```java
// ❌ SIN LOGS DE AUDITORÍA
public void deleteUser(Long userId) {
    userRepository.deleteById(userId);
}

// Pregunta: "¿Quién borró la cuenta del CEO?"
// Respuesta: "No tenemos forma de saberlo"

// ✅ CON LOGS DE AUDITORÍA
public void deleteUser(Long userId, String performedBy) {
    User user = userRepository.findById(userId)
        .orElseThrow(() -> new UserNotFoundException(userId));

    auditLogger.warn("USER_DELETED", Map.of(
        "action", "DELETE_USER",
        "targetUserId", userId,
        "targetUserEmail", user.getEmail(),
        "performedBy", performedBy,
        "timestamp", Instant.now(),
        "ipAddress", getRequestIpAddress(),
        "userAgent", getRequestUserAgent()
    ));

    userRepository.deleteById(userId);
}

// Logs de auditoría:
// 2024-12-24 14:32:15 WARN [AUDIT] USER_DELETED
// {
//   "action": "DELETE_USER",
//   "targetUserId": 12345,
//   "targetUserEmail": "ceo@company.com",
//   "performedBy": "admin@company.com",
//   "timestamp": "2024-12-24T14:32:15Z",
//   "ipAddress": "192.168.1.100",
//   "userAgent": "Mozilla/5.0..."
// }
```

### 2.2 Beneficios Concretos del Logging

```
1. RESOLUCIÓN RÁPIDA DE INCIDENTES
   Sin logs: "Reinicia el servidor y reza"
   Con logs: "Error en línea X, causa Y, solución Z"

   Tiempo de resolución: De 4 horas → 30 minutos

2. ANÁLISIS POST-MORTEM
   Sin logs: "Creemos que fue..."
   Con logs: "Sabemos exactamente qué pasó"

   Confianza en RCA: De 30% → 95%

3. DETECCIÓN PROACTIVA DE PROBLEMAS
   Sin logs: Cliente te avisa del problema
   Con logs: Alertas te avisan ANTES que el cliente

   MTTR (Mean Time To Repair): De 2 horas → 15 minutos

4. COMPLIANCE Y AUDITORÍA
   Sin logs: Multas regulatorias
   Con logs: Evidencia de compliance

   GDPR/HIPAA/SOC2: Requerimiento obligatorio

5. OPTIMIZACIÓN DE PERFORMANCE
   Sin logs: "La app está lenta, no sé por qué"
   Con logs: "Este endpoint tarda 5s, el 80% es una query SQL"

   Identificación de bottlenecks: De semanas → horas

6. SEGURIDAD Y DETECCIÓN DE AMENAZAS
   Sin logs: Brecha descubierta meses después
   Con logs: Detección en tiempo real de patrones sospechosos

   Tiempo de detección: De 200 días → 24 horas
```

---

## 3. Conceptos Fundamentales

### 3.1 Anatomía de un Log Entry

```json
{
  "timestamp": "2024-12-24T14:32:15.123Z",      // CUÁNDO
  "level": "ERROR",                              // SEVERIDAD
  "logger": "com.company.OrderService",          // DÓNDE (componente)
  "thread": "http-nio-8080-exec-5",             // CONTEXTO (thread)
  "message": "Failed to process payment",        // QUÉ PASÓ
  "context": {                                   // CONTEXTO DEL NEGOCIO
    "orderId": "ORD-123",
    "userId": "USR-456",
    "amount": 150.50,
    "currency": "USD"
  },
  "error": {                                     // DETALLES DEL ERROR
    "type": "PaymentGatewayException",
    "message": "Insufficient funds",
    "stackTrace": "at com.company..."
  },
  "metadata": {                                  // METADATA DEL SISTEMA
    "hostname": "prod-server-03",
    "environment": "production",
    "version": "1.2.3",
    "traceId": "550e8400-e29b-41d4-a716-446655440000",
    "spanId": "f3f9f3c4a1b2c3d4"
  }
}
```

**Componentes esenciales:**

```
1. TIMESTAMP (Cuándo)
   ✅ Siempre en UTC/ISO8601
   ✅ Con milisegundos (o microsegundos)
   ❌ Nunca en timezone local

2. LEVEL (Severidad)
   TRACE → DEBUG → INFO → WARN → ERROR → FATAL

3. LOGGER NAME (Dónde en el código)
   ✅ Fully qualified class name
   ✅ Namespace jerárquico

4. MESSAGE (Qué pasó)
   ✅ Descriptivo y conciso
   ✅ Sin información sensible
   ❌ Sin placeholders sin reemplazar

5. CONTEXT (Contexto del negocio)
   ✅ IDs relevantes (userId, orderId, etc.)
   ✅ Valores clave para debugging

6. METADATA (Metadata del sistema)
   ✅ Hostname, environment, version
   ✅ TraceID para correlación
```

### 3.2 Logging Síncrono vs Asíncrono

```java
// ═══════════════════════════════════════════════════════
// SÍNCRONO - Bloquea el thread hasta que el log se escribe
// ═══════════════════════════════════════════════════════

public void processOrder(Order order) {
    logger.info("Processing order {}", order.getId());  // ⏸️ Espera
    // Thread bloqueado ~1-5ms mientras escribe al archivo

    orderService.process(order);
}

Ventajas:
✅ Garantiza que el log se escribió
✅ Orden estricto de logs
✅ Más simple de configurar

Desventajas:
❌ Impacto en performance (1-5ms por log)
❌ Thread bloqueado durante I/O
❌ No escalable para alto volumen

Cuándo usar:
- Logs críticos de auditoría
- Bajo volumen (<1000 logs/seg)
- Garantías de entrega requeridas

// ═══════════════════════════════════════════════════════
// ASÍNCRONO - No bloquea, usa cola en memoria
// ═══════════════════════════════════════════════════════

public void processOrder(Order order) {
    logger.info("Processing order {}", order.getId());  // ⚡ Instantáneo
    // Log va a cola en memoria, thread continúa inmediatamente

    orderService.process(order);
}

Ventajas:
✅ Casi cero impacto en performance (<0.1ms)
✅ Alto throughput (100k+ logs/seg)
✅ No bloquea threads de aplicación

Desventajas:
❌ Logs pueden perderse si app crashea
❌ Orden puede variar ligeramente
❌ Usa memoria para cola (buffer)

Cuándo usar:
- Alto volumen de logs
- Performance crítica
- Logs no críticos (DEBUG, INFO)

// ═══════════════════════════════════════════════════════
// CONFIGURACIÓN: Log4j2 Async
// ═══════════════════════════════════════════════════════

<Configuration>
    <Appenders>
        <!-- Async wrapper -->
        <Async name="AsyncFile" bufferSize="512">
            <AppenderRef ref="File"/>
        </Async>

        <File name="File" fileName="app.log">
            <PatternLayout pattern="%d{ISO8601} %-5level %c{1} - %msg%n"/>
        </File>
    </Appenders>

    <Loggers>
        <Root level="info">
            <AppenderRef ref="AsyncFile"/>
        </Root>
    </Loggers>
</Configuration>
```

### 3.3 Structured Logging vs Plain Text

```java
// ═══════════════════════════════════════════════════════
// PLAIN TEXT (Tradicional)
// ═══════════════════════════════════════════════════════

logger.info("User john@example.com logged in from 192.168.1.1");

// Resultado:
// 2024-12-24 14:32:15 INFO User john@example.com logged in from 192.168.1.1

Problemas:
❌ Difícil de parsear programáticamente
❌ No se puede buscar por campo específico
❌ No se puede agregar/analizar fácilmente
❌ Formato inconsistente entre developers

// ═══════════════════════════════════════════════════════
// STRUCTURED LOGGING (JSON)
// ═══════════════════════════════════════════════════════

logger.info("User logged in", Map.of(
    "userId", "USR-123",
    "email", "john@example.com",
    "ipAddress", "192.168.1.1",
    "userAgent", "Chrome/120.0",
    "loginMethod", "password"
));

// Resultado (JSON):
{
  "timestamp": "2024-12-24T14:32:15.123Z",
  "level": "INFO",
  "message": "User logged in",
  "userId": "USR-123",
  "email": "john@example.com",
  "ipAddress": "192.168.1.1",
  "userAgent": "Chrome/120.0",
  "loginMethod": "password"
}

Ventajas:
✅ Fácil de parsear (JSON parsing)
✅ Búsqueda por campo: userId="USR-123"
✅ Agregaciones: COUNT(DISTINCT userId)
✅ Formato consistente y estándar
✅ Compatible con ELK, Splunk, CloudWatch

Ejemplo de búsqueda en Elasticsearch:
{
  "query": {
    "bool": {
      "must": [
        { "term": { "userId": "USR-123" }},
        { "range": { "timestamp": { "gte": "2024-12-24T00:00:00Z" }}}
      ]
    }
  }
}
```

---

## 4. Niveles de Logging

### 4.1 Jerarquía de Niveles

```
TRACE → DEBUG → INFO → WARN → ERROR → FATAL
  ↑                                      ↑
Menos severo                        Más severo
Más verboso                         Menos verboso
Solo desarrollo                     Siempre visible
```

### 4.2 TRACE

```java
logger.trace("Entering method calculateDiscount with params: {}, {}",
             customerId, orderTotal);
logger.trace("Loop iteration {}: processing item {}", i, item.getId());
logger.trace("Cache hit for key: {}", cacheKey);

Cuándo usar:
✅ Tracing de ejecución línea por línea
✅ Debugging muy detallado
✅ Análisis de flujo de datos

Cuándo NO usar:
❌ NUNCA en producción (too verbose)
❌ Información que no sea útil para debugging

Configuración típica:
- Desarrollo: OFF o ON solo para clases específicas
- Staging: OFF
- Producción: OFF

Overhead:
- Alto (puede generar GB de logs en minutos)
```

### 4.3 DEBUG

```java
logger.debug("Fetching user {} from database", userId);
logger.debug("Query executed in {}ms: {}", duration, sql);
logger.debug("Cache miss for key {}, fetching from origin", key);
logger.debug("Validating order {}: items count = {}", orderId, items.size());

Cuándo usar:
✅ Información útil para debugging
✅ Valores de variables importantes
✅ Pasos intermedios de algoritmos
✅ Resultados de queries/cálculos

Cuándo NO usar:
❌ Información que no ayuda a debug
❌ Dentro de loops intensivos
❌ Datos sensibles (passwords, tokens)

Configuración típica:
- Desarrollo: ON
- Staging: ON (o por clase específica)
- Producción: OFF (activar solo para troubleshooting)

Ejemplo real:
// ❌ DEBUG mal usado
logger.debug("i = " + i);  // No ayuda
logger.debug("Entering loop");  // Obvio del código

// ✅ DEBUG bien usado
logger.debug("Fetching products for category {}, page {}, size {}",
             categoryId, page, size);
logger.debug("Applied discount: original=${}, discount={}%, final=${}",
             originalPrice, discountPercent, finalPrice);
```

### 4.4 INFO

```java
logger.info("Application started successfully on port {}", port);
logger.info("User {} logged in from IP {}", userId, ipAddress);
logger.info("Order {} created with {} items, total ${}",
            orderId, itemCount, total);
logger.info("Payment processed: orderId={}, amount=${}, status={}",
            orderId, amount, status);
logger.info("Scheduled job 'cleanupExpiredSessions' started");

Cuándo usar:
✅ Eventos importantes del negocio
✅ Cambios de estado significativos
✅ Inicio/fin de procesos importantes
✅ Milestones en flujos de trabajo

Cuándo NO usar:
❌ Eventos muy frecuentes (>100/segundo)
❌ Detalles técnicos internos
❌ Información solo útil para developers

Configuración típica:
- Desarrollo: ON
- Staging: ON
- Producción: ON (nivel por defecto)

Ejemplos por tipo de aplicación:

// E-Commerce
logger.info("Order placed: orderId={}, userId={}, total=${}",
            orderId, userId, total);

// Banking
logger.info("Transaction completed: txnId={}, from={}, to={}, amount=${}",
            txnId, fromAccount, toAccount, amount);

// SaaS
logger.info("User signed up: userId={}, plan={}, referrer={}",
            userId, plan, referrer);
```

### 4.5 WARN

```java
logger.warn("API call to payment gateway took {}ms (threshold: 3000ms)",
            duration);
logger.warn("Retrying failed operation, attempt {} of {}",
            attempt, maxRetries);
logger.warn("Cache unavailable, falling back to database");
logger.warn("Deprecated API endpoint /v1/users called by client {}",
            clientId);
logger.warn("User {} attempted to access unauthorized resource {}",
            userId, resourceId);

Cuándo usar:
✅ Situaciones anormales pero recuperables
✅ Uso de fallbacks o degradación de servicio
✅ Deprecations
✅ Exceder thresholds de performance
✅ Intentos de acceso no autorizado

Cuándo NO usar:
❌ Situaciones esperadas y normales
❌ Errores críticos (usa ERROR)
❌ Información solo para debugging

Configuración típica:
- Desarrollo: ON
- Staging: ON
- Producción: ON + Alertas

Ejemplo de configuración de alertas:
// Si más de 10 WARNs en 5 minutos → notificar a on-call

if (logCount.warn > 10 in last 5 minutes) {
  sendPagerDutyAlert("High warning rate detected");
}

Ejemplos detallados:

// ❌ WARN mal usado
logger.warn("User not found");  // Esto es ERROR

// ✅ WARN bien usado
logger.warn("Database query took {}ms, exceeds threshold of {}ms. " +
            "Consider optimizing query: {}",
            duration, threshold, query);

logger.warn("External service timeout after {}ms, using cached data. " +
            "Service: {}, endpoint: {}",
            timeout, serviceName, endpoint);
```

### 4.6 ERROR

```java
logger.error("Failed to process payment for order {}: {}",
             orderId, e.getMessage(), e);
logger.error("Database connection failed: {}", e.getMessage(), e);
logger.error("Unable to send email to {}: {}",
             email, e.getMessage(), e);
logger.error("Unexpected null value for required field: userId in order {}",
             orderId);

Cuándo usar:
✅ Errores que afectan funcionalidad
✅ Excepciones no manejadas
✅ Operaciones críticas fallidas
✅ Data integrity issues

Cuándo NO usar:
❌ Validaciones de negocio esperadas
❌ Errores ya manejados en capas superiores
❌ Situaciones recuperables (usa WARN)

Configuración típica:
- Desarrollo: ON
- Staging: ON + Alertas
- Producción: ON + Alertas inmediatas

IMPORTANTE - Siempre incluir la excepción:

// ❌ ERROR sin stacktrace
logger.error("Payment failed: " + e.getMessage());

// ✅ ERROR con stacktrace completo
logger.error("Payment failed for order {}: {}",
             orderId, e.getMessage(), e);  // ← La 'e' al final es crucial

Ejemplos contextualizados:

logger.error("Critical: Failed to charge payment. " +
            "orderId={}, userId={}, amount=${}, gateway={}, error={}",
            orderId, userId, amount, gateway, e.getMessage(), e);

logger.error("Data corruption detected: Order {} has negative total ${}. " +
            "This should never happen. Investigate immediately.",
            orderId, total);
```

### 4.7 FATAL

```java
logger.fatal("Unable to connect to database on startup. " +
            "Application cannot function. Exiting.", e);
logger.fatal("Critical configuration missing: PAYMENT_GATEWAY_KEY. " +
            "Cannot start application.", e);
logger.fatal("Out of memory error. Application terminating.", e);

Cuándo usar:
✅ Errores que causan shutdown de la aplicación
✅ Corrupción de datos críticos
✅ Pérdida de recursos vitales
✅ Estado irrecuperable del sistema

Cuándo NO usar:
❌ Errores recuperables
❌ Problemas transitorios
❌ Errores de requests individuales

Configuración típica:
- Desarrollo: ON
- Staging: ON + Alertas PagerDuty
- Producción: ON + Alertas P0 inmediatas

IMPORTANTE: FATAL debe ir seguido de shutdown

logger.fatal("Database connection pool exhausted and unrecoverable. " +
            "Shutting down application to prevent data corruption.", e);
System.exit(1);  // Terminar la aplicación

Ejemplos:

// Startup fatal error
public static void main(String[] args) {
    try {
        Database.connect();
    } catch (DatabaseConnectionException e) {
        logger.fatal("FATAL: Cannot connect to database. " +
                    "Host: {}, Port: {}, Error: {}. Application exiting.",
                    dbHost, dbPort, e.getMessage(), e);
        System.exit(1);
    }
}

// Runtime fatal error
if (availableMemory < CRITICAL_THRESHOLD) {
    logger.fatal("FATAL: Available memory {} is below critical threshold {}. " +
                "Risk of OOM. Initiating graceful shutdown.",
                availableMemory, CRITICAL_THRESHOLD);
    gracefulShutdown();
}
```

### 4.8 Decisión de Nivel: Diagrama de Flujo

```
                    ┌─────────────────────┐
                    │ Necesitas loguear   │
                    │  algo?              │
                    └──────────┬──────────┘
                               │
                    ┌──────────▼──────────┐
                    │ ¿La app va a        │
                    │  terminar/crashear? │
                    └──────────┬──────────┘
                            Sí │
                    ┌──────────▼──────────┐
                    │     FATAL           │
                    └─────────────────────┘
                               │ No
                    ┌──────────▼──────────┐
                    │ ¿Es una excepción   │
                    │  que rompe funcio-  │
                    │  nalidad?           │
                    └──────────┬──────────┘
                            Sí │
                    ┌──────────▼──────────┐
                    │     ERROR           │
                    └─────────────────────┘
                               │ No
                    ┌──────────▼──────────┐
                    │ ¿Es anormal pero    │
                    │  recuperable?       │
                    └──────────┬──────────┘
                            Sí │
                    ┌──────────▼──────────┐
                    │     WARN            │
                    └─────────────────────┘
                               │ No
                    ┌──────────▼──────────┐
                    │ ¿Es evento de       │
                    │  negocio importante?│
                    └──────────┬──────────┘
                            Sí │
                    ┌──────────▼──────────┐
                    │     INFO            │
                    └─────────────────────┘
                               │ No
                    ┌──────────▼──────────┐
                    │ ¿Es para debugging  │
                    │  de developers?     │
                    └──────────┬──────────┘
                            Sí │
                    ┌──────────▼──────────┐
                    │     DEBUG           │
                    └─────────────────────┘
                               │ No
                    ┌──────────▼──────────┐
                    │ ¿Es tracing muy     │
                    │  detallado?         │
                    └──────────┬──────────┘
                            Sí │
                    ┌──────────▼──────────┐
                    │     TRACE           │
                    └─────────────────────┘
                               │ No
                    ┌──────────▼──────────┐
                    │ Probablemente no    │
                    │ necesitas loguear   │
                    └─────────────────────┘
```

---

## 5. Mejores Prácticas

### 5.1 QUÉ Loguear

```java
// ═══════════════════════════════════════════════════════
// ✅ LOGUEAR: Entrada a funciones críticas
// ═══════════════════════════════════════════════════════

public Order createOrder(CreateOrderRequest request) {
    logger.info("Creating order for user {}, items count: {}",
                request.getUserId(), request.getItems().size());
    // ... lógica
}

// ═══════════════════════════════════════════════════════
// ✅ LOGUEAR: Salida de funciones críticas (con resultado)
// ═══════════════════════════════════════════════════════

public Order createOrder(CreateOrderRequest request) {
    // ... lógica
    logger.info("Order created successfully: orderId={}, total=${}",
                order.getId(), order.getTotal());
    return order;
}

// ═══════════════════════════════════════════════════════
// ✅ LOGUEAR: Cambios de estado
// ═══════════════════════════════════════════════════════

public void updateOrderStatus(Long orderId, OrderStatus newStatus) {
    Order order = findById(orderId);
    OrderStatus oldStatus = order.getStatus();

    order.setStatus(newStatus);
    orderRepository.save(order);

    logger.info("Order status changed: orderId={}, from={}, to={}",
                orderId, oldStatus, newStatus);
}

// ═══════════════════════════════════════════════════════
// ✅ LOGUEAR: Llamadas a servicios externos
// ═══════════════════════════════════════════════════════

public PaymentResult chargeCard(PaymentRequest request) {
    logger.info("Calling payment gateway: amount=${}, gateway={}",
                request.getAmount(), gatewayName);

    long startTime = System.currentTimeMillis();

    try {
        PaymentResult result = paymentGateway.charge(request);
        long duration = System.currentTimeMillis() - startTime;

        logger.info("Payment gateway response: txnId={}, status={}, duration={}ms",
                    result.getTransactionId(), result.getStatus(), duration);

        return result;
    } catch (PaymentGatewayException e) {
        long duration = System.currentTimeMillis() - startTime;

        logger.error("Payment gateway error after {}ms: {}",
                    duration, e.getMessage(), e);
        throw e;
    }
}

// ═══════════════════════════════════════════════════════
// ✅ LOGUEAR: Decisiones de negocio importantes
// ═══════════════════════════════════════════════════════

public double calculateDiscount(Order order) {
    Customer customer = order.getCustomer();

    if (customer.isVIP()) {
        logger.info("Applying VIP discount for customer {}: 20%",
                    customer.getId());
        return order.getTotal() * 0.20;
    }

    if (order.getTotal() > 1000) {
        logger.info("Applying bulk order discount for order {}: 15%",
                    order.getId());
        return order.getTotal() * 0.15;
    }

    logger.debug("No discount applied for order {}", order.getId());
    return 0;
}

// ═══════════════════════════════════════════════════════
// ✅ LOGUEAR: Excepciones y errores
// ═══════════════════════════════════════════════════════

try {
    processPayment(order);
} catch (InsufficientFundsException e) {
    logger.warn("Payment declined due to insufficient funds: " +
                "orderId={}, userId={}, amount=${}",
                order.getId(), order.getUserId(), order.getTotal());
    throw e;
} catch (PaymentGatewayException e) {
    logger.error("Payment gateway error for order {}: {}",
                order.getId(), e.getMessage(), e);
    throw e;
} catch (Exception e) {
    logger.error("Unexpected error processing payment for order {}",
                order.getId(), e);
    throw e;
}

// ═══════════════════════════════════════════════════════
// ✅ LOGUEAR: Performance metrics
// ═══════════════════════════════════════════════════════

long startTime = System.currentTimeMillis();
List<Product> products = productRepository.findByCategoryId(categoryId);
long duration = System.currentTimeMillis() - startTime;

if (duration > 1000) {
    logger.warn("Slow query detected: findByCategoryId took {}ms. " +
                "categoryId={}, results count={}",
                duration, categoryId, products.size());
} else {
    logger.debug("Query executed: findByCategoryId in {}ms, results count={}",
                duration, products.size());
}

// ═══════════════════════════════════════════════════════
// ✅ LOGUEAR: Auditoría de seguridad
// ═══════════════════════════════════════════════════════

auditLogger.info("User authentication attempt", Map.of(
    "email", email,
    "ipAddress", request.getRemoteAddr(),
    "userAgent", request.getHeader("User-Agent"),
    "success", true,
    "timestamp", Instant.now()
));

auditLogger.warn("Failed login attempt", Map.of(
    "email", email,
    "ipAddress", request.getRemoteAddr(),
    "failureReason", "Invalid password",
    "attemptCount", loginAttempts.incrementAndGet(),
    "timestamp", Instant.now()
));

// ═══════════════════════════════════════════════════════
// ✅ LOGUEAR: Configuración de aplicación en startup
// ═══════════════════════════════════════════════════════

@PostConstruct
public void logConfiguration() {
    logger.info("Application configuration: " +
                "environment={}, version={}, database={}, cacheEnabled={}",
                environment, version, databaseUrl, cacheEnabled);
}
```

### 5.2 QUÉ NO Loguear

```java
// ═══════════════════════════════════════════════════════
// ❌ NO LOGUEAR: Información sensible
// ═══════════════════════════════════════════════════════

// ❌ NUNCA
logger.info("User login: email={}, password={}", email, password);
logger.debug("Credit card: {}", creditCardNumber);
logger.info("API key: {}", apiKey);
logger.debug("Social security number: {}", ssn);

// ✅ MEJOR
logger.info("User login: email={}", email);  // Sin password
logger.info("Payment method added: type={}, last4={}",
            paymentType, last4Digits);  // Solo últimos 4 dígitos
logger.debug("API request authenticated");  // Sin mostrar key

// ═══════════════════════════════════════════════════════
// ❌ NO LOGUEAR: PII sin redacción
// ═══════════════════════════════════════════════════════

// ❌ GDPR violation
logger.info("User profile: {}", user);  // Contiene PII completo

// ✅ MEJOR (con redacción)
logger.info("User profile updated: userId={}, email={}",
            user.getId(), maskEmail(user.getEmail()));  // nombre***@domain.com

// Función de redacción
private String maskEmail(String email) {
    String[] parts = email.split("@");
    if (parts.length == 2) {
        String name = parts[0];
        String masked = name.substring(0, Math.min(name.length(), 3)) + "***";
        return masked + "@" + parts[1];
    }
    return "***@***";
}

// ═══════════════════════════════════════════════════════
// ❌ NO LOGUEAR: Dentro de loops intensivos
// ═══════════════════════════════════════════════════════

// ❌ Genera millones de logs
for (Product product : allProducts) {  // 1 millón de productos
    logger.debug("Processing product {}", product.getId());
    // ...
}

// ✅ MEJOR - Log solo al inicio y fin
logger.info("Processing {} products", allProducts.size());
for (Product product : allProducts) {
    // ... sin logs
}
logger.info("Finished processing {} products", allProducts.size());

// ✅ O log cada N iteraciones
int count = 0;
for (Product product : allProducts) {
    count++;
    if (count % 10000 == 0) {
        logger.debug("Progress: {} products processed", count);
    }
    // ...
}

// ═══════════════════════════════════════════════════════
// ❌ NO LOGUEAR: Stack traces de excepciones esperadas
// ═══════════════════════════════════════════════════════

// ❌ Stack trace innecesario
try {
    User user = userRepository.findById(userId)
        .orElseThrow(() -> new UserNotFoundException(userId));
} catch (UserNotFoundException e) {
    logger.error("User not found: {}", userId, e);  // ❌ No necesitas stack
    throw e;
}

// ✅ MEJOR
try {
    User user = userRepository.findById(userId)
        .orElseThrow(() -> new UserNotFoundException(userId));
} catch (UserNotFoundException e) {
    logger.warn("User not found: userId={}", userId);  // Solo el mensaje
    throw e;
}

// ═══════════════════════════════════════════════════════
// ❌ NO LOGUEAR: Información duplicada en múltiples niveles
// ═══════════════════════════════════════════════════════

// ❌ Logging en cada capa (duplicado)
// Controller
logger.info("Creating order for user {}", userId);
orderService.createOrder(request);

// Service
logger.info("Creating order for user {}", userId);  // ❌ Duplicado
Order order = new Order();
orderRepository.save(order);

// Repository
logger.info("Saving order for user {}", userId);  // ❌ Duplicado

// ✅ MEJOR - Log solo en el boundary (Controller o Service)
// Controller
logger.info("Request: POST /orders userId={}, itemsCount={}",
            userId, request.getItems().size());
orderService.createOrder(request);

// Service - Sin log de entrada, solo de resultado
Order order = new Order();
orderRepository.save(order);
logger.info("Order created: orderId={}, total=${}",
            order.getId(), order.getTotal());

// Repository - Sin logs (capa de infraestructura)

// ═══════════════════════════════════════════════════════
// ❌ NO LOGUEAR: toString() de objetos grandes
// ═══════════════════════════════════════════════════════

// ❌ Puede generar MB de logs por un solo log entry
logger.debug("Processing order: {}", order);  // order.toString() = 50KB

// ✅ MEJOR - Solo campos relevantes
logger.debug("Processing order: id={}, itemsCount={}, total=${}",
            order.getId(), order.getItems().size(), order.getTotal());
```

### 5.3 Logging con Contexto (MDC - Mapped Diagnostic Context)

```java
// ═══════════════════════════════════════════════════════
// MDC: Contexto propagado a TODOS los logs del thread
// ═══════════════════════════════════════════════════════

import org.slf4j.MDC;

@RestController
public class OrderController {

    @PostMapping("/orders")
    public ResponseEntity<Order> createOrder(@RequestBody CreateOrderRequest request) {
        // Agregar contexto al inicio del request
        MDC.put("userId", request.getUserId());
        MDC.put("requestId", UUID.randomUUID().toString());
        MDC.put("ipAddress", getClientIp());

        try {
            Order order = orderService.createOrder(request);

            // Agregar más contexto cuando esté disponible
            MDC.put("orderId", order.getId().toString());

            return ResponseEntity.ok(order);
        } finally {
            // IMPORTANTE: Limpiar MDC al final
            MDC.clear();
        }
    }
}

// Ahora TODOS los logs en este request incluirán ese contexto
// Sin tener que pasarlo manualmente

@Service
public class OrderService {

    public Order createOrder(CreateOrderRequest request) {
        // Log automáticamente incluye userId, requestId, ipAddress del MDC
        logger.info("Creating order");
        // → 2024-12-24 14:32:15 [userId=USR-123, requestId=abc-def, ipAddress=192.168.1.1] INFO Creating order

        Order order = new Order();
        orderRepository.save(order);

        logger.info("Order created successfully");
        // → 2024-12-24 14:32:16 [userId=USR-123, requestId=abc-def, orderId=ORD-456] INFO Order created successfully

        return order;
    }
}

// ═══════════════════════════════════════════════════════
// Configuración de Logback para mostrar MDC
// ═══════════════════════════════════════════════════════

<configuration>
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{ISO8601} [%X{requestId}] [%X{userId}] %-5level %c{1} - %msg%n</pattern>
            <!--            ↑ %X{key} muestra valor del MDC          -->
        </encoder>
    </appender>
</configuration>

// ═══════════════════════════════════════════════════════
// MDC Filter para aplicar automáticamente a todos los requests
// ═══════════════════════════════════════════════════════

@Component
@Order(Ordered.HIGHEST_PRECEDENCE)
public class MDCFilter implements Filter {

    @Override
    public void doFilter(ServletRequest request, ServletResponse response,
                        FilterChain chain) throws IOException, ServletException {

        HttpServletRequest httpRequest = (HttpServletRequest) request;

        try {
            // Agregar contexto común a TODOS los requests
            MDC.put("requestId", UUID.randomUUID().toString());
            MDC.put("ipAddress", httpRequest.getRemoteAddr());
            MDC.put("method", httpRequest.getMethod());
            MDC.put("uri", httpRequest.getRequestURI());

            // Si hay usuario autenticado, agregarlo
            Authentication auth = SecurityContextHolder.getContext().getAuthentication();
            if (auth != null && auth.isAuthenticated()) {
                MDC.put("userId", auth.getName());
            }

            chain.doFilter(request, response);

        } finally {
            MDC.clear();  // Limpiar después del request
        }
    }
}

// ═══════════════════════════════════════════════════════
// MDC en aplicaciones asíncronas
// ═══════════════════════════════════════════════════════

@Service
public class AsyncOrderService {

    @Async
    public CompletableFuture<Order> processOrderAsync(CreateOrderRequest request) {
        // PROBLEMA: MDC no se propaga automáticamente a threads asíncronos

        // SOLUCIÓN: Capturar MDC del thread principal
        Map<String, String> mdcContext = MDC.getCopyOfContextMap();

        return CompletableFuture.supplyAsync(() -> {
            try {
                // Restaurar MDC en el nuevo thread
                if (mdcContext != null) {
                    MDC.setContextMap(mdcContext);
                }

                logger.info("Processing order asynchronously");
                // ...

            } finally {
                MDC.clear();
            }
        });
    }
}

// ═══════════════════════════════════════════════════════
// MDC con Spring Cloud Sleuth (auto-configuración)
// ═══════════════════════════════════════════════════════

<!-- pom.xml -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>

// Sleuth automáticamente agrega traceId y spanId al MDC
// No necesitas código adicional

logger.info("Processing order");
// → 2024-12-24 14:32:15 [traceId=abc123, spanId=def456] INFO Processing order
```

### 5.4 Formato de Mensajes: Buenas Prácticas

```java
// ═══════════════════════════════════════════════════════
// ✅ USAR: Placeholders de SLF4J (eficiente)
// ═══════════════════════════════════════════════════════

// ✅ CORRECTO - Solo construye string si el nivel está habilitado
logger.debug("User {} ordered {} items", userId, itemCount);

// ❌ INCORRECTO - SIEMPRE construye string, incluso si DEBUG está OFF
logger.debug("User " + userId + " ordered " + itemCount + " items");

// Performance:
// Con concatenación: ~100ns por log (incluso si DEBUG está OFF)
// Con placeholders: ~10ns si nivel está OFF, ~100ns si está ON

// ═══════════════════════════════════════════════════════
// ✅ USAR: Mensajes descriptivos
// ═══════════════════════════════════════════════════════

// ❌ Mensaje vago
logger.error("Error in payment");

// ✅ Mensaje descriptivo
logger.error("Payment gateway declined transaction: orderId={}, " +
            "amount=${}, reason={}, gateway={}",
            orderId, amount, declineReason, gatewayName);

// ═══════════════════════════════════════════════════════
// ✅ USAR: Formato consistente
// ═══════════════════════════════════════════════════════

// ❌ Inconsistente
logger.info("Order created: " + orderId);
logger.info("User=" + userId + " logged in");
logger.info("Payment processed, amount: $" + amount);

// ✅ Consistente (key=value)
logger.info("Order created: orderId={}", orderId);
logger.info("User logged in: userId={}", userId);
logger.info("Payment processed: amount=${}", amount);

// ═══════════════════════════════════════════════════════
// ✅ USAR: Checked logging con isEnabled (para operaciones costosas)
// ═══════════════════════════════════════════════════════

// ❌ Si DEBUG está OFF, aún ejecuta toJson() (costoso)
logger.debug("Request payload: {}", toJson(request));

// ✅ Solo ejecuta toJson() si DEBUG está ON
if (logger.isDebugEnabled()) {
    logger.debug("Request payload: {}", toJson(request));
}

// ✅ Otro ejemplo
if (logger.isTraceEnabled()) {
    logger.trace("Full object state: {}", serializeToJson(complexObject));
}

// ═══════════════════════════════════════════════════════
// ✅ USAR: Excepciones al final
// ═══════════════════════════════════════════════════════

// ❌ Excepción como placeholder (solo muestra e.toString())
logger.error("Payment failed: {}", e);

// ✅ Excepción como último parámetro (muestra stack trace completo)
logger.error("Payment failed: orderId={}, error={}", orderId, e.getMessage(), e);
//                                                                            ↑
//                                                    Último parámetro = exception

// ═══════════════════════════════════════════════════════
// ✅ USAR: Verbos en presente o pasado según contexto
// ═══════════════════════════════════════════════════════

// Inicio de operación - presente continuo
logger.info("Processing payment for order {}", orderId);

// Fin de operación - pasado
logger.info("Payment processed successfully for order {}", orderId);

// Error - pasado
logger.error("Failed to process payment for order {}", orderId);
```

---

## 6. Logging Estructurado

### 6.1 ¿Qué es Logging Estructurado?

```
LOGGING TRADICIONAL (Plain Text):
2024-12-24 14:32:15 INFO User john@example.com logged in from 192.168.1.1

Problemas:
❌ Parsear requiere regex complejas
❌ Formato inconsistente
❌ Difícil de buscar y filtrar
❌ No se puede agregar fácilmente

LOGGING ESTRUCTURADO (JSON):
{
  "timestamp": "2024-12-24T14:32:15.123Z",
  "level": "INFO",
  "message": "User logged in",
  "email": "john@example.com",
  "ipAddress": "192.168.1.1"
}

Ventajas:
✅ Fácil de parsear (JSON native)
✅ Formato consistente y estándar
✅ Fácil de buscar: email="john@example.com"
✅ Agregaciones: COUNT(DISTINCT email)
```

### 6.2 Logging Estructurado en Java (Logback con Logstash Encoder)

```xml
<!-- pom.xml -->
<dependency>
    <groupId>net.logstash.logback</groupId>
    <artifactId>logstash-logback-encoder</artifactId>
    <version>7.4</version>
</dependency>
```

```xml
<!-- logback-spring.xml -->
<configuration>
    <appender name="JSON_FILE" class="ch.qos.logback.core.FileAppender">
        <file>logs/app.json</file>
        <encoder class="net.logstash.logback.encoder.LogstashEncoder">
            <!-- Campos estándar automáticos -->
            <includeCallerData>true</includeCallerData>
            <includeMdc>true</includeMdc>
            <includeContext>true</includeContext>
            <includeStructuredArguments>true</includeStructuredArguments>

            <!-- Campos custom globales -->
            <customFields>{"application":"my-app","environment":"production"}</customFields>
        </encoder>
    </appender>

    <root level="INFO">
        <appender-ref ref="JSON_FILE"/>
    </root>
</configuration>
```

```java
import static net.logstash.logback.argument.StructuredArguments.*;

@Service
public class OrderService {

    private static final Logger logger = LoggerFactory.getLogger(OrderService.class);

    public Order createOrder(CreateOrderRequest request) {
        // Logging estructurado con campos typed
        logger.info("Creating order",
            keyValue("userId", request.getUserId()),
            keyValue("itemsCount", request.getItems().size()),
            keyValue("currency", request.getCurrency())
        );

        Order order = orderRepository.save(new Order(request));

        // Log con objeto completo
        logger.info("Order created",
            keyValue("orderId", order.getId()),
            keyValue("total", order.getTotal()),
            keyValue("status", order.getStatus()),
            value("order", order)  // Objeto serializado como JSON
        );

        return order;
    }

    public void processPayment(Order order) {
        long startTime = System.currentTimeMillis();

        try {
            PaymentResult result = paymentGateway.charge(order);
            long duration = System.currentTimeMillis() - startTime;

            logger.info("Payment processed",
                keyValue("orderId", order.getId()),
                keyValue("amount", order.getTotal()),
                keyValue("transactionId", result.getTransactionId()),
                keyValue("gateway", "stripe"),
                keyValue("durationMs", duration),
                keyValue("success", true)
            );

        } catch (PaymentException e) {
            long duration = System.currentTimeMillis() - startTime;

            logger.error("Payment failed",
                keyValue("orderId", order.getId()),
                keyValue("amount", order.getTotal()),
                keyValue("durationMs", duration),
                keyValue("errorCode", e.getErrorCode()),
                keyValue("errorMessage", e.getMessage()),
                keyValue("success", false),
                e
            );
            throw e;
        }
    }
}
```

**Resultado en logs/app.json:**

```json
{
  "@timestamp": "2024-12-24T14:32:15.123Z",
  "level": "INFO",
  "thread_name": "http-nio-8080-exec-1",
  "logger_name": "com.company.OrderService",
  "message": "Creating order",
  "userId": "USR-123",
  "itemsCount": 5,
  "currency": "USD",
  "application": "my-app",
  "environment": "production",
  "stack_trace": null
}

{
  "@timestamp": "2024-12-24T14:32:16.456Z",
  "level": "INFO",
  "logger_name": "com.company.OrderService",
  "message": "Order created",
  "orderId": "ORD-789",
  "total": 150.50,
  "status": "PENDING",
  "order": {
    "id": "ORD-789",
    "userId": "USR-123",
    "total": 150.50,
    "status": "PENDING",
    "items": [...]
  },
  "application": "my-app",
  "environment": "production"
}

{
  "@timestamp": "2024-12-24T14:32:17.789Z",
  "level": "ERROR",
  "logger_name": "com.company.OrderService",
  "message": "Payment failed",
  "orderId": "ORD-789",
  "amount": 150.50,
  "durationMs": 1200,
  "errorCode": "INSUFFICIENT_FUNDS",
  "errorMessage": "Card declined",
  "success": false,
  "stack_trace": "com.company.PaymentException: Card declined\n\tat com.company...",
  "application": "my-app",
  "environment": "production"
}
```

### 6.3 Logging Estructurado en JavaScript/Node.js (Winston)

```javascript
// logger.js
const winston = require('winston');

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: winston.format.combine(
    winston.format.timestamp({ format: 'YYYY-MM-DDTHH:mm:ss.SSSZ' }),
    winston.format.errors({ stack: true }),
    winston.format.json()
  ),
  defaultMeta: {
    application: 'my-app',
    environment: process.env.NODE_ENV,
    version: process.env.APP_VERSION
  },
  transports: [
    // File transport
    new winston.transports.File({
      filename: 'logs/error.json',
      level: 'error'
    }),
    new winston.transports.File({
      filename: 'logs/combined.json'
    })
  ]
});

// En desarrollo, también log a consola en formato legible
if (process.env.NODE_ENV !== 'production') {
  logger.add(new winston.transports.Console({
    format: winston.format.combine(
      winston.format.colorize(),
      winston.format.simple()
    )
  }));
}

module.exports = logger;
```

```javascript
// orderService.js
const logger = require('./logger');

class OrderService {
  async createOrder(request) {
    logger.info('Creating order', {
      userId: request.userId,
      itemsCount: request.items.length,
      currency: request.currency
    });

    const order = await this.orderRepository.save({
      userId: request.userId,
      items: request.items,
      total: this.calculateTotal(request.items)
    });

    logger.info('Order created', {
      orderId: order.id,
      userId: order.userId,
      total: order.total,
      status: order.status
    });

    return order;
  }

  async processPayment(order) {
    const startTime = Date.now();

    try {
      logger.info('Processing payment', {
        orderId: order.id,
        amount: order.total,
        gateway: 'stripe'
      });

      const result = await this.paymentGateway.charge(order);
      const duration = Date.now() - startTime;

      logger.info('Payment processed', {
        orderId: order.id,
        transactionId: result.transactionId,
        amount: order.total,
        durationMs: duration,
        success: true
      });

      return result;

    } catch (error) {
      const duration = Date.now() - startTime;

      logger.error('Payment failed', {
        orderId: order.id,
        amount: order.total,
        durationMs: duration,
        errorCode: error.code,
        errorMessage: error.message,
        success: false,
        error: error  // Winston automáticamente extrae stack trace
      });

      throw error;
    }
  }
}
```

**Resultado en logs/combined.json:**

```json
{"timestamp":"2024-12-24T14:32:15.123Z","level":"info","message":"Creating order","userId":"USR-123","itemsCount":5,"currency":"USD","application":"my-app","environment":"production","version":"1.2.3"}

{"timestamp":"2024-12-24T14:32:16.456Z","level":"info","message":"Order created","orderId":"ORD-789","userId":"USR-123","total":150.50,"status":"PENDING","application":"my-app","environment":"production","version":"1.2.3"}

{"timestamp":"2024-12-24T14:32:17.789Z","level":"error","message":"Payment failed","orderId":"ORD-789","amount":150.50,"durationMs":1200,"errorCode":"INSUFFICIENT_FUNDS","errorMessage":"Card declined","success":false,"stack":"Error: Card declined\n    at PaymentGateway.charge (...)","application":"my-app","environment":"production","version":"1.2.3"}
```

### 6.4 Logging Estructurado en C# (.NET con Serilog)

```csharp
// Program.cs
using Serilog;
using Serilog.Formatting.Compact;

Log.Logger = new LoggerConfiguration()
    .MinimumLevel.Information()
    .Enrich.WithProperty("Application", "my-app")
    .Enrich.WithProperty("Environment", Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"))
    .Enrich.FromLogContext()
    .WriteTo.Console()
    .WriteTo.File(
        formatter: new CompactJsonFormatter(),
        path: "logs/app.json",
        rollingInterval: RollingInterval.Day
    )
    .CreateLogger();

var builder = WebApplication.CreateBuilder(args);
builder.Host.UseSerilog();
```

```csharp
// OrderService.cs
using Serilog;

public class OrderService
{
    private readonly ILogger _logger = Log.ForContext<OrderService>();

    public async Task<Order> CreateOrderAsync(CreateOrderRequest request)
    {
        _logger.Information("Creating order for user {UserId} with {ItemsCount} items",
            request.UserId, request.Items.Count);

        var order = new Order
        {
            UserId = request.UserId,
            Items = request.Items,
            Total = CalculateTotal(request.Items)
        };

        await _orderRepository.SaveAsync(order);

        _logger.Information("Order created: {OrderId}, Total: {Total}, Status: {Status}",
            order.Id, order.Total, order.Status);

        return order;
    }

    public async Task<PaymentResult> ProcessPaymentAsync(Order order)
    {
        var stopwatch = Stopwatch.StartNew();

        try
        {
            _logger.Information("Processing payment for order {OrderId}, Amount: {Amount}",
                order.Id, order.Total);

            var result = await _paymentGateway.ChargeAsync(order);
            stopwatch.Stop();

            _logger.Information("Payment processed successfully: " +
                "OrderId: {OrderId}, TransactionId: {TransactionId}, " +
                "Amount: {Amount}, Duration: {DurationMs}ms, Success: {Success}",
                order.Id, result.TransactionId, order.Total,
                stopwatch.ElapsedMilliseconds, true);

            return result;
        }
        catch (PaymentException ex)
        {
            stopwatch.Stop();

            _logger.Error(ex, "Payment failed: " +
                "OrderId: {OrderId}, Amount: {Amount}, " +
                "Duration: {DurationMs}ms, ErrorCode: {ErrorCode}, Success: {Success}",
                order.Id, order.Total, stopwatch.ElapsedMilliseconds,
                ex.ErrorCode, false);

            throw;
        }
    }
}
```

---

## 7. Frameworks y Herramientas

### 7.1 Java: SLF4J + Logback

```java
// ═══════════════════════════════════════════════════════
// DEPENDENCIAS (Maven)
// ═══════════════════════════════════════════════════════

<dependencies>
    <!-- SLF4J API (facade) -->
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-api</artifactId>
        <version>2.0.9</version>
    </dependency>

    <!-- Logback (implementation) -->
    <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
        <version>1.4.14</version>
    </dependency>

    <!-- Para logging estructurado (JSON) -->
    <dependency>
        <groupId>net.logstash.logback</groupId>
        <artifactId>logstash-logback-encoder</artifactId>
        <version>7.4</version>
    </dependency>
</dependencies>

// ═══════════════════════════════════════════════════════
// USO EN CÓDIGO
// ═══════════════════════════════════════════════════════

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class OrderService {
    // Logger nombrado según la clase
    private static final Logger logger = LoggerFactory.getLogger(OrderService.class);

    public void processOrder(Long orderId) {
        logger.info("Processing order {}", orderId);
        // ...
    }
}
```

```xml
<!-- logback-spring.xml -->
<configuration>
    <!-- Variables -->
    <property name="LOG_PATH" value="logs"/>
    <property name="LOG_PATTERN" value="%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n"/>

    <!-- Appender: Consola -->
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>${LOG_PATTERN}</pattern>
        </encoder>
    </appender>

    <!-- Appender: Archivo con rotación -->
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${LOG_PATH}/app.log</file>
        <encoder>
            <pattern>${LOG_PATTERN}</pattern>
        </encoder>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- Rotación diaria -->
            <fileNamePattern>${LOG_PATH}/app.%d{yyyy-MM-dd}.log</fileNamePattern>
            <!-- Mantener 30 días -->
            <maxHistory>30</maxHistory>
            <!-- Máximo 10GB total -->
            <totalSizeCap>10GB</totalSizeCap>
        </rollingPolicy>
    </appender>

    <!-- Appender: Archivo JSON -->
    <appender name="JSON_FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${LOG_PATH}/app.json</file>
        <encoder class="net.logstash.logback.encoder.LogstashEncoder">
            <includeMdc>true</includeMdc>
            <includeStructuredArguments>true</includeStructuredArguments>
        </encoder>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${LOG_PATH}/app.%d{yyyy-MM-dd}.json</fileNamePattern>
            <maxHistory>30</maxHistory>
        </rollingPolicy>
    </appender>

    <!-- Appender: Async (wrapper) -->
    <appender name="ASYNC_FILE" class="ch.qos.logback.classic.AsyncAppender">
        <queueSize>512</queueSize>
        <discardingThreshold>0</discardingThreshold>
        <appender-ref ref="FILE"/>
    </appender>

    <!-- Loggers específicos -->
    <logger name="com.company.payment" level="DEBUG"/>
    <logger name="org.springframework" level="WARN"/>
    <logger name="org.hibernate" level="WARN"/>

    <!-- Root logger -->
    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="ASYNC_FILE"/>
        <appender-ref ref="JSON_FILE"/>
    </root>
</configuration>
```

### 7.2 JavaScript/Node.js: Winston

```javascript
// logger.js
const winston = require('winston');
const path = require('path');

// Custom format para desarrollo
const devFormat = winston.format.combine(
  winston.format.colorize(),
  winston.format.timestamp({ format: 'YYYY-MM-DD HH:mm:ss' }),
  winston.format.printf(info => {
    const { timestamp, level, message, ...meta } = info;
    return `${timestamp} ${level}: ${message} ${
      Object.keys(meta).length ? JSON.stringify(meta, null, 2) : ''
    }`;
  })
);

// JSON format para producción
const prodFormat = winston.format.combine(
  winston.format.timestamp({ format: 'YYYY-MM-DDTHH:mm:ss.SSSZ' }),
  winston.format.errors({ stack: true }),
  winston.format.json()
);

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: process.env.NODE_ENV === 'production' ? prodFormat : devFormat,
  defaultMeta: {
    service: 'my-service',
    environment: process.env.NODE_ENV,
    version: process.env.npm_package_version
  },
  transports: [
    // Error log
    new winston.transports.File({
      filename: path.join('logs', 'error.log'),
      level: 'error',
      maxsize: 10485760, // 10MB
      maxFiles: 5
    }),
    // Combined log
    new winston.transports.File({
      filename: path.join('logs', 'combined.log'),
      maxsize: 10485760, // 10MB
      maxFiles: 5
    })
  ],
  // Handle exceptions
  exceptionHandlers: [
    new winston.transports.File({
      filename: path.join('logs', 'exceptions.log')
    })
  ],
  // Handle promise rejections
  rejectionHandlers: [
    new winston.transports.File({
      filename: path.join('logs', 'rejections.log')
    })
  ]
});

// Console en desarrollo
if (process.env.NODE_ENV !== 'production') {
  logger.add(new winston.transports.Console());
}

module.exports = logger;
```

```javascript
// Uso en la aplicación
const logger = require('./logger');

// Express middleware para logging de requests
const requestLogger = (req, res, next) => {
  const startTime = Date.now();

  res.on('finish', () => {
    const duration = Date.now() - startTime;

    logger.info('HTTP Request', {
      method: req.method,
      url: req.url,
      statusCode: res.statusCode,
      durationMs: duration,
      ip: req.ip,
      userAgent: req.get('user-agent')
    });
  });

  next();
};

app.use(requestLogger);

// Logging en servicios
class OrderService {
  async createOrder(data) {
    logger.info('Creating order', {
      userId: data.userId,
      itemsCount: data.items.length
    });

    try {
      const order = await this.repository.save(data);

      logger.info('Order created successfully', {
        orderId: order.id,
        total: order.total
      });

      return order;
    } catch (error) {
      logger.error('Failed to create order', {
        userId: data.userId,
        error: error.message,
        stack: error.stack
      });
      throw error;
    }
  }
}
```

### 7.3 Python: Logging Module (stdlib)

```python
# logger.py
import logging
import logging.handlers
import json
from datetime import datetime

class JSONFormatter(logging.Formatter):
    """Custom formatter para output JSON"""

    def format(self, record):
        log_data = {
            'timestamp': datetime.utcnow().isoformat() + 'Z',
            'level': record.levelname,
            'logger': record.name,
            'message': record.getMessage(),
            'module': record.module,
            'function': record.funcName,
            'line': record.lineno
        }

        # Agregar campos extra
        if hasattr(record, 'user_id'):
            log_data['user_id'] = record.user_id
        if hasattr(record, 'request_id'):
            log_data['request_id'] = record.request_id

        # Agregar exception info si existe
        if record.exc_info:
            log_data['exception'] = self.formatException(record.exc_info)

        return json.dumps(log_data)

def setup_logging(log_level='INFO'):
    """Configurar logging para la aplicación"""

    # Root logger
    logger = logging.getLogger()
    logger.setLevel(log_level)

    # Console handler (texto legible)
    console_handler = logging.StreamHandler()
    console_handler.setLevel(logging.DEBUG)
    console_formatter = logging.Formatter(
        '%(asctime)s - %(name)s - %(levelname)s - %(message)s'
    )
    console_handler.setFormatter(console_formatter)
    logger.addHandler(console_handler)

    # File handler (JSON)
    file_handler = logging.handlers.RotatingFileHandler(
        'logs/app.json',
        maxBytes=10485760,  # 10MB
        backupCount=5
    )
    file_handler.setLevel(logging.INFO)
    file_handler.setFormatter(JSONFormatter())
    logger.addHandler(file_handler)

    # Error file handler
    error_handler = logging.handlers.RotatingFileHandler(
        'logs/error.log',
        maxBytes=10485760,
        backupCount=5
    )
    error_handler.setLevel(logging.ERROR)
    error_handler.setFormatter(console_formatter)
    logger.addHandler(error_handler)

    return logger

# Inicializar logger
logger = setup_logging()
```

```python
# order_service.py
import logging
from time import time

logger = logging.getLogger(__name__)

class OrderService:
    def create_order(self, user_id, items):
        logger.info('Creating order', extra={
            'user_id': user_id,
            'items_count': len(items)
        })

        try:
            order = self.repository.save({
                'user_id': user_id,
                'items': items,
                'total': self._calculate_total(items)
            })

            logger.info('Order created successfully', extra={
                'order_id': order['id'],
                'user_id': user_id,
                'total': order['total']
            })

            return order

        except Exception as e:
            logger.error(f'Failed to create order: {str(e)}', extra={
                'user_id': user_id
            }, exc_info=True)
            raise

    def process_payment(self, order):
        start_time = time()

        try:
            logger.info('Processing payment', extra={
                'order_id': order['id'],
                'amount': order['total']
            })

            result = self.payment_gateway.charge(order)
            duration_ms = (time() - start_time) * 1000

            logger.info('Payment processed', extra={
                'order_id': order['id'],
                'transaction_id': result['transaction_id'],
                'duration_ms': duration_ms,
                'success': True
            })

            return result

        except PaymentException as e:
            duration_ms = (time() - start_time) * 1000

            logger.error('Payment failed', extra={
                'order_id': order['id'],
                'amount': order['total'],
                'duration_ms': duration_ms,
                'error_code': e.code,
                'success': False
            }, exc_info=True)

            raise
```

---

## 8. Logging en Producción

### 8.1 Agregación de Logs: ELK Stack

```
┌─────────────────────────────────────────────────────────┐
│  APLICACIÓN                                             │
│  ├── app-server-01 → logs/app.json                     │
│  ├── app-server-02 → logs/app.json                     │
│  └── app-server-03 → logs/app.json                     │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│  FILEBEAT (Log Shipper)                                 │
│  ├── Lee archivos JSON                                  │
│  ├── Agrega metadata (hostname, tags)                   │
│  └── Envía a Logstash                                   │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│  LOGSTASH (Procesamiento)                               │
│  ├── Parsea JSON                                        │
│  ├── Filtra logs                                        │
│  ├── Enriquece con datos adicionales                    │
│  └── Indexa en Elasticsearch                            │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│  ELASTICSEARCH (Storage + Search)                       │
│  ├── Almacena logs indexados                            │
│  ├── Provee búsqueda full-text                          │
│  └── Agregaciones y análisis                            │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│  KIBANA (Visualización)                                 │
│  ├── Dashboards                                         │
│  ├── Búsqueda interactiva                               │
│  ├── Alertas                                             │
│  └── Análisis                                            │
└─────────────────────────────────────────────────────────┘
```

**Configuración de Filebeat:**

```yaml
# filebeat.yml
filebeat.inputs:
  - type: log
    enabled: true
    paths:
      - /var/log/myapp/app.json
    json.keys_under_root: true
    json.add_error_key: true
    fields:
      application: my-app
      environment: production

output.logstash:
  hosts: ["logstash:5044"]

processors:
  - add_host_metadata: ~
  - add_cloud_metadata: ~
```

**Configuración de Logstash:**

```ruby
# logstash.conf
input {
  beats {
    port => 5044
  }
}

filter {
  # Parsear timestamp
  date {
    match => ["timestamp", "ISO8601"]
    target => "@timestamp"
  }

  # Agregar geolocalización de IP
  geoip {
    source => "ipAddress"
    target => "geo"
  }

  # Parsear user agent
  useragent {
    source => "userAgent"
    target => "user_agent"
  }
}

output {
  elasticsearch {
    hosts => ["elasticsearch:9200"]
    index => "logs-%{[application]}-%{+YYYY.MM.dd}"
  }
}
```

### 8.2 Búsquedas en Kibana

```
Ejemplos de queries en Kibana:

# Buscar logs de un usuario específico
userId:"USR-123"

# Buscar errores en las últimas 24 horas
level:ERROR AND @timestamp:[now-24h TO now]

# Buscar pagos fallidos
message:"Payment failed" AND success:false

# Buscar requests lentos (>3 segundos)
durationMs:>3000

# Combinar condiciones
level:ERROR AND userId:"USR-123" AND @timestamp:[now-1h TO now]

# Buscar por patrón en mensaje
message:/timeout|connection.*failed/

# Excluir logs específicos
NOT logger:"org.springframework"
```

### 8.3 Dashboards en Kibana

```
Dashboard típico para monitoreo:

┌────────────────────────────────────────────────────────┐
│  ERROR RATE (last 24h)                                 │
│  [Gráfico de línea mostrando errors/hora]             │
└────────────────────────────────────────────────────────┘

┌────────────────────────────────────────────────────────┐
│  TOP ERRORS                                             │
│  1. PaymentGatewayException (245 occurrences)          │
│  2. DatabaseTimeoutException (87 occurrences)          │
│  3. NullPointerException (12 occurrences)              │
└────────────────────────────────────────────────────────┘

┌────────────────────────────────────────────────────────┐
│  RESPONSE TIME P95                                      │
│  [Gráfico showing percentil 95 response time]          │
└────────────────────────────────────────────────────────┘

┌────────────────────────────────────────────────────────┐
│  REQUESTS BY ENDPOINT                                   │
│  /api/orders - 12,345 req/hour                         │
│  /api/users - 8,901 req/hour                           │
│  /api/products - 5,432 req/hour                        │
└────────────────────────────────────────────────────────┘

┌────────────────────────────────────────────────────────┐
│  GEOGRAPHIC DISTRIBUTION                                │
│  [Mapa mostrando requests por país]                    │
└────────────────────────────────────────────────────────┘
```

### 8.4 Alertas

```yaml
# Elasticsearch Watcher - Alerta de error rate alto
PUT _watcher/watch/high_error_rate
{
  "trigger": {
    "schedule": {
      "interval": "5m"
    }
  },
  "input": {
    "search": {
      "request": {
        "indices": ["logs-*"],
        "body": {
          "query": {
            "bool": {
              "must": [
                { "term": { "level": "ERROR" }},
                { "range": { "@timestamp": { "gte": "now-5m" }}}
              ]
            }
          }
        }
      }
    }
  },
  "condition": {
    "compare": {
      "ctx.payload.hits.total": {
        "gt": 10
      }
    }
  },
  "actions": {
    "send_email": {
      "email": {
        "to": "oncall@company.com",
        "subject": "High Error Rate Alert",
        "body": "More than 10 errors in the last 5 minutes"
      }
    },
    "send_slack": {
      "webhook": {
        "url": "https://hooks.slack.com/...",
        "body": "{\"text\": \"🚨 High error rate detected!\"}"
      }
    }
  }
}
```

### 8.5 Retención y Rotación de Logs

```
ESTRATEGIA DE RETENCIÓN:

Hot Tier (0-7 días):
├── Storage: SSD rápido
├── Índices: Totalmente indexados
├── Uso: Búsquedas frecuentes, dashboards
└── Costo: Alto

Warm Tier (7-30 días):
├── Storage: SSD estándar
├── Índices: Read-only
├── Uso: Búsquedas ocasionales
└── Costo: Medio

Cold Tier (30-90 días):
├── Storage: HDD
├── Índices: Comprimidos
├── Uso: Compliance, auditoría
└── Costo: Bajo

Frozen/Archive (90-365 días):
├── Storage: S3/Object Storage
├── Índices: Snapshots
├── Uso: Requisitos legales
└── Costo: Muy bajo

Delete (>365 días):
└── Eliminar según política de retención
```

**Configuración en Elasticsearch Index Lifecycle Management (ILM):**

```json
PUT _ilm/policy/logs_policy
{
  "policy": {
    "phases": {
      "hot": {
        "actions": {
          "rollover": {
            "max_age": "1d",
            "max_size": "50GB"
          }
        }
      },
      "warm": {
        "min_age": "7d",
        "actions": {
          "readonly": {},
          "forcemerge": {
            "max_num_segments": 1
          },
          "shrink": {
            "number_of_shards": 1
          }
        }
      },
      "cold": {
        "min_age": "30d",
        "actions": {
          "freeze": {}
        }
      },
      "delete": {
        "min_age": "90d",
        "actions": {
          "delete": {}
        }
      }
    }
  }
}
```

---

## 9. Logging Distribuido

### 9.1 Trace ID y Correlation

```
PROBLEMA: En microservicios, un request pasa por múltiples servicios

User Request
    │
    ▼
┌────────────────┐
│ API Gateway    │ → traceId: abc-123
└────────┬───────┘
         │
         ▼
┌────────────────┐
│ Order Service  │ → traceId: abc-123, spanId: span-001
└────────┬───────┘
         │
         ├──────────────────────┐
         ▼                      ▼
┌────────────────┐    ┌─────────────────┐
│ Payment Service│    │ Inventory Svc   │
│ traceId:abc-123│    │ traceId:abc-123 │
│ spanId:span-002│    │ spanId:span-003 │
└────────────────┘    └─────────────────┘

Sin traceId: Imposible correlacionar logs entre servicios
Con traceId: Puedes seguir el request completo a través de todos los servicios
```

### 9.2 Implementación de Trace ID con Spring Cloud Sleuth

```xml
<!-- pom.xml -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
```

```java
// Sleuth AUTOMÁTICAMENTE:
// 1. Genera traceId y spanId para cada request
// 2. Los agrega al MDC (Mapped Diagnostic Context)
// 3. Los propaga en headers HTTP a servicios downstream

@RestController
public class OrderController {

    private static final Logger logger = LoggerFactory.getLogger(OrderController.class);

    @PostMapping("/orders")
    public Order createOrder(@RequestBody CreateOrderRequest request) {
        // Sleuth automáticamente agrega [traceId,spanId] al log
        logger.info("Creating order for user {}", request.getUserId());

        Order order = orderService.createOrder(request);

        logger.info("Order created: {}", order.getId());
        return order;
    }
}

// Logs resultantes:
// 2024-12-24 14:32:15 [order-service,abc123,span001] INFO Creating order for user USR-123
// 2024-12-24 14:32:16 [order-service,abc123,span001] INFO Order created: ORD-789
```

```java
@Service
public class OrderService {

    private static final Logger logger = LoggerFactory.getLogger(OrderService.class);

    @Autowired
    private PaymentServiceClient paymentClient;  // Feign client

    public Order createOrder(CreateOrderRequest request) {
        logger.info("Processing order creation");

        Order order = orderRepository.save(new Order(request));

        // Llamada a Payment Service
        // Sleuth automáticamente propaga traceId en header HTTP
        logger.info("Calling payment service");
        paymentClient.processPayment(order);

        return order;
    }
}

// Payment Service (servicio separado)
@RestController
public class PaymentController {

    private static final Logger logger = LoggerFactory.getLogger(PaymentController.class);

    @PostMapping("/payments")
    public PaymentResult processPayment(@RequestBody PaymentRequest request) {
        // MISMO traceId que Order Service, pero spanId diferente
        logger.info("Processing payment for order {}", request.getOrderId());

        PaymentResult result = paymentService.charge(request);

        logger.info("Payment processed: {}", result.getTransactionId());
        return result;
    }
}

// Logs en Payment Service:
// 2024-12-24 14:32:16 [payment-service,abc123,span002] INFO Processing payment for order ORD-789
// 2024-12-24 14:32:17 [payment-service,abc123,span002] INFO Payment processed: TXN-456
```

**En Kibana, puedes buscar todos los logs de un request:**

```
traceId:"abc123"

Resultado:
[order-service,abc123,span001] INFO Creating order for user USR-123
[order-service,abc123,span001] INFO Calling payment service
[payment-service,abc123,span002] INFO Processing payment for order ORD-789
[payment-service,abc123,span002] INFO Payment processed: TXN-456
[order-service,abc123,span001] INFO Order created: ORD-789
```

### 9.3 Trace ID Manual (Sin Sleuth)

```java
// ═══════════════════════════════════════════════════════
// Filter para generar trace ID
// ═══════════════════════════════════════════════════════

@Component
@Order(Ordered.HIGHEST_PRECEDENCE)
public class TraceIdFilter implements Filter {

    private static final String TRACE_ID_HEADER = "X-Trace-Id";
    private static final String TRACE_ID_MDC_KEY = "traceId";

    @Override
    public void doFilter(ServletRequest request, ServletResponse response,
                        FilterChain chain) throws IOException, ServletException {

        HttpServletRequest httpRequest = (HttpServletRequest) request;

        try {
            // Obtener traceId del header o generar uno nuevo
            String traceId = httpRequest.getHeader(TRACE_ID_HEADER);
            if (traceId == null || traceId.isEmpty()) {
                traceId = UUID.randomUUID().toString();
            }

            // Agregar a MDC (aparecerá en todos los logs)
            MDC.put(TRACE_ID_MDC_KEY, traceId);

            // Agregar a response header
            HttpServletResponse httpResponse = (HttpServletResponse) response;
            httpResponse.setHeader(TRACE_ID_HEADER, traceId);

            chain.doFilter(request, response);

        } finally {
            MDC.remove(TRACE_ID_MDC_KEY);
        }
    }
}

// ═══════════════════════════════════════════════════════
// Propagar trace ID a servicios downstream (RestTemplate)
// ═══════════════════════════════════════════════════════

@Component
public class TraceIdInterceptor implements ClientHttpRequestInterceptor {

    private static final String TRACE_ID_HEADER = "X-Trace-Id";
    private static final String TRACE_ID_MDC_KEY = "traceId";

    @Override
    public ClientHttpResponse intercept(HttpRequest request, byte[] body,
                                       ClientHttpRequestExecution execution)
            throws IOException {

        // Obtener traceId del MDC
        String traceId = MDC.get(TRACE_ID_MDC_KEY);

        if (traceId != null) {
            // Agregar a headers del request saliente
            request.getHeaders().set(TRACE_ID_HEADER, traceId);
        }

        return execution.execute(request, body);
    }
}

@Configuration
public class RestTemplateConfig {

    @Bean
    public RestTemplate restTemplate(TraceIdInterceptor traceIdInterceptor) {
        RestTemplate restTemplate = new RestTemplate();
        restTemplate.setInterceptors(List.of(traceIdInterceptor));
        return restTemplate;
    }
}

// ═══════════════════════════════════════════════════════
// Logback config para mostrar trace ID
// ═══════════════════════════════════════════════════════

<configuration>
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{ISO8601} [%X{traceId}] %-5level %logger{36} - %msg%n</pattern>
            <!--                  ↑ Muestra traceId del MDC                      -->
        </encoder>
    </appender>
</configuration>
```

---

## 10. Anti-Patrones Comunes

### 10.1 ❌ Logging Excesivo

```java
// ❌ ANTI-PATRÓN: Log en cada línea
public void processOrder(Order order) {
    logger.debug("Entering processOrder");
    logger.debug("Order is: {}", order);
    logger.debug("Getting order items");
    List<OrderItem> items = order.getItems();
    logger.debug("Items count: {}", items.size());
    logger.debug("Looping through items");
    for (OrderItem item : items) {
        logger.debug("Processing item: {}", item);
        logger.debug("Item price: {}", item.getPrice());
        // ...
    }
    logger.debug("Exiting processOrder");
}

Problemas:
- Genera MB de logs innecesarios
- Degrada performance
- Dificulta encontrar información relevante
- Costo de storage alto

// ✅ CORRECTO: Log solo información relevante
public void processOrder(Order order) {
    logger.info("Processing order: orderId={}, itemsCount={}",
                order.getId(), order.getItems().size());

    for (OrderItem item : items) {
        processItem(item);
    }

    logger.info("Order processed successfully: orderId={}", order.getId());
}
```

### 10.2 ❌ Concatenación de Strings

```java
// ❌ ANTI-PATRÓN: Concatenación (siempre se ejecuta)
logger.debug("User " + user.getId() + " ordered " + order.getItems().size() + " items");

// Problema: La concatenación se ejecuta INCLUSO si DEBUG está deshabilitado
// Costo: ~100ns por log aunque no se loguee

// ✅ CORRECTO: Placeholders (lazy evaluation)
logger.debug("User {} ordered {} items", user.getId(), order.getItems().size());

// Costo: ~10ns si DEBUG está deshabilitado
```

### 10.3 ❌ Logging de Información Sensible

```java
// ❌ ANTI-PATRÓN: Loguear passwords, tokens, PII
logger.info("User login: username={}, password={}", username, password);
logger.debug("API key: {}", apiKey);
logger.info("Credit card: {}", creditCard.getNumber());
logger.info("User data: {}", user);  // Puede contener SSN, etc.

Problemas:
- GDPR violation (multas hasta €20 millones)
- Security breach (logs suelen tener permisos amplios)
- Compliance violation (PCI-DSS, HIPAA)

// ✅ CORRECTO: Redactar o no loguear
logger.info("User login: username={}", username);  // Sin password
logger.debug("API call authenticated");  // Sin mostrar key
logger.info("Payment method added: last4={}", creditCard.getLast4Digits());
logger.info("User updated: userId={}", user.getId());  // Solo ID, no PII
```

### 10.4 ❌ Excepciones sin Stack Trace

```java
// ❌ ANTI-PATRÓN: Solo mensaje, sin stack trace
try {
    processPayment(order);
} catch (PaymentException e) {
    logger.error("Payment failed: " + e.getMessage());
}

// Problema: No sabes DÓNDE falló ni la causa raíz

// ✅ CORRECTO: Incluir excepción completa
try {
    processPayment(order);
} catch (PaymentException e) {
    logger.error("Payment failed for order {}: {}",
                order.getId(), e.getMessage(), e);
    //                                         ↑ Excepción al final
}

// Ahora el log incluye:
// - Mensaje del error
// - Stack trace completo
// - Caused by chain
// - Línea exacta donde ocurrió
```

### 10.5 ❌ Logging Síncrono en Hot Path

```java
// ❌ ANTI-PATRÓN: Logging síncrono en loop crítico
for (int i = 0; i < 1000000; i++) {
    // Cada log bloquea ~1-5ms
    logger.info("Processing item {}", i);
    processItem(i);
}
// Tiempo total: 1-5 SEGUNDOS solo en logging

// ✅ CORRECTO: Usar async logging o reducir frecuencia
// Opción 1: Async logging
<appender name="ASYNC" class="ch.qos.logback.classic.AsyncAppender">
    <appender-ref ref="FILE"/>
</appender>

// Opción 2: Log solo cada N iteraciones
for (int i = 0; i < 1000000; i++) {
    if (i % 10000 == 0) {
        logger.info("Progress: {} items processed", i);
    }
    processItem(i);
}
```

### 10.6 ❌ No Usar Niveles Correctos

```java
// ❌ ANTI-PATRÓN: Todo en INFO o ERROR
logger.info("User not found");  // Esto es WARN o DEBUG
logger.error("User logged in");  // Esto es INFO

// ✅ CORRECTO: Usar el nivel apropiado
logger.debug("Attempting to fetch user {}", userId);
logger.warn("User {} not found in cache, fetching from DB", userId);
logger.info("User {} logged in successfully", userId);
logger.error("Database connection failed", exception);
```

### 10.7 ❌ Logs sin Contexto

```java
// ❌ ANTI-PATRÓN: Logs sin contexto
logger.error("Payment failed");

// Preguntas sin respuesta:
// - ¿Qué orden?
// - ¿Qué usuario?
// - ¿Por qué falló?
// - ¿Cuándo pasó exactamente?

// ✅ CORRECTO: Logs con contexto completo
logger.error("Payment failed: orderId={}, userId={}, amount=${}, " +
            "gateway={}, errorCode={}, errorMessage={}",
            orderId, userId, amount, gateway,
            errorCode, errorMessage, exception);
```

---

## Resumen de Mejores Prácticas

```
✅ SIEMPRE:
├── Usar logging estructurado (JSON)
├── Incluir trace IDs en sistemas distribuidos
├── Usar placeholders, no concatenación
├── Incluir contexto (userId, orderId, etc.)
├── Usar niveles correctos (ERROR, WARN, INFO, DEBUG)
├── Loguear excepciones completas (con stack trace)
├── Usar async logging en producción
└── Configurar rotación y retención

❌ NUNCA:
├── Loguear passwords, tokens, o PII sin redactar
├── Usar System.out.println() en producción
├── Loguear dentro de loops intensivos
├── Concatenar strings para logs
├── Ignorar excepciones sin loguear
├── Dejar logs de DEBUG en producción
└── Logging excesivo sin valor

🔧 CONFIGURACIÓN TÍPICA:
Desarrollo:
├── Nivel: DEBUG
├── Output: Console (texto legible)
└── Async: OFF (para debugging)

Staging:
├── Nivel: DEBUG (o INFO)
├── Output: File (JSON) + Console
└── Async: ON

Producción:
├── Nivel: INFO
├── Output: File (JSON)
├── Async: ON
├── Agregación: ELK Stack
└── Alertas: Configuradas
```

---

**Última actualización:** Diciembre 2025
**Versión:** 1.0
**Autor:** Illescas, Michael Jonathan
**Licencia:** Uso libre para aprendizaje y aplicación profesional


