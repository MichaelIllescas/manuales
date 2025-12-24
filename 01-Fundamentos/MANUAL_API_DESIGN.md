# Manual Completo de API Design
## Guía Profesional: De Principiante a Experto

> Manual profesional sobre diseño de APIs RESTful, GraphQL y mejores prácticas para crear APIs escalables y mantenibles

---

## 📚 Índice

1. [Introducción](#1-introducción)
2. [El Problema que Resuelve](#2-el-problema-que-resuelve)
3. [REST: Conceptos Fundamentales](#3-rest-conceptos-fundamentales)
4. [Richardson Maturity Model](#4-richardson-maturity-model)
5. [Diseño de Recursos y URLs](#5-diseño-de-recursos-y-urls)
6. [HTTP Methods y Códigos de Estado](#6-http-methods-y-códigos-de-estado)
7. [Versionado de APIs](#7-versionado-de-apis)
8. [Autenticación y Autorización](#8-autenticación-y-autorización)
9. [Paginación, Filtrado y Ordenamiento](#9-paginación-filtrado-y-ordenamiento)
10. [Manejo de Errores](#10-manejo-de-errores)
11. [Documentación con OpenAPI/Swagger](#11-documentación-con-openapiswagger)
12. [Rate Limiting y Throttling](#12-rate-limiting-y-throttling)
13. [GraphQL como Alternativa](#13-graphql-como-alternativa)
14. [Anti-Patrones Comunes](#14-anti-patrones-comunes)

---

## 🎯 Lo que Cubre este Manual

Este manual incluye **todo lo esencial para diseñar APIs profesionales**:

✅ **REST fundamentals** - Principios, recursos, HTTP methods, status codes
✅ **Richardson Maturity Model** - Niveles de madurez RESTful
✅ **Diseño de URLs** - Convenciones, recursos anidados, buenas prácticas
✅ **Versionado** - Estrategias de versionado (URI, Header, Content Negotiation)
✅ **Autenticación** - OAuth2, JWT, API Keys, mejores prácticas de seguridad
✅ **Documentación** - OpenAPI/Swagger con ejemplos completos
✅ **Operaciones avanzadas** - Paginación, filtrado, ordenamiento, búsqueda
✅ **Manejo de errores** - Formato estándar, códigos apropiados
✅ **GraphQL** - Alternativa moderna a REST
✅ **Ejemplos completos** - Java/Spring Boot, JavaScript/Express, Python/FastAPI



## 1. Introducción

### 1.1 ¿Qué es una API?

**API (Application Programming Interface)** es un contrato que define cómo diferentes sistemas de software se comunican entre sí.

```
API = Interfaz de Programación de Aplicaciones

Analogía del Restaurante:
┌─────────────────────────────────────────────────────┐
│  CLIENTE (Frontend)                                 │
│  "Quiero una pizza margarita"                       │
└────────────────────┬────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────┐
│  MENÚ (API Documentation)                           │
│  - Lista de platillos disponibles                   │
│  - Ingredientes, precios, tiempos                   │
└────────────────────┬────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────┐
│  MESERO (API)                                       │
│  - Toma tu orden (Request)                          │
│  - La lleva a la cocina                             │
│  - Te trae la comida (Response)                     │
└────────────────────┬────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────┐
│  COCINA (Backend/Database)                          │
│  - Prepara la orden                                 │
│  - Lógica de negocio                                │
│  - Acceso a datos                                   │
└─────────────────────────────────────────────────────┘

El CLIENTE no necesita saber cómo funciona la COCINA.
Solo necesita conocer el MENÚ y hablar con el MESERO.
```

### 1.2 Tipos de APIs

```
1. REST (Representational State Transfer)
   ✅ Más común y popular
   ✅ Basado en HTTP
   ✅ Stateless
   ✅ Recursos identificados por URLs
   📊 Uso: 70-80% de APIs públicas

2. GraphQL
   ✅ Query language para APIs
   ✅ Cliente pide exactamente lo que necesita
   ✅ Un solo endpoint
   📊 Uso: Creciendo rápidamente (Facebook, GitHub, Shopify)

3. gRPC
   ✅ Remote Procedure Call de Google
   ✅ Usa Protocol Buffers (binario)
   ✅ Alto performance
   📊 Uso: Microservicios internos, IoT

4. SOAP (legacy)
   ❌ XML pesado
   ❌ Complejo
   ❌ Menos usado en proyectos nuevos
   📊 Uso: Sistemas legacy, enterprise antiguo

5. WebSockets
   ✅ Comunicación bidireccional en tiempo real
   ✅ Mantiene conexión abierta
   📊 Uso: Chat, notificaciones en tiempo real, gaming
```

### 1.3 ¿Por Qué el Diseño de APIs es Crítico?

```
DATO: El 80% del tiempo de desarrollo se gasta MANTENIENDO código.
Una API mal diseñada = DOLOR para siempre.

Estadísticas:
- APIs bien diseñadas: 3x más rápido integrar
- APIs mal diseñadas: 5x más bugs reportados
- Costo de cambiar API pública: $50,000 - $500,000
  (breaking changes, migraciones, soporte)

Ejemplos reales:

✅ Stripe API
   - Diseño consistente y predecible
   - Documentación excelente
   - Backward compatibility garantizada
   → Resultado: 1M+ developers, $640B procesados/año

❌ Twitter API v1
   - Inconsistencias en endpoints
   - Rate limiting confuso
   - Breaking changes frecuentes
   → Resultado: Migración forzada a v2, developers enojados
```

---

## 2. El Problema que Resuelve

### 2.1 Problemas de APIs Mal Diseñadas

**Problema 1: URLs Inconsistentes y Confusas**

```bash
# ❌ API mal diseñada - Inconsistente
GET /getUser?id=123              # Verbo en URL
GET /user/delete/456             # DELETE como GET
POST /createNewUserAccount       # Redundante
GET /api/v1/users-list          # Mezcla de convenciones
GET /fetch_all_products         # Snake_case en URL

# Usuario: "¿Cómo borro un usuario?"
# → No se sabe si es DELETE /user/123, POST /deleteUser, o GET /user/delete/123

# ✅ API bien diseñada - RESTful y consistente
GET    /api/v1/users             # Listar usuarios
GET    /api/v1/users/123         # Obtener usuario 123
POST   /api/v1/users             # Crear usuario
PUT    /api/v1/users/123         # Actualizar usuario 123
DELETE /api/v1/users/123         # Eliminar usuario 123
```

**Problema 2: Códigos de Estado HTTP Incorrectos**

```javascript
// ❌ API que SIEMPRE retorna 200, incluso en errores
// POST /api/users
HTTP/1.1 200 OK
{
  "success": false,
  "error": "Email already exists",
  "errorCode": "DUPLICATE_EMAIL"
}

Problemas:
- Clientes HTTP no pueden detectar errores automáticamente
- Monitoring tools marcan como "exitoso" (fue 200)
- Caches pueden guardar errores
- Logs/métricas incorrectos

// ✅ API con códigos de estado correctos
// POST /api/users
HTTP/1.1 409 Conflict
{
  "error": {
    "code": "DUPLICATE_EMAIL",
    "message": "Email already exists",
    "field": "email"
  }
}

Beneficios:
✅ Cliente sabe que hay error (4xx)
✅ Cliente sabe que es conflicto de recursos (409)
✅ Monitoring correcto
✅ Puede implementar retry logic apropiado
```

**Problema 3: Over-fetching y Under-fetching**

```javascript
// ❌ Over-fetching: API retorna TODO, incluso lo que no necesitas
// GET /api/users/123
{
  "id": 123,
  "firstName": "John",
  "lastName": "Doe",
  "email": "john@example.com",
  "password": "hashed...",           // ❌ No debería exponerse
  "creditCard": "4111...",            // ❌ Información sensible
  "orders": [...1000 orders...],      // ❌ Demasiado pesado
  "loginHistory": [...],              // ❌ No lo necesito
  "preferences": {...},
  "avatar": "base64_10MB_image..."    // ❌ Imagen enorme
}

// Problema: El frontend solo quería nombre y email para mostrar en navbar
// Transferidos: 15MB, Necesitados: 50 bytes

// ❌ Under-fetching: Necesitas múltiples llamadas
// Quiero mostrar: Usuario + sus últimos 5 pedidos + cada pedido con productos

GET /api/users/123                    // 1 llamada - obtener usuario
GET /api/users/123/orders             // 2 llamada - obtener órdenes
GET /api/orders/1/products            // 3 llamada
GET /api/orders/2/products            // 4 llamada
GET /api/orders/3/products            // 5 llamada
GET /api/orders/4/products            // 6 llamada
GET /api/orders/5/products            // 7 llamada

// 7 llamadas HTTP = ~700ms latencia total

// ✅ SOLUCIÓN 1: Campos selectivos (sparse fieldsets)
GET /api/users/123?fields=id,firstName,email

{
  "id": 123,
  "firstName": "John",
  "email": "john@example.com"
}

// ✅ SOLUCIÓN 2: Incluir relaciones (compound documents)
GET /api/users/123?include=orders.products&orders.limit=5

{
  "id": 123,
  "firstName": "John",
  "orders": [
    {
      "id": 1,
      "total": 100,
      "products": [...]
    }
  ]
}

// ✅ SOLUCIÓN 3: GraphQL (el cliente pide exactamente lo que necesita)
query {
  user(id: 123) {
    firstName
    email
    orders(limit: 5) {
      total
      products {
        name
        price
      }
    }
  }
}
```

**Problema 4: Sin Versionado**

```javascript
// Mes 1: API lanzada
GET /api/users
[
  {
    "id": 123,
    "name": "John Doe",
    "email": "john@example.com"
  }
]

// Mes 6: Necesitas separar firstName y lastName
// ❌ SIN versionado: Cambias el schema y rompes a TODOS los clientes
GET /api/users
[
  {
    "id": 123,
    "firstName": "John",      // ❌ Cambio breaking
    "lastName": "Doe",         // ❌ "name" desapareció
    "email": "john@example.com"
  }
]

// Resultado:
// - Apps móviles antiguas: CRASH
// - Integraciones de terceros: ROTAS
// - Clientes enojados: CHURN

// ✅ CON versionado: Mantienes v1, introduces v2
GET /api/v1/users           // v1 sigue funcionando
[
  {
    "id": 123,
    "name": "John Doe",     // ✅ Formato antiguo preserved
    "email": "john@example.com"
  }
]

GET /api/v2/users           // v2 con nuevo schema
[
  {
    "id": 123,
    "firstName": "John",    // ✅ Nuevo formato
    "lastName": "Doe",
    "email": "john@example.com"
  }
]

// Beneficios:
// ✅ Clientes antiguos siguen funcionando
// ✅ Nuevos clientes usan v2
// ✅ Migración gradual
// ✅ Deprecation planificado (dar 6-12 meses aviso)
```

### 2.2 Beneficios de un Buen Diseño de API

```
1. DEVELOPER EXPERIENCE (DX)
   Antes: "¿Cómo uso esta API?" → 2 horas leyendo docs confusas
   Ahora: "API intuitiva y consistente" → 15 minutos para primer request

2. TIME TO MARKET
   Antes: 2 semanas integrando API mal diseñada
   Ahora: 2 días con API bien diseñada

3. MANTENIBILIDAD
   Antes: Cada cambio rompe clientes → miedo a cambiar
   Ahora: Versionado correcto → cambios seguros

4. ESCALABILIDAD
   Antes: Queries N+1, over-fetching → 5 seg por request
   Ahora: Optimizado, solo datos necesarios → 100ms

5. ADOPCIÓN
   API bien diseñada = Más developers = Más negocio

   Ejemplos:
   - Stripe: API excelente → $640B procesados
   - Twilio: API simple → 10M+ developers
   - AWS: Consistencia → Ecosystem gigante
```

---

## 3. REST: Conceptos Fundamentales

### 3.1 ¿Qué es REST?

**REST (Representational State Transfer)** es un estilo arquitectónico para diseñar APIs basadas en HTTP.

**Principios REST:**

```
1. CLIENT-SERVER
   ├── Separación de responsabilidades
   ├── Cliente maneja UI
   ├── Servidor maneja datos y lógica
   └── Pueden evolucionar independientemente

2. STATELESS
   ├── Cada request es independiente
   ├── Servidor NO guarda estado de sesión
   ├── Toda la información está en el request
   └── Mejor escalabilidad (puedes agregar servidores)

3. CACHEABLE
   ├── Responses deben indicar si son cacheables
   ├── Usa headers HTTP: Cache-Control, ETag
   └── Mejora performance y reduce carga

4. UNIFORM INTERFACE
   ├── URLs identifican recursos
   ├── HTTP methods (GET, POST, PUT, DELETE)
   ├── Representaciones estándar (JSON, XML)
   └── HATEOAS (enlaces a recursos relacionados)

5. LAYERED SYSTEM
   ├── Cliente no sabe si habla directo con servidor final
   ├── Puede haber proxies, gateways, load balancers
   └── Transparente para el cliente

6. CODE ON DEMAND (opcional)
   └── Servidor puede enviar código ejecutable (JavaScript)
```

### 3.2 Recursos: La Piedra Angular de REST

```
En REST, TODO es un RECURSO.

Recurso = Cualquier cosa que pueda ser nombrada y representada

Ejemplos:
✅ Un usuario
✅ Un producto
✅ Una orden de compra
✅ Una imagen
✅ Una colección de usuarios
✅ Una búsqueda
✅ Un proceso (checkout, export, etc.)

Identificación de Recursos:
┌─────────────────────────────────────────────────────┐
│  URL (Uniform Resource Locator)                     │
│  https://api.example.com/v1/users/123               │
│  └──────┬─────────┘ └┬┘ └─┬──┘ └─┬─┘               │
│      Protocol      Version │   Resource ID          │
│                          Collection                  │
└─────────────────────────────────────────────────────┘

Representación de Recursos:
- Un mismo recurso puede tener múltiples representaciones
- JSON (más común)
- XML
- HTML
- Protobuf

Ejemplo:
GET /api/users/123
Accept: application/json    → Retorna JSON
Accept: application/xml     → Retorna XML
Accept: text/html          → Retorna HTML
```

### 3.3 HTTP Methods (Verbos)

```
┌──────────┬─────────────┬──────────────┬─────────────┬──────────────┐
│ Method   │ CRUD        │ Safe         │ Idempotent  │ Request Body │
├──────────┼─────────────┼──────────────┼─────────────┼──────────────┤
│ GET      │ READ        │ ✅ Yes       │ ✅ Yes      │ ❌ No        │
│ POST     │ CREATE      │ ❌ No        │ ❌ No       │ ✅ Yes       │
│ PUT      │ UPDATE      │ ❌ No        │ ✅ Yes      │ ✅ Yes       │
│ PATCH    │ PARTIAL UP. │ ❌ No        │ ❌ No       │ ✅ Yes       │
│ DELETE   │ DELETE      │ ❌ No        │ ✅ Yes      │ ❌ No*       │
└──────────┴─────────────┴──────────────┴─────────────┴──────────────┘

* DELETE puede tener body, pero no es común

Safe (Seguro):
- No modifica recursos en el servidor
- Solo lectura
- Puede ser cacheado

Idempotent (Idempotente):
- Llamar N veces = mismo resultado que llamar 1 vez
- PUT /users/123 → 5 veces = usuario actualizado una vez
- DELETE /users/123 → 5 veces = usuario eliminado (404 en llamadas 2-5)
```

**Ejemplos Detallados:**

```bash
# ═══════════════════════════════════════════════════════
# GET - Obtener recursos
# ═══════════════════════════════════════════════════════

# Obtener colección
GET /api/users
200 OK
[
  { "id": 1, "name": "John" },
  { "id": 2, "name": "Jane" }
]

# Obtener recurso específico
GET /api/users/123
200 OK
{
  "id": 123,
  "name": "John Doe",
  "email": "john@example.com"
}

# Recurso no encontrado
GET /api/users/999
404 Not Found
{
  "error": {
    "code": "USER_NOT_FOUND",
    "message": "User with ID 999 not found"
  }
}

# ═══════════════════════════════════════════════════════
# POST - Crear nuevo recurso
# ═══════════════════════════════════════════════════════

POST /api/users
Content-Type: application/json

{
  "name": "Alice Smith",
  "email": "alice@example.com"
}

# Response
201 Created
Location: /api/users/124
{
  "id": 124,
  "name": "Alice Smith",
  "email": "alice@example.com",
  "createdAt": "2024-12-24T14:32:15Z"
}

# ═══════════════════════════════════════════════════════
# PUT - Reemplazar recurso completo (idempotente)
# ═══════════════════════════════════════════════════════

PUT /api/users/123
Content-Type: application/json

{
  "name": "John Updated",
  "email": "john.updated@example.com"
}

# Response
200 OK
{
  "id": 123,
  "name": "John Updated",
  "email": "john.updated@example.com",
  "updatedAt": "2024-12-24T14:35:00Z"
}

# PUT es idempotente: llamar 5 veces = mismo resultado
PUT /api/users/123 { "name": "John" }  # 1ra vez → 200 OK
PUT /api/users/123 { "name": "John" }  # 2da vez → 200 OK (mismo resultado)
PUT /api/users/123 { "name": "John" }  # 3ra vez → 200 OK (mismo resultado)

# ═══════════════════════════════════════════════════════
# PATCH - Actualización parcial
# ═══════════════════════════════════════════════════════

PATCH /api/users/123
Content-Type: application/json

{
  "email": "newemail@example.com"
  // Solo actualiza email, deja name sin cambios
}

# Response
200 OK
{
  "id": 123,
  "name": "John Doe",              // ← Sin cambios
  "email": "newemail@example.com",  // ← Actualizado
  "updatedAt": "2024-12-24T14:40:00Z"
}

# PUT vs PATCH:
# PUT: Reemplaza TODO el recurso
PUT /api/users/123 { "email": "new@example.com" }
# Resultado: name = null (fue removido), email = "new@example.com"

# PATCH: Actualiza SOLO los campos enviados
PATCH /api/users/123 { "email": "new@example.com" }
# Resultado: name = "John Doe" (sin cambios), email = "new@example.com"

# ═══════════════════════════════════════════════════════
# DELETE - Eliminar recurso
# ═══════════════════════════════════════════════════════

DELETE /api/users/123

# Response (sin body)
204 No Content

# O con body de confirmación
200 OK
{
  "message": "User deleted successfully",
  "deletedId": 123
}

# DELETE es idempotente:
DELETE /api/users/123  # 1ra vez → 204 No Content (eliminado)
DELETE /api/users/123  # 2da vez → 404 Not Found (ya no existe)
DELETE /api/users/123  # 3ra vez → 404 Not Found (ya no existe)
# Resultado final es el mismo: recurso no existe
```

---

## 4. Richardson Maturity Model

El **Richardson Maturity Model** define 4 niveles de madurez para APIs REST:

```
                    ┌─────────────────────┐
                    │   Level 3: HATEOAS  │
                    │   Hypermedia        │
                    └──────────┬──────────┘
                               │
                    ┌──────────┴──────────┐
                    │   Level 2: HTTP     │
                    │   Verbs + Status    │
                    └──────────┬──────────┘
                               │
                    ┌──────────┴──────────┐
                    │   Level 1: Resources│
                    │   URLs individuales │
                    └──────────┬──────────┘
                               │
                    ┌──────────┴──────────┐
                    │   Level 0: POX      │
                    │   (The Swamp)       │
                    └─────────────────────┘
```

### 4.1 Level 0: The Swamp of POX (Plain Old XML)

```bash
# Un solo endpoint, todo es POST
POST /api/endpoint

# Crear usuario
{
  "action": "createUser",
  "params": {
    "name": "John",
    "email": "john@example.com"
  }
}

# Obtener usuario
{
  "action": "getUser",
  "params": {
    "id": 123
  }
}

# Eliminar usuario
{
  "action": "deleteUser",
  "params": {
    "id": 123
  }
}

Problemas:
❌ No usa HTTP correctamente
❌ Todo es POST
❌ Sin códigos de estado semánticos
❌ No cacheable
❌ Similar a RPC, no REST

Nivel: 🔴 SOAP-like, no RESTful
```

### 4.2 Level 1: Resources

```bash
# Múltiples URLs, un endpoint por recurso
POST /api/users          # Crear
POST /api/users/get      # ❌ Todavía usa POST para todo
POST /api/users/delete   # ❌

{
  "id": 123
}

Mejoras:
✅ Recursos individuales (/users, /products)
✅ URLs específicas

Aún faltan:
❌ No usa HTTP verbs correctamente
❌ Todavía todo POST

Nivel: 🟡 Progreso, pero no RESTful aún
```

### 4.3 Level 2: HTTP Verbs

```bash
# Usa HTTP methods correctamente
GET    /api/users          # Listar
POST   /api/users          # Crear
GET    /api/users/123      # Obtener
PUT    /api/users/123      # Actualizar
DELETE /api/users/123      # Eliminar

# Usa códigos de estado HTTP
200 OK
201 Created
204 No Content
400 Bad Request
404 Not Found
409 Conflict
500 Internal Server Error

Mejoras:
✅ HTTP methods semánticos (GET, POST, PUT, DELETE)
✅ Códigos de estado correctos
✅ Cacheable (GET responses)
✅ Idempotencia (PUT, DELETE)

Nivel: 🟢 RESTful básico - LA MAYORÍA DE APIs ESTÁN AQUÍ
```

### 4.4 Level 3: Hypermedia Controls (HATEOAS)

**HATEOAS** = Hypermedia As The Engine Of Application State

```bash
# API incluye LINKS a recursos relacionados
GET /api/users/123

{
  "id": 123,
  "name": "John Doe",
  "email": "john@example.com",
  "_links": {
    "self": {
      "href": "/api/users/123"
    },
    "orders": {
      "href": "/api/users/123/orders"
    },
    "update": {
      "href": "/api/users/123",
      "method": "PUT"
    },
    "delete": {
      "href": "/api/users/123",
      "method": "DELETE"
    }
  }
}

# Cliente NO necesita construir URLs
# Solo sigue los links proporcionados

# Ejemplo: Orden con estados
GET /api/orders/456

{
  "id": 456,
  "status": "pending",
  "total": 150.00,
  "_links": {
    "self": { "href": "/api/orders/456" },
    "cancel": {
      "href": "/api/orders/456/cancel",
      "method": "POST"
    },
    "pay": {
      "href": "/api/orders/456/payment",
      "method": "POST"
    }
  }
}

# Si orden ya está pagada:
GET /api/orders/456

{
  "id": 456,
  "status": "paid",
  "total": 150.00,
  "_links": {
    "self": { "href": "/api/orders/456" },
    // "cancel" y "pay" NO están disponibles (orden pagada)
    "refund": {
      "href": "/api/orders/456/refund",
      "method": "POST"
    },
    "invoice": {
      "href": "/api/orders/456/invoice",
      "method": "GET"
    }
  }
}

Beneficios:
✅ Self-documenting (links muestran qué acciones son posibles)
✅ Evolvable (cambias URLs sin romper clientes)
✅ State machine explícito (links dependen del estado)

Desventajas:
❌ Más complejo
❌ Payloads más grandes
❌ Pocos clientes aprovechan HATEOAS

Nivel: 🟣 RESTful completo - POCAS APIs LLEGAN AQUÍ
Ejemplos: GitHub API (parcial), PayPal API
```

**Recomendación práctica:**

```
Para la mayoría de proyectos:
→ Apunta a Level 2 (HTTP Verbs)

Considera Level 3 (HATEOAS) solo si:
- API pública muy usada
- Necesitas versionado muy flexible
- Clientes sofisticados que aprovechen hypermedia
- Máquinas de estado complejas

La mayoría de APIs exitosas (Stripe, Twilio, Twitter) están en Level 2.
```

---

## 5. Diseño de Recursos y URLs

### 5.1 Convenciones de Nomenclatura

```bash
# ═══════════════════════════════════════════════════════
# ✅ REGLAS DE ORO para URLs
# ═══════════════════════════════════════════════════════

1. USA SUSTANTIVOS, NO VERBOS
   ✅ /users
   ✅ /products
   ✅ /orders
   ❌ /getUsers
   ❌ /createProduct
   ❌ /deleteOrder

2. USA PLURAL PARA COLECCIONES
   ✅ /users           (colección)
   ✅ /users/123       (recurso individual)
   ❌ /user            (inconsistente)
   ❌ /user/123

3. USA KEBAB-CASE (minúsculas con guiones)
   ✅ /product-categories
   ✅ /order-items
   ❌ /productCategories  (camelCase)
   ❌ /product_categories (snake_case)
   ❌ /ProductCategories  (PascalCase)

4. NO USES TRAILING SLASH
   ✅ /users/123
   ❌ /users/123/

5. VERSIONADO EN LA URL (o header)
   ✅ /v1/users
   ✅ /v2/users
   o
   ✅ /users (con header: Accept: application/vnd.api.v2+json)

6. FILTROS EN QUERY PARAMS, NO EN PATH
   ✅ /users?status=active&role=admin
   ❌ /users/active/admin

7. JERARQUÍA REFLEJA RELACIONES
   ✅ /users/123/orders           # Órdenes del usuario 123
   ✅ /users/123/orders/456       # Orden 456 del usuario 123
   ⚠️  Máximo 2-3 niveles (evitar /a/b/c/d/e/f)
```

### 5.2 Recursos y Sub-recursos

```bash
# ═══════════════════════════════════════════════════════
# COLECCIONES Y RECURSOS INDIVIDUALES
# ═══════════════════════════════════════════════════════

GET    /users              # Todos los usuarios
GET    /users/123          # Usuario específico
POST   /users              # Crear usuario
PUT    /users/123          # Actualizar usuario
PATCH  /users/123          # Actualización parcial
DELETE /users/123          # Eliminar usuario

# ═══════════════════════════════════════════════════════
# SUB-RECURSOS (Relaciones)
# ═══════════════════════════════════════════════════════

# Órdenes de un usuario
GET    /users/123/orders
POST   /users/123/orders         # Crear orden para usuario 123

# Orden específica de un usuario
GET    /users/123/orders/456
PUT    /users/123/orders/456
DELETE /users/123/orders/456

# Items de una orden
GET    /users/123/orders/456/items
POST   /users/123/orders/456/items

# ═══════════════════════════════════════════════════════
# CUANDO NO USAR SUB-RECURSOS
# ═══════════════════════════════════════════════════════

# ❌ Demasiado anidado
GET /users/123/orders/456/items/789/reviews/012

# ✅ MEJOR: Recurso de nivel superior con filtro
GET /reviews?itemId=789
GET /reviews/012

# ❌ Sub-recurso no tiene sentido semánticamente
GET /users/123/products  # Usuario no "posee" productos

# ✅ MEJOR: Filtrar productos por usuario
GET /products?userId=123
GET /products?createdBy=123

# ═══════════════════════════════════════════════════════
# ACCIONES QUE NO SON CRUD
# ═══════════════════════════════════════════════════════

# Regla: Usa sustantivos que representen "el proceso"

✅ /orders/123/payment       # Recurso: el pago
   POST /orders/123/payment  # Crear el pago

✅ /orders/123/cancellation
   POST /orders/123/cancellation  # Crear cancelación

✅ /users/123/password-reset
   POST /users/123/password-reset

✅ /products/search
   GET /products/search?q=laptop

# Alternativa: Usar verbos solo si realmente no hay recurso
✅ /orders/123/cancel        # Verbo como acción
   POST /orders/123/cancel

✅ /cart/checkout
   POST /cart/checkout

# Evita verbos genéricos en la URL base
❌ /process
❌ /execute
❌ /do
```

### 5.3 Ejemplos de URLs Bien Diseñadas

```bash
# ═══════════════════════════════════════════════════════
# E-COMMERCE
# ═══════════════════════════════════════════════════════

# Productos
GET    /v1/products
GET    /v1/products/123
GET    /v1/products?category=electronics&price[min]=100&price[max]=500
GET    /v1/products/search?q=laptop

# Categorías
GET    /v1/categories
GET    /v1/categories/456/products

# Carrito
GET    /v1/cart
POST   /v1/cart/items
DELETE /v1/cart/items/789

# Checkout
POST   /v1/orders                    # Crear orden desde cart
GET    /v1/orders/123
POST   /v1/orders/123/payment
POST   /v1/orders/123/cancellation

# ═══════════════════════════════════════════════════════
# SOCIAL MEDIA
# ═══════════════════════════════════════════════════════

# Usuarios
GET    /v1/users/123
GET    /v1/users/123/posts
GET    /v1/users/123/followers
GET    /v1/users/123/following

# Posts
GET    /v1/posts
POST   /v1/posts
GET    /v1/posts/789
DELETE /v1/posts/789

# Likes (sub-recurso o acción)
POST   /v1/posts/789/likes          # Dar like
DELETE /v1/posts/789/likes          # Quitar like
GET    /v1/posts/789/likes          # Ver quién dio like

# Comentarios
GET    /v1/posts/789/comments
POST   /v1/posts/789/comments
DELETE /v1/comments/456             # Top-level para facilitar

# ═══════════════════════════════════════════════════════
# BANKING
# ═══════════════════════════════════════════════════════

# Cuentas
GET    /v1/accounts
GET    /v1/accounts/123

# Transacciones
GET    /v1/accounts/123/transactions
GET    /v1/accounts/123/transactions?from=2024-01-01&to=2024-12-31
GET    /v1/transactions/789         # Transacción específica

# Transferencias
POST   /v1/transfers
GET    /v1/transfers/456
GET    /v1/transfers?status=pending

# ═══════════════════════════════════════════════════════
# SaaS PROJECT MANAGEMENT
# ═══════════════════════════════════════════════════════

# Proyectos
GET    /v1/projects
POST   /v1/projects
GET    /v1/projects/123

# Tareas
GET    /v1/projects/123/tasks
POST   /v1/projects/123/tasks
GET    /v1/tasks/456                # También accesible top-level
PUT    /v1/tasks/456

# Asignaciones
POST   /v1/tasks/456/assignments    # Asignar usuario a tarea
DELETE /v1/tasks/456/assignments/789

# Comentarios
GET    /v1/tasks/456/comments
POST   /v1/tasks/456/comments
```

---

## 6. HTTP Methods y Códigos de Estado

### 6.1 Códigos de Estado HTTP - Guía Completa

```
┌─────────────────────────────────────────────────────────────┐
│  1xx - INFORMATIONAL (raro en APIs REST)                    │
├─────────────────────────────────────────────────────────────┤
│  100 Continue                                               │
│  101 Switching Protocols                                    │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  2xx - SUCCESS                                              │
├─────────────────────────────────────────────────────────────┤
│  200 OK                    - GET exitoso, PUT exitoso       │
│  201 Created               - POST creó recurso              │
│  202 Accepted              - Request aceptado, procesando   │
│  204 No Content            - DELETE exitoso sin body        │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  3xx - REDIRECTION                                          │
├─────────────────────────────────────────────────────────────┤
│  301 Moved Permanently     - Recurso movido permanentemente │
│  302 Found                 - Redirección temporal           │
│  304 Not Modified          - Cache válido (con ETag)        │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  4xx - CLIENT ERROR                                         │
├─────────────────────────────────────────────────────────────┤
│  400 Bad Request           - Request malformado             │
│  401 Unauthorized          - No autenticado                 │
│  403 Forbidden             - Autenticado pero sin permiso   │
│  404 Not Found             - Recurso no existe              │
│  405 Method Not Allowed    - Method no soportado            │
│  409 Conflict              - Conflicto (ej: email duplicado)│
│  422 Unprocessable Entity  - Validación falló               │
│  429 Too Many Requests     - Rate limit excedido            │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  5xx - SERVER ERROR                                         │
├─────────────────────────────────────────────────────────────┤
│  500 Internal Server Error - Error genérico del servidor    │
│  502 Bad Gateway           - Proxy recibió respuesta mala   │
│  503 Service Unavailable   - Servidor temporalmente down    │
│  504 Gateway Timeout       - Proxy timeout                  │
└─────────────────────────────────────────────────────────────┘
```

### 6.2 Decisión de Código de Estado - Guía Rápida

```
┌─────────────────────────────────────────┐
│  Request recibido                       │
└──────────────┬──────────────────────────┘
               │
        ┌──────▼──────┐
        │ ¿Request    │
        │ malformado? │
        └──────┬──────┘
           Yes │           No
      ┌────────▼───────┐   │
      │ 400 Bad Request│   │
      └────────────────┘   │
                           │
                    ┌──────▼──────┐
                    │ ¿Usuario    │
                    │ autenticado?│
                    └──────┬──────┘
                       No  │  Yes
                  ┌────────▼───────┐
                  │ 401 Unauthorized│
                  └────────────────┘
                           │
                    ┌──────▼──────┐
                    │ ¿Tiene      │
                    │ permiso?    │
                    └──────┬──────┘
                       No  │  Yes
                  ┌────────▼───────┐
                  │ 403 Forbidden  │
                  └────────────────┘
                           │
                    ┌──────▼──────┐
                    │ ¿Recurso    │
                    │ existe?     │
                    └──────┬──────┘
                       No  │  Yes
                  ┌────────▼───────┐
                  │ 404 Not Found  │
                  └────────────────┘
                           │
                    ┌──────▼──────┐
                    │ ¿Validación │
                    │ correcta?   │
                    └──────┬──────┘
                       No  │  Yes
            ┌──────────────▼─────────────┐
            │ 422 Unprocessable Entity   │
            └────────────────────────────┘
                           │
                    ┌──────▼──────┐
                    │ ¿Conflicto  │
                    │ de estado?  │
                    └──────┬──────┘
                       Yes │  No
                  ┌────────▼───────┐
                  │ 409 Conflict   │
                  └────────────────┘
                           │
                    ┌──────▼──────┐
                    │ Procesar    │
                    │ request     │
                    └──────┬──────┘
                           │
                    ┌──────▼──────┐
                    │ ¿Éxito?     │
                    └──────┬──────┘
                       No  │  Yes
          ┌────────────────▼────────────────┐
          │ 500 Internal Server Error       │
          └─────────────────────────────────┘
                           │
                    ┌──────▼──────┐
                    │ ¿Qué method?│
                    └──────┬──────┘
                           │
            ┌──────────────┼──────────────┐
            │              │              │
         POST           PUT/PATCH      DELETE
            │              │              │
    ┌───────▼──────┐  ┌───▼────┐  ┌─────▼──────┐
    │ 201 Created  │  │ 200 OK │  │ 204 No     │
    │ + Location   │  │        │  │ Content    │
    └──────────────┘  └────────┘  └────────────┘
```

### 6.3 Ejemplos de Uso de Códigos de Estado

```javascript
// ═══════════════════════════════════════════════════════
// 200 OK - GET exitoso
// ═══════════════════════════════════════════════════════
GET /api/users/123

HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 123,
  "name": "John Doe",
  "email": "john@example.com"
}

// ═══════════════════════════════════════════════════════
// 201 Created - Recurso creado exitosamente
// ═══════════════════════════════════════════════════════
POST /api/users
{
  "name": "Alice",
  "email": "alice@example.com"
}

HTTP/1.1 201 Created
Location: /api/users/124
Content-Type: application/json

{
  "id": 124,
  "name": "Alice",
  "email": "alice@example.com",
  "createdAt": "2024-12-24T14:32:15Z"
}

// ═══════════════════════════════════════════════════════
// 204 No Content - Operación exitosa sin body
// ═══════════════════════════════════════════════════════
DELETE /api/users/123

HTTP/1.1 204 No Content

// ═══════════════════════════════════════════════════════
// 400 Bad Request - Request malformado
// ═══════════════════════════════════════════════════════
POST /api/users
{
  "email": "not-valid-email"  // Falta "name"
}

HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "error": {
    "code": "INVALID_REQUEST",
    "message": "Request validation failed",
    "details": [
      {
        "field": "name",
        "message": "Field 'name' is required"
      }
    ]
  }
}

// ═══════════════════════════════════════════════════════
// 401 Unauthorized - No autenticado
// ═══════════════════════════════════════════════════════
GET /api/users/123
Authorization: Bearer invalid_token

HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer realm="api"
Content-Type: application/json

{
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Invalid or expired authentication token"
  }
}

// ═══════════════════════════════════════════════════════
// 403 Forbidden - Autenticado pero sin permiso
// ═══════════════════════════════════════════════════════
DELETE /api/users/999
Authorization: Bearer valid_token_but_not_admin

HTTP/1.1 403 Forbidden
Content-Type: application/json

{
  "error": {
    "code": "FORBIDDEN",
    "message": "You don't have permission to delete users"
  }
}

// ═══════════════════════════════════════════════════════
// 404 Not Found - Recurso no existe
// ═══════════════════════════════════════════════════════
GET /api/users/99999

HTTP/1.1 404 Not Found
Content-Type: application/json

{
  "error": {
    "code": "USER_NOT_FOUND",
    "message": "User with ID 99999 not found"
  }
}

// ═══════════════════════════════════════════════════════
// 409 Conflict - Conflicto de estado/recursos
// ═══════════════════════════════════════════════════════
POST /api/users
{
  "name": "Bob",
  "email": "existing@example.com"  // Email ya existe
}

HTTP/1.1 409 Conflict
Content-Type: application/json

{
  "error": {
    "code": "DUPLICATE_EMAIL",
    "message": "User with email 'existing@example.com' already exists",
    "field": "email"
  }
}

// ═══════════════════════════════════════════════════════
// 422 Unprocessable Entity - Validación de negocio falló
// ═══════════════════════════════════════════════════════
POST /api/orders
{
  "userId": 123,
  "items": [],  // Carrito vacío
  "total": 0
}

HTTP/1.1 422 Unprocessable Entity
Content-Type: application/json

{
  "error": {
    "code": "VALIDATION_FAILED",
    "message": "Order validation failed",
    "details": [
      {
        "field": "items",
        "message": "Order must have at least one item"
      }
    ]
  }
}

// ═══════════════════════════════════════════════════════
// 429 Too Many Requests - Rate limit excedido
// ═══════════════════════════════════════════════════════
GET /api/users

HTTP/1.1 429 Too Many Requests
Retry-After: 60
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1703435535
Content-Type: application/json

{
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "API rate limit exceeded. Try again in 60 seconds.",
    "retryAfter": 60
  }
}

// ═══════════════════════════════════════════════════════
// 500 Internal Server Error - Error del servidor
// ═══════════════════════════════════════════════════════
GET /api/users/123

HTTP/1.1 500 Internal Server Error
Content-Type: application/json

{
  "error": {
    "code": "INTERNAL_ERROR",
    "message": "An unexpected error occurred. Please try again later.",
    "requestId": "req_abc123def456"  // Para tracking en logs
  }
}
// ❌ NUNCA expongas stack traces o detalles internos en producción

// ═══════════════════════════════════════════════════════
// 503 Service Unavailable - Mantenimiento o sobrecarga
// ═══════════════════════════════════════════════════════
GET /api/users

HTTP/1.1 503 Service Unavailable
Retry-After: 3600
Content-Type: application/json

{
  "error": {
    "code": "SERVICE_UNAVAILABLE",
    "message": "Service temporarily unavailable due to maintenance",
    "retryAfter": 3600
  }
}
```

---

## 7. Versionado de APIs

### 7.1 ¿Por Qué Versionar?

```
REALIDAD: Tu API CAMBIARÁ con el tiempo

Cambios que requieren versionado:
✅ Renombrar campos (name → firstName + lastName)
✅ Cambiar tipos de datos (string → object)
✅ Remover campos
✅ Cambiar comportamiento (cálculo de descuentos)
✅ Reestructurar responses

Cambios que NO requieren versionado (backward compatible):
✅ Agregar campos nuevos opcionales
✅ Agregar endpoints nuevos
✅ Agregar query params opcionales
✅ Hacer campos opcionales (que eran required)

SIN versionado:
- Cambio → Rompe clientes existentes
- Apps móviles antiguas crashean
- Integraciones de terceros fallan
- Clientes enojados

CON versionado:
- v1 sigue funcionando
- v2 con cambios nuevos
- Migración gradual
- Deprecation planificado
```

### 7.2 Estrategias de Versionado

**Estrategia 1: URI Versioning (Más Común)**

```bash
# ✅ Versión en path
GET /api/v1/users
GET /api/v2/users

Ventajas:
✅ Muy explícito y visible
✅ Fácil de testear en browser
✅ Fácil de cachear
✅ Más usado (Stripe, Twitter, GitHub)

Desventajas:
❌ URLs cambian
❌ Puede considerarse "no RESTful puro"

Cuándo usar:
- APIs públicas
- Cambios breaking mayores
- Simplicidad es prioridad

Ejemplo:
GET /api/v1/users/123
{
  "id": 123,
  "name": "John Doe",
  "email": "john@example.com"
}

GET /api/v2/users/123
{
  "id": 123,
  "firstName": "John",
  "lastName": "Doe",
  "email": "john@example.com",
  "profile": {
    "avatar": "https://...",
    "bio": "..."
  }
}
```

**Estrategia 2: Header Versioning**

```bash
# Versión en header personalizado
GET /api/users
X-API-Version: 1

GET /api/users
X-API-Version: 2

Ventajas:
✅ URLs limpias y estables
✅ Más "RESTful"
✅ Un solo endpoint

Desventajas:
❌ Menos visible
❌ Más difícil de testear
❌ Complicado de cachear

Ejemplo:
curl -H "X-API-Version: 2" https://api.example.com/users
```

**Estrategia 3: Content Negotiation (Accept Header)**

```bash
# Versión en Accept header
GET /api/users
Accept: application/vnd.example.v1+json

GET /api/users
Accept: application/vnd.example.v2+json

Ventajas:
✅ Estándar HTTP
✅ URLs estables
✅ Más "RESTful puro"

Desventajas:
❌ Complejo para clients
❌ Difícil de testear manualmente
❌ Menos común

Ejemplo:
curl -H "Accept: application/vnd.example.v2+json" \
  https://api.example.com/users

Usado por: GitHub API
```

**Estrategia 4: Query Parameter**

```bash
# ❌ Generalmente NO recomendado
GET /api/users?version=1
GET /api/users?version=2

Desventajas:
❌ Puede afectar caching
❌ Mezcla versionado con filtros
❌ No es semántico

Solo usar si:
- Necesitas versionado muy temporal
- Testing/debugging
```

### 7.3 Estrategia Recomendada y Mejores Prácticas

```bash
# ═══════════════════════════════════════════════════════
# RECOMENDACIÓN: URI Versioning (mayoría de casos)
# ═══════════════════════════════════════════════════════

/api/v1/users
/api/v2/users

# Reglas:
1. Versión MAJOR solamente (v1, v2, v3)
   ❌ NO: /api/v1.2.3/users (semantic versioning en URL es excesivo)
   ✅ SÍ: /api/v1/users

2. Versión al inicio del path
   ✅ /api/v1/users
   ❌ /api/users/v1  (confuso)

3. Mantener mínimo 1 versión anterior
   - v3 lanzado → v2 y v3 funcionan
   - v4 lanzado → v3 y v4 funcionan (v2 deprecated)

4. Deprecation period: 6-12 meses
   - Mes 1: Anunciar deprecation de v1
   - Mes 3: Warnings en responses de v1
   - Mes 6-12: Shutdown de v1

# ═══════════════════════════════════════════════════════
# Ejemplo de Deprecation Warning
# ═══════════════════════════════════════════════════════

GET /api/v1/users

HTTP/1.1 200 OK
Sunset: Sat, 01 Jan 2025 00:00:00 GMT
Deprecation: true
Link: </api/v2/users>; rel="successor-version"

{
  "data": [...],
  "meta": {
    "deprecation": {
      "deprecated": true,
      "sunsetDate": "2025-01-01",
      "message": "This version will be sunset on 2025-01-01. Please migrate to v2.",
      "migrationGuide": "https://docs.example.com/migration/v1-to-v2"
    }
  }
}

# ═══════════════════════════════════════════════════════
# Cambios que NO requieren nueva versión
# ═══════════════════════════════════════════════════════

# ✅ Agregar campos nuevos (backward compatible)
# v1:
{
  "id": 123,
  "name": "John"
}

# v1 (enhanced, pero sigue siendo v1):
{
  "id": 123,
  "name": "John",
  "avatar": "https://..."  // ← Nuevo campo, clientes viejos lo ignoran
}

# ✅ Agregar endpoints nuevos
POST /api/v1/users/123/avatar  # Nuevo endpoint, no afecta existentes

# ✅ Agregar query params opcionales
GET /api/v1/users?includeAvatar=true  # Opcional, default = false

# ═══════════════════════════════════════════════════════
# Cambios que SÍ requieren nueva versión
# ═══════════════════════════════════════════════════════

# ❌ Renombrar campos
# v1: { "name": "John Doe" }
# v2: { "firstName": "John", "lastName": "Doe" }  # ← Breaking change

# ❌ Cambiar tipos
# v1: { "price": "100.50" }  # String
# v2: { "price": 100.50 }     # Number  ← Breaking change

# ❌ Remover campos
# v1: { "id": 123, "name": "John", "age": 30 }
# v2: { "id": 123, "name": "John" }  # age removido ← Breaking change

# ❌ Cambiar estructura
# v1: { "user": {...} }
# v2: { "data": { "user": {...} } }  # ← Breaking change
```

---

## 8. Autenticación y Autorización

### 8.1 Diferencia: Autenticación vs Autorización

```
AUTENTICACIÓN (Authentication)
"¿QUIÉN eres?"
→ Verificar identidad del usuario
→ Login con username/password
→ Validar token
→ Código HTTP: 401 Unauthorized

AUTORIZACIÓN (Authorization)
"¿QUÉ puedes hacer?"
→ Verificar permisos del usuario
→ Roles (admin, user, guest)
→ Permissions (read, write, delete)
→ Código HTTP: 403 Forbidden

Ejemplo:
┌─────────────────────────────────────────────────────┐
│  Usuario: john@example.com                          │
│  Password: ********                                 │
│  → AUTENTICACIÓN exitosa ✅                         │
│  → Token JWT generado                               │
└─────────────────────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────────────────┐
│  Request: DELETE /api/users/999                     │
│  Authorization: Bearer token_de_john                │
│  → Usuario autenticado ✅                           │
│  → ¿Tiene permiso para DELETE users?               │
│  → Role: "user" (no es admin)                       │
│  → AUTORIZACIÓN fallida ❌                          │
│  → 403 Forbidden                                    │
└─────────────────────────────────────────────────────┘
```

### 8.2 Métodos de Autenticación

**1. API Keys (Básico)**

```bash
# API Key en header
GET /api/users
X-API-Key: sk_live_abc123def456

# O en query param (menos seguro)
GET /api/users?api_key=sk_live_abc123def456

Ventajas:
✅ Simple de implementar
✅ Ideal para server-to-server
✅ Fácil de rotar

Desventajas:
❌ No identifica usuarios individuales
❌ Sin expiración automática
❌ Si se compromete, acceso total
❌ No para apps frontend (key visible)

Cuándo usar:
- APIs internas
- Server-to-server
- Webhooks
- Scripts/automation

Ejemplos: Stripe, SendGrid, Mailgun
```

```javascript
// Ejemplo en servidor (Node.js/Express)
const API_KEYS = {
  'sk_live_abc123': { name: 'Service A', permissions: ['read', 'write'] },
  'sk_live_def456': { name: 'Service B', permissions: ['read'] }
};

app.use('/api', (req, res, next) => {
  const apiKey = req.header('X-API-Key');

  if (!apiKey) {
    return res.status(401).json({
      error: {
        code: 'API_KEY_MISSING',
        message: 'API key is required'
      }
    });
  }

  const keyInfo = API_KEYS[apiKey];
  if (!keyInfo) {
    return res.status(401).json({
      error: {
        code: 'INVALID_API_KEY',
        message: 'Invalid API key'
      }
    });
  }

  req.apiClient = keyInfo;
  next();
});
```

**2. Basic Authentication (Legacy)**

```bash
# Username:Password en Base64
GET /api/users
Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=
#                    ↑ base64("username:password")

Ventajas:
✅ Estándar HTTP
✅ Soportado por todos los browsers

Desventajas:
❌ Credenciales en CADA request
❌ Base64 != cifrado (fácil decodificar)
❌ REQUIERE HTTPS
❌ Sin expiración
❌ Problemas de logout

Cuándo usar:
- APIs muy simples
- Desarrollo/testing
- Cuando no puedes usar mejor método

⚠️  SIEMPRE sobre HTTPS
```

**3. Bearer Token / JWT (Recomendado para APIs modernas)**

```bash
# Token en Authorization header
GET /api/users
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...

# JWT (JSON Web Token) estructura:
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9  ← Header
.
eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ  ← Payload
.
SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c  ← Signature

# Payload decodificado:
{
  "sub": "1234567890",      # Subject (user ID)
  "name": "John Doe",
  "email": "john@example.com",
  "role": "admin",
  "iat": 1516239022,        # Issued at
  "exp": 1516242622         # Expiration (1 hora)
}

Ventajas:
✅ Stateless (no almacenamiento en servidor)
✅ Auto-contenido (info en el token)
✅ Expiración automática
✅ Escalable
✅ Estándar de industria

Desventajas:
❌ No se puede revocar fácilmente
❌ Tamaño mayor que API key
❌ Si se compromete, válido hasta expiration

Cuándo usar:
- APIs RESTful modernas
- SPAs (React, Vue, Angular)
- Apps móviles
- Microservicios
```

```javascript
// ═══════════════════════════════════════════════════════
// Ejemplo: Login y generación de JWT (Node.js)
// ═══════════════════════════════════════════════════════

const jwt = require('jsonwebtoken');
const bcrypt = require('bcrypt');

// Login endpoint
app.post('/api/auth/login', async (req, res) => {
  const { email, password } = req.body;

  // 1. Buscar usuario
  const user = await User.findOne({ email });
  if (!user) {
    return res.status(401).json({
      error: {
        code: 'INVALID_CREDENTIALS',
        message: 'Invalid email or password'
      }
    });
  }

  // 2. Verificar password
  const isValidPassword = await bcrypt.compare(password, user.passwordHash);
  if (!isValidPassword) {
    return res.status(401).json({
      error: {
        code: 'INVALID_CREDENTIALS',
        message: 'Invalid email or password'
      }
    });
  }

  // 3. Generar JWT
  const token = jwt.sign(
    {
      sub: user.id,
      email: user.email,
      role: user.role
    },
    process.env.JWT_SECRET,
    {
      expiresIn: '1h'  // Token válido por 1 hora
    }
  );

  // 4. Retornar token
  res.json({
    accessToken: token,
    tokenType: 'Bearer',
    expiresIn: 3600,
    user: {
      id: user.id,
      email: user.email,
      name: user.name
    }
  });
});

// ═══════════════════════════════════════════════════════
// Middleware de autenticación JWT
// ═══════════════════════════════════════════════════════

function authenticateToken(req, res, next) {
  // 1. Obtener token del header
  const authHeader = req.headers['authorization'];
  const token = authHeader && authHeader.split(' ')[1]; // Bearer TOKEN

  if (!token) {
    return res.status(401).json({
      error: {
        code: 'TOKEN_MISSING',
        message: 'Authentication token is required'
      }
    });
  }

  // 2. Verificar token
  jwt.verify(token, process.env.JWT_SECRET, (err, decoded) => {
    if (err) {
      if (err.name === 'TokenExpiredError') {
        return res.status(401).json({
          error: {
            code: 'TOKEN_EXPIRED',
            message: 'Authentication token has expired'
          }
        });
      }

      return res.status(401).json({
        error: {
          code: 'INVALID_TOKEN',
          message: 'Invalid authentication token'
        }
      });
    }

    // 3. Token válido, agregar user info al request
    req.user = decoded;
    next();
  });
}

// Usar middleware
app.get('/api/users', authenticateToken, (req, res) => {
  // req.user contiene la info del JWT
  console.log(req.user.sub);   // User ID
  console.log(req.user.role);  // Role
  // ...
});
```

**4. OAuth 2.0 (Para APIs con third-party access)**

```bash
# OAuth 2.0 - Delegación de acceso

Ejemplo: "Login with Google"

┌─────────────────────────────────────────────────────┐
│  1. Usuario: "Quiero usar GitHub login"            │
└──────────────────┬──────────────────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────────────────┐
│  2. Redirect a GitHub OAuth                         │
│  https://github.com/login/oauth/authorize?          │
│    client_id=abc123&                                │
│    redirect_uri=https://myapp.com/callback&         │
│    scope=user:email                                 │
└──────────────────┬──────────────────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────────────────┐
│  3. Usuario autoriza en GitHub                      │
│  "¿Permitir que MyApp acceda a tu email?"          │
│  [Authorize] [Deny]                                 │
└──────────────────┬──────────────────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────────────────┐
│  4. GitHub redirect a MyApp con code                │
│  https://myapp.com/callback?code=xyz789             │
└──────────────────┬──────────────────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────────────────┐
│  5. MyApp intercambia code por access_token         │
│  POST https://github.com/login/oauth/access_token   │
│  {                                                   │
│    code: "xyz789",                                  │
│    client_id: "abc123",                             │
│    client_secret: "secret"                          │
│  }                                                   │
└──────────────────┬──────────────────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────────────────┐
│  6. GitHub retorna access_token                     │
│  {                                                   │
│    access_token: "gho_abc123...",                   │
│    token_type: "bearer",                            │
│    scope: "user:email"                              │
│  }                                                   │
└──────────────────┬──────────────────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────────────────┐
│  7. MyApp usa access_token para acceder a API       │
│  GET https://api.github.com/user                    │
│  Authorization: Bearer gho_abc123...                │
└─────────────────────────────────────────────────────┘

Ventajas:
✅ Usuario NO da password a tu app
✅ Acceso limitado (scopes)
✅ Revocable desde provider
✅ Refresh tokens para renovar acceso

Cuándo usar:
- "Login with Google/Facebook/GitHub"
- Tu API permite acceso a third-party apps
- Necesitas acceso a APIs de terceros

Providers: Google, GitHub, Facebook, Auth0, Okta
```

### 8.3 Autorización (Permissions y Roles)

```javascript
// ═══════════════════════════════════════════════════════
// RBAC (Role-Based Access Control)
// ═══════════════════════════════════════════════════════

const ROLES = {
  ADMIN: {
    permissions: ['users:read', 'users:write', 'users:delete', 'posts:*']
  },
  MODERATOR: {
    permissions: ['users:read', 'posts:read', 'posts:write', 'posts:delete']
  },
  USER: {
    permissions: ['users:read:own', 'posts:read', 'posts:write:own']
  },
  GUEST: {
    permissions: ['posts:read']
  }
};

// Middleware de autorización
function authorize(...requiredPermissions) {
  return (req, res, next) => {
    const userRole = req.user.role;
    const userPermissions = ROLES[userRole].permissions;

    const hasPermission = requiredPermissions.every(permission => {
      return userPermissions.includes(permission) ||
             userPermissions.includes(permission.split(':')[0] + ':*');
    });

    if (!hasPermission) {
      return res.status(403).json({
        error: {
          code: 'FORBIDDEN',
          message: 'You don\'t have permission to perform this action',
          required: requiredPermissions,
          current: userPermissions
        }
      });
    }

    next();
  };
}

// Uso:
app.delete('/api/users/:id',
  authenticateToken,                    // 1. Autenticar
  authorize('users:delete'),            // 2. Autorizar
  async (req, res) => {
    // Solo admins llegan aquí
    await User.delete(req.params.id);
    res.status(204).send();
  }
);

app.get('/api/users',
  authenticateToken,
  authorize('users:read'),
  async (req, res) => {
    // Admins, moderators y users llegan aquí
    const users = await User.findAll();
    res.json(users);
  }
);

// ═══════════════════════════════════════════════════════
// Autorización basada en ownership
// ═══════════════════════════════════════════════════════

app.put('/api/posts/:id',
  authenticateToken,
  async (req, res) => {
    const post = await Post.findById(req.params.id);

    if (!post) {
      return res.status(404).json({
        error: { code: 'POST_NOT_FOUND', message: 'Post not found' }
      });
    }

    // Verificar si es el autor o admin
    if (post.authorId !== req.user.sub && req.user.role !== 'ADMIN') {
      return res.status(403).json({
        error: {
          code: 'FORBIDDEN',
          message: 'You can only edit your own posts'
        }
      });
    }

    // Proceder con actualización
    await post.update(req.body);
    res.json(post);
  }
);
```

---

## 9. Paginación, Filtrado y Ordenamiento

### 9.1 Paginación

**¿Por qué Paginar?**

```
Problema SIN paginación:
GET /api/products

Response:
[...10,000 products...]  ← 50MB JSON

Problemas:
❌ Tiempo de respuesta: 30+ segundos
❌ Memoria del servidor: Sobrecarga
❌ Ancho de banda: Costoso
❌ Cliente: Puede crashear browser/app

Solución CON paginación:
GET /api/products?page=1&limit=20

Response:
[...20 products...]  ← 100KB JSON

Beneficios:
✅ Respuesta rápida: <500ms
✅ Memoria controlada
✅ Mejor UX (carga progresiva)
```

**Estrategia 1: Offset-Based Pagination (Más Común)**

```bash
# ═══════════════════════════════════════════════════════
# OFFSET + LIMIT (Page-based)
# ═══════════════════════════════════════════════════════

# Página 1 (primeros 20)
GET /api/products?page=1&limit=20
GET /api/products?offset=0&limit=20  # Equivalente

# Página 2 (siguientes 20)
GET /api/products?page=2&limit=20
GET /api/products?offset=20&limit=20

# Página 3
GET /api/products?page=3&limit=20
GET /api/products?offset=40&limit=20
```

```json
// Response format
{
  "data": [
    { "id": 1, "name": "Product 1", "price": 100 },
    { "id": 2, "name": "Product 2", "price": 150 },
    // ... 18 more
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 1543,           // Total de items
    "totalPages": 78,         // Total de páginas
    "hasNextPage": true,
    "hasPreviousPage": false
  },
  "links": {
    "self": "/api/products?page=1&limit=20",
    "first": "/api/products?page=1&limit=20",
    "last": "/api/products?page=78&limit=20",
    "next": "/api/products?page=2&limit=20",
    "previous": null
  }
}
```

```javascript
// Implementación en Node.js/Express
app.get('/api/products', async (req, res) => {
  // Parse query params
  const page = parseInt(req.query.page) || 1;
  const limit = parseInt(req.query.limit) || 20;
  const offset = (page - 1) * limit;

  // Validar límites
  if (limit > 100) {
    return res.status(400).json({
      error: {
        code: 'INVALID_LIMIT',
        message: 'Limit cannot exceed 100'
      }
    });
  }

  // Query con paginación
  const [products, total] = await Promise.all([
    Product.findAll({ offset, limit }),
    Product.count()
  ]);

  const totalPages = Math.ceil(total / limit);

  res.json({
    data: products,
    pagination: {
      page,
      limit,
      total,
      totalPages,
      hasNextPage: page < totalPages,
      hasPreviousPage: page > 1
    },
    links: {
      self: `/api/products?page=${page}&limit=${limit}`,
      first: `/api/products?page=1&limit=${limit}`,
      last: `/api/products?page=${totalPages}&limit=${limit}`,
      next: page < totalPages ? `/api/products?page=${page + 1}&limit=${limit}` : null,
      previous: page > 1 ? `/api/products?page=${page - 1}&limit=${limit}` : null
    }
  });
});
```

**Ventajas y Desventajas:**

```
Offset-Based Pagination

Ventajas:
✅ Fácil de implementar
✅ Permite saltar a página específica
✅ Muestra total de páginas
✅ Funciona bien para datasets estáticos

Desventajas:
❌ Performance degrada con offsets grandes
   SELECT * FROM products LIMIT 20 OFFSET 100000  ← Lento
❌ Resultados inconsistentes si datos cambian
   (durante paginación, insertan/eliminan items → saltas/duplicas)
❌ No funciona bien para datos en tiempo real
```

**Estrategia 2: Cursor-Based Pagination (Para feeds en tiempo real)**

```bash
# ═══════════════════════════════════════════════════════
# CURSOR (Keyset pagination)
# ═══════════════════════════════════════════════════════

# Primera página
GET /api/posts?limit=20

# Response incluye cursor
{
  "data": [...],
  "pagination": {
    "nextCursor": "eyJpZCI6MjAsImNyZWF0ZWRBdCI6IjIwMjQtMTItMjQifQ==",
    "hasMore": true
  }
}

# Siguiente página (usa cursor anterior)
GET /api/posts?limit=20&cursor=eyJpZCI6MjAsImNyZWF0ZWRBdCI6IjIwMjQtMTItMjQifQ==
```

```javascript
// Implementación con cursor
app.get('/api/posts', async (req, res) => {
  const limit = parseInt(req.query.limit) || 20;
  const cursor = req.query.cursor;

  let query = Post.query().orderBy('createdAt', 'desc').limit(limit + 1);

  if (cursor) {
    // Decodificar cursor
    const decoded = JSON.parse(Buffer.from(cursor, 'base64').toString());
    // Continuar desde el cursor
    query = query.where('createdAt', '<', decoded.createdAt)
                 .orWhere(builder => {
                   builder.where('createdAt', '=', decoded.createdAt)
                          .where('id', '<', decoded.id);
                 });
  }

  const posts = await query;
  const hasMore = posts.length > limit;
  const results = posts.slice(0, limit);

  let nextCursor = null;
  if (hasMore) {
    const lastPost = results[results.length - 1];
    nextCursor = Buffer.from(JSON.stringify({
      id: lastPost.id,
      createdAt: lastPost.createdAt
    })).toString('base64');
  }

  res.json({
    data: results,
    pagination: {
      nextCursor,
      hasMore
    }
  });
});
```

**Ventajas y Desventajas:**

```
Cursor-Based Pagination

Ventajas:
✅ Performance constante (usa índices)
✅ Resultados consistentes (no afectado por cambios)
✅ Ideal para feeds en tiempo real
✅ Escalable a datasets grandes

Desventajas:
❌ No puedes saltar a página específica
❌ No sabes total de páginas
❌ Más complejo de implementar
❌ Cursor opaco para usuario

Cuándo usar:
- Feeds de redes sociales (Twitter, Facebook)
- Notifications
- Activity logs
- Datos que cambian frecuentemente
```

### 9.2 Filtrado

```bash
# ═══════════════════════════════════════════════════════
# FILTROS BÁSICOS (Igualdad)
# ═══════════════════════════════════════════════════════

GET /api/products?category=electronics
GET /api/products?inStock=true
GET /api/products?brand=apple

# Múltiples filtros (AND)
GET /api/products?category=electronics&inStock=true&brand=apple

# ═══════════════════════════════════════════════════════
# FILTROS DE RANGO
# ═══════════════════════════════════════════════════════

# Precio entre $100 y $500
GET /api/products?price[min]=100&price[max]=500
GET /api/products?price[gte]=100&price[lte]=500  # Greater/Less Than or Equal

# Fecha
GET /api/orders?createdAt[gte]=2024-01-01&createdAt[lte]=2024-12-31

# ═══════════════════════════════════════════════════════
# FILTROS DE LISTA (IN)
# ═══════════════════════════════════════════════════════

# Múltiples valores (OR)
GET /api/products?category=electronics,clothing,books
GET /api/products?id=1,5,10,25

# ═══════════════════════════════════════════════════════
# BÚSQUEDA DE TEXTO
# ═══════════════════════════════════════════════════════

GET /api/products/search?q=laptop
GET /api/products?name[contains]=macbook
GET /api/products?description[like]=%wireless%

# ═══════════════════════════════════════════════════════
# FILTROS NEGATIVOS
# ═══════════════════════════════════════════════════════

GET /api/products?category[not]=electronics
GET /api/products?price[gt]=1000  # Greater than (no equal)
```

```javascript
// Implementación de filtros
app.get('/api/products', async (req, res) => {
  let query = Product.query();

  // Filtro simple
  if (req.query.category) {
    query = query.where('category', req.query.category);
  }

  if (req.query.inStock !== undefined) {
    query = query.where('inStock', req.query.inStock === 'true');
  }

  // Filtro de rango (precio)
  if (req.query['price[min]']) {
    query = query.where('price', '>=', parseFloat(req.query['price[min]']));
  }
  if (req.query['price[max]']) {
    query = query.where('price', '<=', parseFloat(req.query['price[max]']));
  }

  // Filtro IN (múltiples categorías)
  if (req.query.categories) {
    const categories = req.query.categories.split(',');
    query = query.whereIn('category', categories);
  }

  // Búsqueda de texto
  if (req.query.q) {
    query = query.where('name', 'like', `%${req.query.q}%`)
                 .orWhere('description', 'like', `%${req.query.q}%`);
  }

  const products = await query;
  res.json({ data: products });
});
```

### 9.3 Ordenamiento (Sorting)

```bash
# ═══════════════════════════════════════════════════════
# ORDENAMIENTO BÁSICO
# ═══════════════════════════════════════════════════════

# Ascendente (por defecto)
GET /api/products?sort=price
GET /api/products?sort=+price      # Explícito ascendente

# Descendente
GET /api/products?sort=-price

# ═══════════════════════════════════════════════════════
# ORDENAMIENTO MÚLTIPLE
# ═══════════════════════════════════════════════════════

# Ordenar por categoría (ASC), luego precio (DESC)
GET /api/products?sort=category,-price

# Ordenar por stock (DESC), nombre (ASC), precio (DESC)
GET /api/products?sort=-inStock,name,-price

# ═══════════════════════════════════════════════════════
# ALTERNATIVAS DE SINTAXIS
# ═══════════════════════════════════════════════════════

# Query param separado
GET /api/products?sortBy=price&order=desc

# Múltiples params
GET /api/products?sortBy=category&sortBy=price&order=asc&order=desc
```

```javascript
// Implementación de sorting
app.get('/api/products', async (req, res) => {
  let query = Product.query();

  // Parsing de sort param
  if (req.query.sort) {
    const sortFields = req.query.sort.split(',');

    sortFields.forEach(field => {
      const isDescending = field.startsWith('-');
      const fieldName = isDescending ? field.slice(1) : field;
      const direction = isDescending ? 'desc' : 'asc';

      // Validar que el campo sea permitido (seguridad)
      const allowedFields = ['name', 'price', 'createdAt', 'category'];
      if (allowedFields.includes(fieldName)) {
        query = query.orderBy(fieldName, direction);
      }
    });
  } else {
    // Ordenamiento por defecto
    query = query.orderBy('createdAt', 'desc');
  }

  const products = await query;
  res.json({ data: products });
});
```

### 9.4 Campos Selectivos (Sparse Fieldsets)

```bash
# ═══════════════════════════════════════════════════════
# SELECCIONAR SOLO CAMPOS NECESARIOS
# ═══════════════════════════════════════════════════════

# Retornar solo ID y nombre
GET /api/products?fields=id,name

# Response:
[
  { "id": 1, "name": "Product 1" },
  { "id": 2, "name": "Product 2" }
]

# Sin fields param, retorna todos los campos:
[
  {
    "id": 1,
    "name": "Product 1",
    "description": "Long description...",
    "price": 100,
    "category": "electronics",
    "images": [...],
    "reviews": [...],
    // ... muchos más campos
  }
]

Beneficios:
✅ Reduce tamaño del payload
✅ Más rápido (menos datos a serializar)
✅ Menos ancho de banda
✅ Mobile-friendly
```

```javascript
// Implementación de sparse fieldsets
app.get('/api/products', async (req, res) => {
  let query = Product.query();

  // Campos selectivos
  if (req.query.fields) {
    const fields = req.query.fields.split(',');

    // Siempre incluir ID
    if (!fields.includes('id')) {
      fields.unshift('id');
    }

    // Validar campos permitidos
    const allowedFields = ['id', 'name', 'price', 'category', 'description'];
    const validFields = fields.filter(f => allowedFields.includes(f));

    query = query.select(validFields);
  }

  const products = await query;
  res.json({ data: products });
});
```

### 9.5 Ejemplo Completo: Paginación + Filtrado + Sorting

```bash
# Request combinando todo
GET /api/products
  ?page=2
  &limit=20
  &category=electronics
  &price[min]=100
  &price[max]=500
  &inStock=true
  &sort=-price,name
  &fields=id,name,price,category
```

```javascript
// Implementación completa
app.get('/api/products', async (req, res) => {
  try {
    // 1. PAGINACIÓN
    const page = parseInt(req.query.page) || 1;
    const limit = Math.min(parseInt(req.query.limit) || 20, 100);
    const offset = (page - 1) * limit;

    // 2. BASE QUERY
    let query = Product.query();

    // 3. FILTROS
    if (req.query.category) {
      query = query.where('category', req.query.category);
    }

    if (req.query['price[min]']) {
      query = query.where('price', '>=', parseFloat(req.query['price[min]']));
    }

    if (req.query['price[max]']) {
      query = query.where('price', '<=', parseFloat(req.query['price[max]']));
    }

    if (req.query.inStock !== undefined) {
      query = query.where('inStock', req.query.inStock === 'true');
    }

    if (req.query.q) {
      query = query.where(builder => {
        builder.where('name', 'like', `%${req.query.q}%`)
               .orWhere('description', 'like', `%${req.query.q}%`);
      });
    }

    // 4. ORDENAMIENTO
    if (req.query.sort) {
      const sortFields = req.query.sort.split(',');
      const allowedSortFields = ['name', 'price', 'createdAt', 'category'];

      sortFields.forEach(field => {
        const isDescending = field.startsWith('-');
        const fieldName = isDescending ? field.slice(1) : field;
        const direction = isDescending ? 'desc' : 'asc';

        if (allowedSortFields.includes(fieldName)) {
          query = query.orderBy(fieldName, direction);
        }
      });
    } else {
      query = query.orderBy('createdAt', 'desc');
    }

    // 5. CAMPOS SELECTIVOS
    if (req.query.fields) {
      const fields = req.query.fields.split(',');
      const allowedFields = ['id', 'name', 'price', 'category', 'description', 'inStock'];
      const validFields = fields.filter(f => allowedFields.includes(f));

      if (!validFields.includes('id')) {
        validFields.unshift('id');
      }

      query = query.select(validFields);
    }

    // 6. EJECUTAR QUERY CON PAGINACIÓN
    const [products, total] = await Promise.all([
      query.clone().offset(offset).limit(limit),
      query.clone().resultSize()
    ]);

    const totalPages = Math.ceil(total / limit);

    // 7. RESPONSE
    res.json({
      data: products,
      pagination: {
        page,
        limit,
        total,
        totalPages,
        hasNextPage: page < totalPages,
        hasPreviousPage: page > 1
      },
      links: {
        self: buildUrl(req, { page }),
        first: buildUrl(req, { page: 1 }),
        last: buildUrl(req, { page: totalPages }),
        next: page < totalPages ? buildUrl(req, { page: page + 1 }) : null,
        previous: page > 1 ? buildUrl(req, { page: page - 1 }) : null
      }
    });

  } catch (error) {
    res.status(500).json({
      error: {
        code: 'INTERNAL_ERROR',
        message: 'An error occurred while fetching products'
      }
    });
  }
});

// Helper para construir URLs
function buildUrl(req, overrides = {}) {
  const params = { ...req.query, ...overrides };
  const queryString = new URLSearchParams(params).toString();
  return `${req.path}?${queryString}`;
}
```

---

## 10. Manejo de Errores

### 10.1 Formato Estándar de Error

```json
// ═══════════════════════════════════════════════════════
// FORMATO RECOMENDADO (RFC 7807 - Problem Details)
// ═══════════════════════════════════════════════════════

{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Request validation failed",
    "details": [
      {
        "field": "email",
        "message": "Email is required"
      },
      {
        "field": "password",
        "message": "Password must be at least 8 characters"
      }
    ],
    "timestamp": "2024-12-24T14:32:15Z",
    "path": "/api/users",
    "requestId": "req_abc123"
  }
}
```

```javascript
// ═══════════════════════════════════════════════════════
// Clase de Error Personalizada
// ═══════════════════════════════════════════════════════

class ApiError extends Error {
  constructor(code, message, statusCode = 500, details = null) {
    super(message);
    this.code = code;
    this.statusCode = statusCode;
    this.details = details;
  }
}

// Errores específicos
class ValidationError extends ApiError {
  constructor(message, details) {
    super('VALIDATION_ERROR', message, 422, details);
  }
}

class NotFoundError extends ApiError {
  constructor(resource, id) {
    super('NOT_FOUND', `${resource} with ID ${id} not found`, 404);
  }
}

class UnauthorizedError extends ApiError {
  constructor(message = 'Authentication required') {
    super('UNAUTHORIZED', message, 401);
  }
}

class ForbiddenError extends ApiError {
  constructor(message = 'Insufficient permissions') {
    super('FORBIDDEN', message, 403);
  }
}

// ═══════════════════════════════════════════════════════
// Error Handler Middleware (Express)
// ═══════════════════════════════════════════════════════

function errorHandler(err, req, res, next) {
  // Log del error (para debugging)
  console.error({
    error: err,
    requestId: req.id,
    path: req.path,
    method: req.method,
    user: req.user?.id
  });

  // Determinar status code
  const statusCode = err.statusCode || 500;

  // Construir response
  const errorResponse = {
    error: {
      code: err.code || 'INTERNAL_ERROR',
      message: err.message || 'An unexpected error occurred',
      timestamp: new Date().toISOString(),
      path: req.path,
      requestId: req.id
    }
  };

  // Agregar detalles si existen
  if (err.details) {
    errorResponse.error.details = err.details;
  }

  // En desarrollo, incluir stack trace
  if (process.env.NODE_ENV === 'development') {
    errorResponse.error.stack = err.stack;
  }

  res.status(statusCode).json(errorResponse);
}

// Registrar middleware al final
app.use(errorHandler);
```

### 10.2 Ejemplos de Manejo de Errores por Tipo

```javascript
// ═══════════════════════════════════════════════════════
// 400 - Bad Request (Request malformado)
// ═══════════════════════════════════════════════════════

app.post('/api/users', async (req, res, next) => {
  // Validar que el body sea JSON válido
  if (!req.is('application/json')) {
    return next(new ApiError(
      'INVALID_CONTENT_TYPE',
      'Content-Type must be application/json',
      400
    ));
  }

  // ... resto del código
});

// ═══════════════════════════════════════════════════════
// 401 - Unauthorized (No autenticado)
// ═══════════════════════════════════════════════════════

function requireAuth(req, res, next) {
  const token = req.headers.authorization?.split(' ')[1];

  if (!token) {
    return next(new UnauthorizedError('Authentication token required'));
  }

  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (err) {
    if (err.name === 'TokenExpiredError') {
      return next(new UnauthorizedError('Token has expired'));
    }
    return next(new UnauthorizedError('Invalid token'));
  }
}

// ═══════════════════════════════════════════════════════
// 403 - Forbidden (Autenticado pero sin permiso)
// ═══════════════════════════════════════════════════════

app.delete('/api/users/:id', requireAuth, async (req, res, next) => {
  if (req.user.role !== 'admin') {
    return next(new ForbiddenError('Admin role required to delete users'));
  }

  await User.delete(req.params.id);
  res.status(204).send();
});

// ═══════════════════════════════════════════════════════
// 404 - Not Found
// ═══════════════════════════════════════════════════════

app.get('/api/users/:id', async (req, res, next) => {
  const user = await User.findById(req.params.id);

  if (!user) {
    return next(new NotFoundError('User', req.params.id));
  }

  res.json(user);
});

// ═══════════════════════════════════════════════════════
// 409 - Conflict
// ═══════════════════════════════════════════════════════

app.post('/api/users', async (req, res, next) => {
  const { email } = req.body;

  const existingUser = await User.findByEmail(email);
  if (existingUser) {
    return next(new ApiError(
      'DUPLICATE_EMAIL',
      `User with email ${email} already exists`,
      409,
      [{ field: 'email', message: 'Email already in use' }]
    ));
  }

  const user = await User.create(req.body);
  res.status(201).json(user);
});

// ═══════════════════════════════════════════════════════
// 422 - Unprocessable Entity (Validación)
// ═══════════════════════════════════════════════════════

app.post('/api/orders', async (req, res, next) => {
  const errors = [];

  if (!req.body.items || req.body.items.length === 0) {
    errors.push({
      field: 'items',
      message: 'Order must have at least one item'
    });
  }

  if (req.body.total <= 0) {
    errors.push({
      field: 'total',
      message: 'Order total must be greater than 0'
    });
  }

  if (errors.length > 0) {
    return next(new ValidationError('Order validation failed', errors));
  }

  const order = await Order.create(req.body);
  res.status(201).json(order);
});

// ═══════════════════════════════════════════════════════
// 429 - Too Many Requests
// ═══════════════════════════════════════════════════════

const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutos
  max: 100, // Máximo 100 requests por windowMs
  message: {
    error: {
      code: 'RATE_LIMIT_EXCEEDED',
      message: 'Too many requests. Please try again later.',
      retryAfter: 900 // segundos
    }
  },
  standardHeaders: true,
  legacyHeaders: false,
  handler: (req, res) => {
    res.status(429).json({
      error: {
        code: 'RATE_LIMIT_EXCEEDED',
        message: 'Too many requests. Please try again later.',
        retryAfter: Math.ceil(req.rateLimit.resetTime / 1000)
      }
    });
  }
});

app.use('/api/', limiter);

// ═══════════════════════════════════════════════════════
// 500 - Internal Server Error
// ═══════════════════════════════════════════════════════

app.get('/api/users', async (req, res, next) => {
  try {
    const users = await User.findAll();
    res.json(users);
  } catch (error) {
    // Log completo del error
    logger.error('Error fetching users', {
      error: error.message,
      stack: error.stack,
      requestId: req.id
    });

    // Retornar error genérico al cliente (no exponer detalles)
    return next(new ApiError(
      'INTERNAL_ERROR',
      'An unexpected error occurred. Please try again later.',
      500
    ));
  }
});
```

### 10.3 Validación con Libraries

```javascript
// ═══════════════════════════════════════════════════════
// Validación con Joi
// ═══════════════════════════════════════════════════════

const Joi = require('joi');

const userSchema = Joi.object({
  email: Joi.string().email().required(),
  password: Joi.string().min(8).required(),
  name: Joi.string().min(2).max(100).required(),
  age: Joi.number().integer().min(18).optional()
});

app.post('/api/users', async (req, res, next) => {
  // Validar request body
  const { error, value } = userSchema.validate(req.body, {
    abortEarly: false  // Retornar todos los errores, no solo el primero
  });

  if (error) {
    const details = error.details.map(err => ({
      field: err.path.join('.'),
      message: err.message
    }));

    return next(new ValidationError('Validation failed', details));
  }

  // Continuar con value (datos validados y sanitizados)
  const user = await User.create(value);
  res.status(201).json(user);
});

// ═══════════════════════════════════════════════════════
// Middleware reutilizable de validación
// ═══════════════════════════════════════════════════════

function validate(schema) {
  return (req, res, next) => {
    const { error, value } = schema.validate(req.body, { abortEarly: false });

    if (error) {
      const details = error.details.map(err => ({
        field: err.path.join('.'),
        message: err.message
      }));

      return next(new ValidationError('Validation failed', details));
    }

    req.validatedBody = value;
    next();
  };
}

// Uso
app.post('/api/users', validate(userSchema), async (req, res) => {
  const user = await User.create(req.validatedBody);
  res.status(201).json(user);
});
```

---

## 11. Documentación con OpenAPI/Swagger

### 11.1 ¿Qué es OpenAPI/Swagger?

```
OpenAPI Specification (OAS)
= Estándar para describir APIs RESTful

Swagger
= Conjunto de herramientas basadas en OpenAPI

Componentes:
┌─────────────────────────────────────────────────────┐
│  OpenAPI Spec (YAML/JSON)                           │
│  ↓ Describe tu API                                  │
└──────────────────┬──────────────────────────────────┘
                   │
        ┌──────────┴──────────┬──────────────┐
        ▼                     ▼              ▼
┌───────────────┐   ┌─────────────┐   ┌────────────┐
│ Swagger UI    │   │ Code Gen    │   │ Validation │
│ (Docs interac)│   │ (Auto SDK)  │   │ (Testing)  │
└───────────────┘   └─────────────┘   └────────────┘

Beneficios:
✅ Documentación siempre actualizada
✅ Interactive API explorer (try it out!)
✅ Genera SDKs automáticamente
✅ Valida requests/responses
✅ Estándar de industria
```

### 11.2 Ejemplo de OpenAPI Specification

```yaml
# openapi.yaml
openapi: 3.0.3
info:
  title: E-Commerce API
  description: API for managing products, orders, and users
  version: 1.0.0
  contact:
    name: API Support
    email: support@example.com
  license:
    name: MIT
    url: https://opensource.org/licenses/MIT

servers:
  - url: https://api.example.com/v1
    description: Production
  - url: https://staging-api.example.com/v1
    description: Staging
  - url: http://localhost:3000/v1
    description: Development

tags:
  - name: Users
    description: User management
  - name: Products
    description: Product catalog
  - name: Orders
    description: Order processing

paths:
  # ═══════════════════════════════════════════════════════
  # USERS ENDPOINTS
  # ═══════════════════════════════════════════════════════

  /users:
    get:
      tags:
        - Users
      summary: List all users
      description: Returns a paginated list of users
      operationId: listUsers
      parameters:
        - name: page
          in: query
          description: Page number
          required: false
          schema:
            type: integer
            minimum: 1
            default: 1
        - name: limit
          in: query
          description: Items per page
          required: false
          schema:
            type: integer
            minimum: 1
            maximum: 100
            default: 20
        - name: role
          in: query
          description: Filter by role
          required: false
          schema:
            type: string
            enum: [admin, user, guest]
      responses:
        '200':
          description: Successful response
          content:
            application/json:
              schema:
                type: object
                properties:
                  data:
                    type: array
                    items:
                      $ref: '#/components/schemas/User'
                  pagination:
                    $ref: '#/components/schemas/Pagination'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '500':
          $ref: '#/components/responses/InternalError'
      security:
        - bearerAuth: []

    post:
      tags:
        - Users
      summary: Create a new user
      description: Creates a new user account
      operationId: createUser
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateUserRequest'
            examples:
              basic:
                summary: Basic user
                value:
                  email: john@example.com
                  password: secretpass123
                  name: John Doe
      responses:
        '201':
          description: User created successfully
          headers:
            Location:
              description: URL of the created user
              schema:
                type: string
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
        '400':
          $ref: '#/components/responses/BadRequest'
        '409':
          description: Email already exists
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'
              example:
                error:
                  code: DUPLICATE_EMAIL
                  message: User with this email already exists

  /users/{userId}:
    get:
      tags:
        - Users
      summary: Get user by ID
      operationId: getUserById
      parameters:
        - name: userId
          in: path
          required: true
          description: User ID
          schema:
            type: integer
            format: int64
      responses:
        '200':
          description: User found
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
        '404':
          $ref: '#/components/responses/NotFound'
      security:
        - bearerAuth: []

    put:
      tags:
        - Users
      summary: Update user
      operationId: updateUser
      parameters:
        - name: userId
          in: path
          required: true
          schema:
            type: integer
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/UpdateUserRequest'
      responses:
        '200':
          description: User updated
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
        '404':
          $ref: '#/components/responses/NotFound'
      security:
        - bearerAuth: []

    delete:
      tags:
        - Users
      summary: Delete user
      operationId: deleteUser
      parameters:
        - name: userId
          in: path
          required: true
          schema:
            type: integer
      responses:
        '204':
          description: User deleted successfully
        '404':
          $ref: '#/components/responses/NotFound'
        '403':
          $ref: '#/components/responses/Forbidden'
      security:
        - bearerAuth: []

  # ═══════════════════════════════════════════════════════
  # PRODUCTS ENDPOINTS
  # ═══════════════════════════════════════════════════════

  /products:
    get:
      tags:
        - Products
      summary: List products
      parameters:
        - name: page
          in: query
          schema:
            type: integer
            default: 1
        - name: limit
          in: query
          schema:
            type: integer
            default: 20
            maximum: 100
        - name: category
          in: query
          schema:
            type: string
        - name: price[min]
          in: query
          schema:
            type: number
            format: float
        - name: price[max]
          in: query
          schema:
            type: number
            format: float
        - name: sort
          in: query
          description: "Sort by field(s). Prefix with - for descending. Example: -price,name"
          schema:
            type: string
            example: "-price,name"
      responses:
        '200':
          description: Products list
          content:
            application/json:
              schema:
                type: object
                properties:
                  data:
                    type: array
                    items:
                      $ref: '#/components/schemas/Product'
                  pagination:
                    $ref: '#/components/schemas/Pagination'

# ═══════════════════════════════════════════════════════
# COMPONENTS (Schemas reusables)
# ═══════════════════════════════════════════════════════

components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

  schemas:
    User:
      type: object
      properties:
        id:
          type: integer
          format: int64
          example: 123
        email:
          type: string
          format: email
          example: john@example.com
        name:
          type: string
          example: John Doe
        role:
          type: string
          enum: [admin, user, guest]
          example: user
        createdAt:
          type: string
          format: date-time
          example: "2024-12-24T14:32:15Z"
      required:
        - id
        - email
        - name

    CreateUserRequest:
      type: object
      properties:
        email:
          type: string
          format: email
        password:
          type: string
          format: password
          minLength: 8
        name:
          type: string
          minLength: 2
          maxLength: 100
      required:
        - email
        - password
        - name

    UpdateUserRequest:
      type: object
      properties:
        email:
          type: string
          format: email
        name:
          type: string
      # No campos requeridos - todos opcionales para PATCH-like behavior

    Product:
      type: object
      properties:
        id:
          type: integer
        name:
          type: string
        description:
          type: string
        price:
          type: number
          format: float
          minimum: 0
        category:
          type: string
        inStock:
          type: boolean
        images:
          type: array
          items:
            type: string
            format: uri

    Pagination:
      type: object
      properties:
        page:
          type: integer
        limit:
          type: integer
        total:
          type: integer
        totalPages:
          type: integer
        hasNextPage:
          type: boolean
        hasPreviousPage:
          type: boolean

    Error:
      type: object
      properties:
        error:
          type: object
          properties:
            code:
              type: string
            message:
              type: string
            details:
              type: array
              items:
                type: object
                properties:
                  field:
                    type: string
                  message:
                    type: string
            timestamp:
              type: string
              format: date-time

  responses:
    BadRequest:
      description: Bad request
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'

    Unauthorized:
      description: Unauthorized
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'
          example:
            error:
              code: UNAUTHORIZED
              message: Authentication required

    Forbidden:
      description: Forbidden
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'

    NotFound:
      description: Resource not found
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'

    InternalError:
      description: Internal server error
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'
```

### 11.3 Integración con Swagger UI

```javascript
// ═══════════════════════════════════════════════════════
// Setup Swagger UI en Express
// ═══════════════════════════════════════════════════════

const express = require('express');
const swaggerUi = require('swagger-ui-express');
const YAML = require('yamljs');

const app = express();

// Cargar OpenAPI spec
const swaggerDocument = YAML.load('./openapi.yaml');

// Servir Swagger UI en /api-docs
app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(swaggerDocument, {
  customCss: '.swagger-ui .topbar { display: none }',
  customSiteTitle: "My API Documentation"
}));

app.listen(3000, () => {
  console.log('Server running on http://localhost:3000');
  console.log('API Docs available at http://localhost:3000/api-docs');
});

// Ahora puedes visitar http://localhost:3000/api-docs
// y tendrás una UI interactiva para explorar tu API
```

---

## 12. Rate Limiting y Throttling

### 12.1 ¿Qué es Rate Limiting?

```
RATE LIMITING
= Limitar número de requests por usuario/IP en un tiempo determinado

Objetivos:
✅ Prevenir abuso (DDoS, scraping)
✅ Proteger recursos del servidor
✅ Garantizar fair usage
✅ Costos controlados (APIs que pagan por request)

Ejemplo:
┌─────────────────────────────────────────────────────┐
│  Usuario puede hacer:                               │
│  - 100 requests por minuto                          │
│  - 1000 requests por hora                           │
│  - 10,000 requests por día                          │
└─────────────────────────────────────────────────────┘

Estrategias comunes:
1. Fixed Window: 100 req/minuto (reinicia cada minuto)
2. Sliding Window: 100 req en últimos 60 segundos
3. Token Bucket: "Bucket" de tokens que se rellenan
4. Leaky Bucket: Requests procesados a rata constante
```

### 12.2 Implementación de Rate Limiting

```javascript
// ═══════════════════════════════════════════════════════
// Rate Limiting con express-rate-limit
// ═══════════════════════════════════════════════════════

const rateLimit = require('express-rate-limit');
const RedisStore = require('rate-limit-redis');
const Redis = require('ioredis');

const redis = new Redis();

// Rate limiter global
const globalLimiter = rateLimit({
  store: new RedisStore({
    client: redis,
    prefix: 'rl:global:'
  }),
  windowMs: 15 * 60 * 1000, // 15 minutos
  max: 100, // Máximo 100 requests
  message: {
    error: {
      code: 'RATE_LIMIT_EXCEEDED',
      message: 'Too many requests. Please try again later.'
    }
  },
  standardHeaders: true, // Incluir headers RateLimit-*
  legacyHeaders: false,  // Desactivar X-RateLimit-* headers antiguos
  handler: (req, res) => {
    res.status(429).json({
      error: {
        code: 'RATE_LIMIT_EXCEEDED',
        message: 'Too many requests. Please try again later.',
        retryAfter: req.rateLimit.resetTime
      }
    });
  }
});

// Aplicar globalmente
app.use('/api/', globalLimiter);

// ═══════════════════════════════════════════════════════
// Rate limiting específico por endpoint
// ═══════════════════════════════════════════════════════

// Login endpoint - más estricto (prevenir brute force)
const loginLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutos
  max: 5, // Solo 5 intentos
  skipSuccessfulRequests: true, // No contar requests exitosos
  message: {
    error: {
      code: 'TOO_MANY_LOGIN_ATTEMPTS',
      message: 'Too many login attempts. Please try again in 15 minutes.'
    }
  }
});

app.post('/api/auth/login', loginLimiter, async (req, res) => {
  // Login logic
});

// Crear usuario - estricto
const createUserLimiter = rateLimit({
  windowMs: 60 * 60 * 1000, // 1 hora
  max: 3, // Solo 3 usuarios nuevos por hora
  message: {
    error: {
      code: 'ACCOUNT_CREATION_LIMIT',
      message: 'Account creation limit reached. Please try again later.'
    }
  }
});

app.post('/api/users', createUserLimiter, async (req, res) => {
  // Create user logic
});

// ═══════════════════════════════════════════════════════
// Rate limiting por usuario autenticado
// ═══════════════════════════════════════════════════════

const userRateLimiter = rateLimit({
  windowMs: 60 * 1000, // 1 minuto
  max: async (req) => {
    // Diferentes límites según rol
    if (req.user?.role === 'premium') {
      return 1000; // Premium: 1000 req/min
    } else if (req.user?.role === 'basic') {
      return 100;  // Basic: 100 req/min
    }
    return 20; // No autenticado: 20 req/min
  },
  keyGenerator: (req) => {
    // Usar userId si está autenticado, sino IP
    return req.user?.id || req.ip;
  }
});

app.use('/api/', userRateLimiter);

// ═══════════════════════════════════════════════════════
// Headers de Rate Limiting (estándar)
// ═══════════════════════════════════════════════════════

// Response incluye headers:
/*
RateLimit-Limit: 100              ← Límite máximo
RateLimit-Remaining: 73           ← Requests restantes
RateLimit-Reset: 1703435535       ← Timestamp de reset

Retry-After: 60                   ← Segundos hasta poder reintentar (solo en 429)
*/

// ═══════════════════════════════════════════════════════
// Rate limiting con múltiples ventanas
// ═══════════════════════════════════════════════════════

const { RateLimiterRedis, RateLimiterMemory } = require('rate-limiter-flexible');

const rateLimiterRedis = new RateLimiterRedis({
  storeClient: redis,
  points: 100,      // Número de requests
  duration: 60,     // Por segundo
  blockDuration: 60 // Bloquear por 60 segundos si excede
});

app.use(async (req, res, next) => {
  try {
    const key = req.user?.id || req.ip;
    await rateLimiterRedis.consume(key);
    next();
  } catch (rejRes) {
    res.status(429).json({
      error: {
        code: 'RATE_LIMIT_EXCEEDED',
        message: 'Too many requests',
        retryAfter: Math.ceil(rejRes.msBeforeNext / 1000)
      }
    });
  }
});
```

### 12.3 Estrategias Avanzadas

```javascript
// ═══════════════════════════════════════════════════════
// Tiered Rate Limiting (Planes de precios)
// ═══════════════════════════════════════════════════════

const RATE_LIMITS = {
  free: { requests: 100, window: 60 * 60 },      // 100/hora
  basic: { requests: 1000, window: 60 * 60 },    // 1000/hora
  premium: { requests: 10000, window: 60 * 60 }, // 10000/hora
  enterprise: { requests: 100000, window: 60 * 60 } // Sin límite práctico
};

app.use(async (req, res, next) => {
  const plan = req.user?.plan || 'free';
  const limits = RATE_LIMITS[plan];

  // Implementar rate limiting según plan
  // ...
});

// ═══════════════════════════════════════════════════════
// Cost-based Rate Limiting
// ═══════════════════════════════════════════════════════

// Diferentes endpoints cuestan diferente
const COSTS = {
  'GET /api/users': 1,
  'GET /api/search': 5,        // Búsqueda es más costosa
  'POST /api/reports': 10,     // Generar reporte muy costoso
  'POST /api/users': 2
};

app.use(async (req, res, next) => {
  const key = `${req.method} ${req.path}`;
  const cost = COSTS[key] || 1;

  // Consume "cost" points en vez de 1
  await rateLimiter.consume(req.user.id, cost);
  next();
});

// ═══════════════════════════════════════════════════════
// Burst Allowance (Permitir picos cortos)
// ═══════════════════════════════════════════════════════

const { RateLimiterRedis } = require('rate-limiter-flexible');

// Permite burst de 20 requests, pero solo 100/minuto sostenido
const burstLimiter = new RateLimiterRedis({
  storeClient: redis,
  points: 20,       // Permite hasta 20 instantáneos
  duration: 1,      // Por segundo
});

const sustainedLimiter = new RateLimiterRedis({
  storeClient: redis,
  points: 100,      // 100 total
  duration: 60,     // Por minuto
});

app.use(async (req, res, next) => {
  try {
    await Promise.all([
      burstLimiter.consume(req.ip),
      sustainedLimiter.consume(req.ip)
    ]);
    next();
  } catch (err) {
    res.status(429).json({ error: 'Rate limit exceeded' });
  }
});
```

---

## 13. GraphQL como Alternativa

### 13.1 REST vs GraphQL

```
┌──────────────────────────────────────────────────────┐
│  REST                                                │
├──────────────────────────────────────────────────────┤
│  ✅ Múltiples endpoints                              │
│  ✅ Caching HTTP estándar                            │
│  ✅ Más simple                                       │
│  ❌ Over-fetching/Under-fetching                     │
│  ❌ Múltiples requests para datos relacionados       │
└──────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────┐
│  GraphQL                                             │
├──────────────────────────────────────────────────────┤
│  ✅ Un solo endpoint                                 │
│  ✅ Cliente pide exactamente lo que necesita         │
│  ✅ Un request para datos relacionados               │
│  ❌ Caching más complejo                             │
│  ❌ Curva de aprendizaje más alta                    │
└──────────────────────────────────────────────────────┘

CUÁNDO USAR GraphQL:
- Apps con muchas vistas diferentes
- Mobile apps (minimizar requests)
- Datos muy relacionados
- Frontend necesita flexibilidad

CUÁNDO USAR REST:
- APIs públicas simples
- CRUD straightforward
- Caching es crítico
- Equipo no familiarizado con GraphQL
```

### 13.2 Ejemplo de GraphQL

```graphql
# ═══════════════════════════════════════════════════════
# Schema Definition
# ═══════════════════════════════════════════════════════

type User {
  id: ID!
  email: String!
  name: String!
  posts: [Post!]!
  followers: [User!]!
}

type Post {
  id: ID!
  title: String!
  content: String!
  author: User!
  comments: [Comment!]!
  createdAt: DateTime!
}

type Comment {
  id: ID!
  text: String!
  author: User!
  post: Post!
}

type Query {
  # Obtener usuario
  user(id: ID!): User

  # Listar usuarios con filtros
  users(
    limit: Int = 20
    offset: Int = 0
    role: String
  ): [User!]!

  # Obtener post
  post(id: ID!): Post

  # Buscar posts
  searchPosts(query: String!): [Post!]!
}

type Mutation {
  # Crear usuario
  createUser(input: CreateUserInput!): User!

  # Actualizar usuario
  updateUser(id: ID!, input: UpdateUserInput!): User!

  # Eliminar usuario
  deleteUser(id: ID!): Boolean!

  # Crear post
  createPost(input: CreatePostInput!): Post!
}

input CreateUserInput {
  email: String!
  password: String!
  name: String!
}

input UpdateUserInput {
  email: String
  name: String
}

input CreatePostInput {
  title: String!
  content: String!
}

# ═══════════════════════════════════════════════════════
# Queries de ejemplo
# ═══════════════════════════════════════════════════════

# Query 1: Usuario con sus posts
query {
  user(id: "123") {
    id
    name
    email
    posts {
      id
      title
      createdAt
    }
  }
}

# Response:
{
  "data": {
    "user": {
      "id": "123",
      "name": "John Doe",
      "email": "john@example.com",
      "posts": [
        {
          "id": "1",
          "title": "My first post",
          "createdAt": "2024-12-24T14:32:15Z"
        },
        {
          "id": "2",
          "title": "Another post",
          "createdAt": "2024-12-23T10:00:00Z"
        }
      ]
    }
  }
}

# Query 2: Solo nombre (evita over-fetching)
query {
  user(id: "123") {
    name
  }
}

# Response:
{
  "data": {
    "user": {
      "name": "John Doe"
    }
  }
}

# Query 3: Datos relacionados en un solo request
query {
  user(id: "123") {
    name
    posts(limit: 5) {
      title
      comments {
        text
        author {
          name
        }
      }
    }
    followers {
      name
      email
    }
  }
}

# En REST, esto requeriría múltiples requests:
# GET /users/123
# GET /users/123/posts?limit=5
# GET /posts/1/comments
# GET /posts/2/comments
# ...
# GET /users/123/followers
```

---

## 14. Anti-Patrones Comunes

### 14.1 ❌ Verbos en URLs

```bash
# ❌ MAL - Verbos en URL
GET  /api/getUser?id=123
POST /api/createUser
POST /api/deleteUser
GET  /api/fetchAllProducts

# ✅ BIEN - Sustantivos + HTTP methods
GET    /api/users/123
POST   /api/users
DELETE /api/users/123
GET    /api/products
```

### 14.2 ❌ No Usar Códigos de Estado HTTP Correctos

```javascript
// ❌ MAL - Siempre retorna 200
app.post('/api/users', async (req, res) => {
  const existing = await User.findByEmail(req.body.email);
  if (existing) {
    return res.status(200).json({  // ❌ Debería ser 409
      success: false,
      error: "Email exists"
    });
  }
});

// ✅ BIEN
app.post('/api/users', async (req, res) => {
  const existing = await User.findByEmail(req.body.email);
  if (existing) {
    return res.status(409).json({
      error: {
        code: 'DUPLICATE_EMAIL',
        message: 'Email already exists'
      }
    });
  }
});
```

### 14.3 ❌ Exponer IDs de Base de Datos

```javascript
// ❌ RIESGO - Exponer auto-increment IDs
GET /api/users/1
GET /api/users/2  // ← Fácil enumerar todos los usuarios

// ✅ MEJOR - UUIDs o IDs ofuscados
GET /api/users/550e8400-e29b-41d4-a716-446655440000

// O hashids
GET /api/users/jR3qL9pK
```

### 14.4 ❌ Endpoints Inconsistentes

```bash
# ❌ MAL - Inconsistencias
GET /api/user       # Singular
GET /api/products   # Plural
GET /api/getAllOrders  # Verbo
GET /api/product-categories  # kebab-case
GET /api/orderItems          # camelCase

# ✅ BIEN - Consistente
GET /api/users
GET /api/products
GET /api/orders
GET /api/product-categories
GET /api/order-items
```

### 14.5 ❌ No Paginar Listas

```javascript
// ❌ MAL - Retorna TODO
app.get('/api/products', async (req, res) => {
  const products = await Product.findAll(); // 100,000 productos
  res.json(products); // 500MB de JSON
});

// ✅ BIEN - Siempre paginar
app.get('/api/products', async (req, res) => {
  const page = parseInt(req.query.page) || 1;
  const limit = Math.min(parseInt(req.query.limit) || 20, 100);

  const products = await Product.findAll({
    limit,
    offset: (page - 1) * limit
  });

  res.json({ data: products, pagination: {...} });
});
```

### 14.6 ❌ Mutaciones con GET

```bash
# ❌ MAL - Cambiar estado con GET
GET /api/users/123/delete
GET /api/orders/456/cancel
GET /api/products/789/activate

# ✅ BIEN - Usar métodos correctos
DELETE /api/users/123
POST   /api/orders/456/cancellation
PATCH  /api/products/789  { "active": true }
```

### 14.7 ❌ Ignorar Versionado

```bash
# ❌ MAL - Sin versión
GET /api/users

# Después: Cambias el schema → Rompes clientes

# ✅ BIEN - Con versionado
GET /api/v1/users
GET /api/v2/users  # Nueva versión con breaking changes
```

### 14.8 ❌ No Documentar API

```
❌ MAL:
- Sin documentación
- README desactualizado
- Ejemplos que no funcionan

✅ BIEN:
- OpenAPI/Swagger actualizado
- Ejemplos para cada endpoint
- Casos de error documentados
- Changelog de versiones
```

---

## Resumen y Mejores Prácticas

```
✅ DISEÑO DE URLs:
├── Usar sustantivos plurales (/users, /products)
├── kebab-case para URLs (/product-categories)
├── Recursos anidados máximo 2-3 niveles
├── Versionado en URL (/v1/, /v2/)
└── Filtros en query params (?status=active&role=admin)

✅ HTTP:
├── Usar métodos correctos (GET, POST, PUT, DELETE)
├── Códigos de estado semánticos (200, 201, 404, 409, 500)
├── Headers apropiados (Content-Type, Authorization)
└── Idempotencia para PUT y DELETE

✅ DATOS:
├── JSON como formato estándar
├── CamelCase para campos JSON
├── Paginación siempre para listas
├── Filtrado y ordenamiento flexible
└── Sparse fieldsets cuando sea útil

✅ SEGURIDAD:
├── HTTPS siempre en producción
├── Autenticación (JWT, OAuth2)
├── Autorización (RBAC, permisos)
├── Rate limiting
├── Validación de input
└── No exponer información sensible

✅ ERRORES:
├── Formato consistente de error
├── Códigos de error claros
├── Mensajes útiles para developers
├── Detalles de validación
└── Request ID para tracking

✅ DOCUMENTACIÓN:
├── OpenAPI/Swagger actualizado
├── Ejemplos de uso
├── Authentication flow
├── Rate limits documentados
└── Changelog de versiones

✅ PERFORMANCE:
├── Paginación para evitar payloads grandes
├── Caching (ETag, Cache-Control)
├── Compresión (gzip)
├── Async logging
└── Database query optimization

❌ EVITAR:
├── Verbos en URLs
├── Siempre retornar 200
├── Exponer información sensible
├── No versionar
├── Endpoints inconsistentes
├── Mutaciones con GET
├── No documentar
└── No paginar listas grandes
```

---
**Última actualización:** Diciembre 2025
**Versión:** 1.0
**Autor:** Illescas, Michael Jonathan
**Licencia:** Uso libre para aprendizaje y aplicación profesional
