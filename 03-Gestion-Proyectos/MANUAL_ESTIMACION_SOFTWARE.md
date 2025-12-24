# Manual de Estimación de Software
## Guía Profesional para Estimar Proyectos de Desarrollo

> Manual genérico aplicable a cualquier proyecto de software

---

## 📚 Índice

1. [Fundamentos de Estimación](#1-fundamentos-de-estimación)
2. [Métodos de Estimación](#2-métodos-de-estimación)
3. [Descomposición de Historias de Usuario](#3-descomposición-de-historias-de-usuario)
4. [Estimación en Horas](#4-estimación-en-horas)
5. [Story Points vs Horas](#5-story-points-vs-horas)
6. [Calibración y Mejora Continua](#6-calibración-y-mejora-continua)
7. [Errores Comunes](#7-errores-comunes)
8. [Plantillas y Herramientas](#8-plantillas-y-herramientas)

---

## 1. Fundamentos de Estimación

### 1.1 ¿Qué es una Estimación?

Una **estimación** es una predicción informada del esfuerzo, tiempo y recursos necesarios para completar una tarea.

**NO es:**
- ❌ Una promesa o compromiso
- ❌ Un número exacto
- ❌ Una fecha límite

**SÍ es:**
- ✅ Una predicción probabilística
- ✅ Un rango de valores posibles
- ✅ Una herramienta de planificación

### 1.2 Principios Fundamentales

#### Principio 1: La Incertidumbre es Inherente
```
Estimación = Conocimiento + Incertidumbre
```

Mientras más temprano en el proyecto, mayor incertidumbre:

```
Etapa del Proyecto    | Rango de Incertidumbre
---------------------|----------------------
Idea inicial          | ±400% (1x a 4x)
Requisitos definidos  | ±100% (0.5x a 2x)
Diseño completado     | ±50% (0.75x a 1.5x)
Desarrollo iniciado   | ±25% (0.85x a 1.25x)
Casi terminado        | ±10% (0.95x a 1.1x)
```

**Ejemplo:**
```
Estimación inicial: "El proyecto tomará 100 horas"
Rango real: 50-200 horas (±100%)

Estimación en desarrollo: "Faltan 20 horas"
Rango real: 17-25 horas (±25%)
```

#### Principio 2: Estimar ≠ Medir
```
Estimación: Predicción antes de hacer
Medición:   Dato real después de hacer
```

#### Principio 3: La Precisión es Costosa
```
Tiempo invertido en estimar vs Beneficio:

1 min:  Estimación muy aproximada     (±200%)
15 min: Estimación razonable          (±50%)
1 hora: Estimación detallada          (±20%)
1 día:  Estimación muy precisa        (±10%)

Punto óptimo: 15-30 minutos por historia
```

### 1.3 Objetivos de una Buena Estimación

1. **Planificación:** Decidir qué hacer y cuándo
2. **Priorización:** Comparar costo vs beneficio
3. **Asignación de recursos:** Saber cuántas personas necesitas
4. **Gestión de expectativas:** Comunicar realismo al cliente/stakeholders
5. **Detección de riesgos:** Identificar tareas complejas temprano

---

## 2. Métodos de Estimación

### 2.1 Planning Poker (Recomendado para Equipos)

**Proceso:**
1. El Product Owner presenta la historia de usuario
2. El equipo hace preguntas de clarificación
3. Cada miembro elige una carta en secreto (0, 1, 2, 3, 5, 8, 13, 21, ...)
4. Todos revelan al mismo tiempo
5. Discuten diferencias (especialmente valores extremos)
6. Repiten hasta consenso

**Valores típicos (Fibonacci):**
```
0  = Tarea trivial, ya hecha
1  = Muy simple (1-2 horas)
2  = Simple (2-4 horas)
3  = Pequeña (4-6 horas)
5  = Media (1 día)
8  = Grande (2 días)
13 = Muy grande (3-4 días)
21 = Épica (1 semana) → Descomponer
```

**Ventajas:**
- ✅ Combina experiencia de todo el equipo
- ✅ Fomenta discusión y aprendizaje
- ✅ Previene el "ancla" de la primera estimación

**Desventajas:**
- ❌ Requiere todo el equipo presente
- ❌ Puede ser lento (15-30 min por historia)

### 2.2 T-Shirt Sizing (Rápido y Aproximado)

**Escala:**
```
XS  = Extra pequeño   (< 4 horas)
S   = Pequeño         (4-8 horas / 1 día)
M   = Mediano         (8-16 horas / 1-2 días)
L   = Grande          (16-40 horas / 3-5 días)
XL  = Extra grande    (40+ horas / 1-2 semanas)
XXL = Épica           (2+ semanas) → Descomponer
```

**Cuándo usar:**
- ✓ Backlog grooming inicial
- ✓ Estimaciones muy tempranas
- ✓ Cuando necesitas velocidad sobre precisión

**Conversión a horas (ejemplo):**
```
XS → 2-4h
S  → 4-8h
M  → 8-16h
L  → 16-32h
XL → 32-64h
```

### 2.3 Analogía (Comparación con Tareas Similares)

**Proceso:**
1. Encuentra una tarea similar ya completada
2. Identifica diferencias
3. Ajusta la estimación basándote en las diferencias

**Ejemplo:**
```
Tarea de referencia:
"Crear formulario de login" = 6 horas reales

Nueva tarea:
"Crear formulario de registro"

Análisis:
+ Más campos (nombre, apellido, confirmación) → +30%
+ Validación de email único → +20%
- Ya tengo componentes reutilizables → -20%

Estimación: 6h × 1.3 = 7.8h → 7-8h
```

### 2.4 Descomposición (Bottom-Up)

**Proceso:**
1. Divide la historia en tareas técnicas específicas
2. Estima cada tarea individualmente
3. Suma todas las estimaciones
4. Agrega buffer (20-30%)

**Ejemplo:**
```
Historia: "Implementar autenticación JWT"

Tareas:
1. Instalar dependencias y configurar JWT        → 1h
2. Crear middleware de autenticación             → 2h
3. Endpoint POST /login (validar credenciales)   → 2h
4. Generar y retornar token                      → 1h
5. Proteger rutas con middleware                 → 2h
6. Manejo de expiración y refresh token          → 3h
7. Tests unitarios                               → 2h
8. Tests de integración                          → 2h
                                          Subtotal: 15h
                                     Buffer (25%): +3.75h
                                     TOTAL: 18-20h
```

### 2.5 Estimación por Expertos (Delphi)

**Proceso:**
1. Varios expertos estiman independientemente
2. Se recopilan estimaciones anónimamente
3. Se calcula promedio y se comparten resultados
4. Los expertos revisan y ajustan
5. Se repite hasta convergencia

**Ejemplo:**
```
Ronda 1:
Experto A: 8h
Experto B: 12h
Experto C: 6h
Promedio: 8.7h

Ronda 2 (después de discusión):
Experto A: 10h (consideró casos edge)
Experto B: 10h (simplificó approach)
Experto C: 9h (agregó testing)
Promedio: 9.7h → 9-10h ✓
```

---

## 3. Descomposición de Historias de Usuario

### 3.1 Criterio INVEST

Una buena historia de usuario debe ser:

```
I - Independent   (Independiente)
N - Negotiable    (Negociable)
V - Valuable      (Valiosa)
E - Estimable     (Estimable)
S - Small         (Pequeña)
T - Testable      (Testeable)
```

#### ¿Cuándo descomponer una historia?

**Señales de que una historia es MUY GRANDE:**

1. **Tiempo estimado > 3 días (24 horas)**
   ```
   ❌ "Implementar sistema de reportes completo" (80h)
   ✅ "Crear reporte de ventas diarias" (6h)
   ✅ "Crear reporte de top 10 productos" (5h)
   ✅ "Exportar reportes a PDF" (8h)
   ```

2. **Múltiples "Y" en la descripción**
   ```
   ❌ "Como usuario quiero registrarme Y verificar email Y configurar perfil"
   ✅ "Como usuario quiero registrarme con email"
   ✅ "Como usuario quiero verificar mi email"
   ✅ "Como usuario quiero configurar mi perfil"
   ```

3. **Múltiples criterios de aceptación (>5-7)**
   ```
   Si tiene 10 criterios de aceptación → Dividir en 2-3 historias
   ```

4. **Involucra múltiples roles o módulos**
   ```
   ❌ "Sistema de pedidos completo" (afecta cliente, admin, cocina, reportes)
   ✅ "Cliente puede hacer pedido"
   ✅ "Admin puede ver pedidos"
   ✅ "Admin puede cambiar estado de pedido"
   ```

5. **No cabe en un sprint**
   ```
   Si el sprint es de 2 semanas → Historia max 3 días (15% del sprint)
   ```

### 3.2 Técnicas de Descomposición

#### Técnica 1: Por Flujo de Trabajo (Workflow)

```
Historia grande:
"Sistema de checkout completo"

Descomponer por pasos del flujo:
✅ "Agregar productos al carrito"
✅ "Ver resumen del carrito"
✅ "Ingresar datos de envío"
✅ "Seleccionar método de pago"
✅ "Confirmar compra"
✅ "Ver confirmación y recibo"
```

#### Técnica 2: Por Reglas de Negocio

```
Historia grande:
"Calcular precio final del pedido"

Descomponer por reglas:
✅ "Calcular subtotal de productos"
✅ "Aplicar descuento por cantidad"
✅ "Aplicar código de cupón"
✅ "Calcular impuestos"
✅ "Agregar costo de envío"
```

#### Técnica 3: Por Operaciones CRUD

```
Historia grande:
"Gestión de productos"

Descomponer:
✅ "Crear producto"
✅ "Editar producto"
✅ "Eliminar producto"
✅ "Listar productos"
✅ "Buscar producto"
```

#### Técnica 4: Por Capas Técnicas (solo si necesario)

```
Historia grande:
"Implementar autenticación"

Descomponer:
✅ "Backend: Endpoint de login"
✅ "Backend: Middleware de autenticación"
✅ "Frontend: Formulario de login"
✅ "Frontend: Manejo de sesión"
```

⚠️ **Advertencia:** Evita dividir por capas técnicas. Prefiere división por valor de negocio.

#### Técnica 5: Por Complejidad (Spike + Implementación)

```
Historia incierta:
"Integrar sistema de pagos" (??h)

Dividir:
✅ "Spike: Investigar APIs de pago y POC" (4h)
   → Después puedes estimar con confianza
✅ "Implementar integración con Stripe" (8h)
✅ "Manejo de webhooks de pago" (6h)
```

#### Técnica 6: Por Escenarios o Variantes

```
Historia grande:
"Usuario puede buscar productos"

Descomponer:
✅ "Buscar por nombre de producto"
✅ "Buscar por categoría"
✅ "Buscar por rango de precio"
✅ "Búsqueda avanzada con filtros"
```

### 3.3 Matriz de Decisión: ¿Descomponer o No?

```
Criterio                          | Umbral        | Acción
----------------------------------|---------------|------------------
Estimación en horas               | > 24h         | Descomponer
Estimación en story points        | > 13 puntos   | Descomponer
Días de trabajo                   | > 3 días      | Descomponer
Criterios de aceptación           | > 7           | Considerar dividir
Número de "Y" en descripción      | > 2           | Descomponer
Equipo no puede estimar           | N/A           | Spike o descomponer
Múltiples roles involucrados      | > 2           | Dividir por rol
No cabe en el sprint              | N/A           | Descomponer
Incertidumbre técnica alta        | N/A           | Spike primero
```

### 3.4 Ejemplo Completo de Descomposición

**Historia Original (MUY GRANDE):**
```
"Como administrador quiero gestionar el inventario de productos
para mantener el catálogo actualizado"

Estimación inicial: 80 horas ❌
```

**Análisis:**
- Múltiples operaciones (CRUD + búsqueda + reportes)
- Múltiples criterios de aceptación (15+)
- Estimación > 3 días
- **Conclusión: DESCOMPONER**

**Descomposición (Técnica CRUD + Reglas de negocio):**

```
ÉPICA: Gestión de Inventario

US-1: "Crear producto" (6h)
  - Formulario de creación
  - Validaciones
  - Guardar en BD

US-2: "Editar producto" (5h)
  - Formulario de edición
  - Actualizar BD

US-3: "Eliminar producto" (3h)
  - Confirmación
  - Soft delete vs hard delete

US-4: "Listar productos" (6h)
  - Paginación
  - Tabla responsive

US-5: "Buscar productos" (5h)
  - Por nombre
  - Por categoría

US-6: "Subir imagen de producto" (8h)
  - Upload file
  - Validación de formato/tamaño
  - Storage

US-7: "Cambiar disponibilidad de producto" (2h)
  - Toggle activo/inactivo

US-8: "Ver historial de cambios" (10h)
  - Audit log
  - Filtros por fecha

Total descompuesto: 45h (vs 80h original)
```

**Beneficios de descomponer:**
- ✅ Estimaciones más precisas
- ✅ Mejor planificación de sprints
- ✅ Entrega incremental de valor
- ✅ Feedback temprano
- ✅ Menor riesgo por tarea

---

## 4. Estimación en Horas

### 4.1 Fórmula General

```
Tiempo Total = Tiempo de Código Base
             × Factor de Complejidad
             × Factor de Familiaridad
             + Overhead de Desarrollo
```

### 4.2 Tiempo de Código Base

**Tipos de tareas y tiempos promedio:**

```
BACKEND:
Entidad JPA simple                → 1-2h
Repository + Service básico       → 2-3h
Controller REST (CRUD)            → 2-4h
Validaciones de dominio           → 1-2h
Manejo de excepciones             → 1-2h
DTO + Mapper (MapStruct)          → 1-2h
Tests unitarios (por capa)        → 1-2h
Tests de integración              → 2-3h
Endpoint complejo con lógica      → 4-6h

FRONTEND:
Componente simple (sin estado)    → 1-2h
Componente con estado local       → 2-3h
Componente con estado global      → 3-5h
Formulario simple (3-5 campos)    → 2-3h
Formulario complejo (validaciones)→ 4-6h
Página completa (layout + lógica) → 4-8h
Hook personalizado (simple)       → 1-2h
Hook con API + estado             → 2-4h
Integración con API (CRUD)        → 2-3h
Testing de componentes            → 1-2h por componente

GENERAL:
Setup de proyecto nuevo           → 2-4h
Configuración de herramientas     → 1-3h
Investigación técnica (spike)     → 2-8h
Refactoring de código legacy      → Tiempo original × 0.5-1.0
Documentación                     → 10-20% del tiempo total
Code review                       → 10-15% del tiempo total
```

### 4.3 Factor de Complejidad

```
Complejidad Baja (×1.0):
- CRUD simple sin lógica
- Copy-paste de código existente
- Tarea muy rutinaria

Complejidad Media (×1.3-1.5):
- Lógica de negocio moderada
- 2-3 integraciones
- Validaciones estándar

Complejidad Alta (×1.5-2.0):
- Algoritmos complejos
- Múltiples integraciones
- Manejo de estados complejo
- Optimización de performance

Complejidad Muy Alta (×2.0-3.0):
- Funcionalidad nunca hecha antes
- Arquitectura nueva
- Problemas de concurrencia
- Machine Learning / IA
```

### 4.4 Factor de Familiaridad

```
Experto (×0.8):
- Tecnología usada cientos de veces
- Conoces patrones y shortcuts
- Sabes cómo debuggear rápido

Competente (×1.0):
- Tecnología usada decenas de veces
- Conoces la documentación
- Puedes resolver problemas comunes

Aprendiendo (×1.5):
- Primera vez con esta tecnología
- Necesitas consultar docs constantemente
- Trial and error frecuente

Novato (×2.0-3.0):
- Primera vez con el paradigma
- No conoces las herramientas
- Todo es nuevo
```

### 4.5 Overhead de Desarrollo

**Tiempo invisible que SIEMPRE debes agregar:**

```
Testing manual:         +20-30% del tiempo de código
Debugging:              +20-40% del tiempo de código
Refactoring:            +10-20% del tiempo de código
Code review:            +10-15% del tiempo de código
Documentación:          +5-10% del tiempo de código
Meetings/interrupciones:+10-20% del tiempo total
```

**Ejemplo de cálculo:**
```
Tiempo de código puro: 10h

+ Testing (25%):        2.5h
+ Debugging (30%):      3h
+ Refactoring (15%):    1.5h
+ Code review (10%):    1h
+ Docs (10%):           1h
+ Meetings (15%):       1.5h

= TIEMPO REAL: 20.5h (más del doble!)
```

### 4.6 Plantilla de Estimación Paso a Paso

```
1. IDENTIFICAR TIPO DE TAREA
   □ CRUD      □ Lógica compleja   □ UI/UX
   □ API       □ Testing           □ Refactoring

2. ESTIMAR TIEMPO BASE (sin overhead)
   Tiempo base: _____ horas

3. APLICAR FACTOR DE COMPLEJIDAD
   □ Baja (×1.0)    □ Media (×1.3-1.5)
   □ Alta (×1.5-2.0) □ Muy alta (×2.0-3.0)

   Tiempo ajustado: _____ horas

4. APLICAR FACTOR DE FAMILIARIDAD
   □ Experto (×0.8)     □ Competente (×1.0)
   □ Aprendiendo (×1.5) □ Novato (×2.0-3.0)

   Tiempo ajustado: _____ horas

5. AGREGAR OVERHEAD (+50-100%)
   Testing:        +____%
   Debugging:      +____%
   Refactoring:    +____%
   Code review:    +____%
   Docs:           +____%
   Meetings:       +____%

   TOTAL OVERHEAD: +____%

6. CALCULAR TIEMPO TOTAL
   Tiempo sin overhead:    _____ horas
   × (1 + overhead/100):   × _____
   = TIEMPO ESTIMADO:      _____ horas

7. AGREGAR BUFFER DE INCERTIDUMBRE (+20-30%)
   Tiempo estimado:        _____ horas
   × 1.25 (buffer 25%):    × 1.25
   = ESTIMACIÓN FINAL:     _____ horas

8. EXPRESAR COMO RANGO
   Mínimo (optimista):     _____ horas (-20%)
   Máximo (pesimista):     _____ horas (+30%)

   RANGO FINAL: _____ - _____ horas
```

### 4.7 Ejemplos Prácticos

#### Ejemplo 1: Tarea Simple

```
Tarea: "Agregar botón de eliminar a la tabla de usuarios"

1. Tipo: UI simple
2. Tiempo base: 1h
3. Complejidad: Baja (×1.0) → 1h
4. Familiaridad: Experto (×0.8) → 0.8h
5. Overhead:
   - Testing: +20% = 0.16h
   - No debugging complejo: +10% = 0.08h
   - No refactoring: 0h
   - Code review: +10% = 0.08h
   Total: 1.12h
6. Buffer: +25% → 1.4h
7. Rango: 1-2h

ESTIMACIÓN: 1-2 horas
```

#### Ejemplo 2: Tarea Media

```
Tarea: "Implementar formulario de registro con validaciones"

1. Tipo: Formulario con lógica
2. Tiempo base: 4h
3. Complejidad: Media (×1.3) → 5.2h
4. Familiaridad: Competente (×1.0) → 5.2h
5. Overhead:
   - Testing: +25% = 1.3h
   - Debugging: +30% = 1.56h
   - Refactoring: +10% = 0.52h
   - Code review: +10% = 0.52h
   Total: 9.1h
6. Buffer: +25% → 11.4h
7. Rango: 9-14h

ESTIMACIÓN: 9-14 horas
```

#### Ejemplo 3: Tarea Compleja

```
Tarea: "Implementar sistema de notificaciones en tiempo real con WebSocket"

1. Tipo: Funcionalidad compleja nueva
2. Tiempo base: 12h
3. Complejidad: Alta (×2.0) → 24h
4. Familiaridad: Aprendiendo (×1.5) → 36h
5. Overhead:
   - Testing: +30% = 10.8h
   - Debugging: +40% = 14.4h
   - Refactoring: +20% = 7.2h
   - Code review: +15% = 5.4h
   - Docs: +10% = 3.6h
   Total: 77.4h
6. Buffer: +30% → 100.6h
7. Rango: 80-120h

ESTIMACIÓN: 80-120 horas (2-3 semanas)

⚠️ ACCIÓN: Esta tarea es muy grande → DESCOMPONER
```

---

## 5. Story Points vs Horas

### 5.1 ¿Qué son los Story Points?

```
Story Points = Unidad relativa de esfuerzo

NO miden tiempo absoluto
SÍ miden complejidad + esfuerzo + incertidumbre
```

**Ventajas:**
- ✅ Menos discusión sobre tiempo exacto
- ✅ Más fácil consenso en equipo
- ✅ Abstrae diferencias de productividad individual
- ✅ Facilita velocidad de equipo

**Desventajas:**
- ❌ Requiere calibración
- ❌ No intuitivo para stakeholders
- ❌ Difícil traducir a compromisos de fecha

### 5.2 Escala Fibonacci para Story Points

```
Puntos | Complejidad | Equivalencia aproximada | Ejemplos
-------|-------------|------------------------|----------
1      | Trivial     | ~1-2 horas             | Cambiar texto, fix typo
2      | Muy simple  | ~2-4 horas             | Botón simple, validación básica
3      | Simple      | ~4-6 horas             | Formulario pequeño, CRUD simple
5      | Media       | ~1 día                 | Feature completa pequeña
8      | Grande      | ~2 días                | Feature con múltiples partes
13     | Muy grande  | ~3-4 días              | Feature compleja
21     | Épica       | ~1 semana              | → DESCOMPONER
```

### 5.3 Asignación de Story Points

**Método: Historia de Referencia**

1. Elige una historia "mediana" como referencia (ej: 5 puntos)
2. Compara todas las demás con ella:
   - Más simple? → 3 o 2 puntos
   - Similar? → 5 puntos
   - Más compleja? → 8 puntos
   - Mucho más compleja? → 13 puntos

**Ejemplo:**
```
Referencia (5 puntos):
"Crear formulario de login con validación"

Comparaciones:
"Agregar botón logout"              → Más simple → 2 puntos
"Crear formulario de registro"      → Similar → 5 puntos
"Implementar recuperar contraseña"  → Más complejo → 8 puntos
"Sistema completo de autenticación" → Mucho más → 13+ → DESCOMPONER
```

### 5.4 Conversión Story Points ↔ Horas

**NO hay conversión exacta**, pero puedes calibrar:

```
Mide tu velocidad histórica:

Sprint 1: 25 puntos completados en 80 horas
Sprint 2: 30 puntos completados in 90 horas
Sprint 3: 28 puntos completados in 85 horas

Promedio: 27.7 puntos en 85 horas
Ratio: ~3 horas por punto

Conversión (solo para tu equipo):
1 punto   ≈ 3 horas
5 puntos  ≈ 15 horas (2 días)
8 puntos  ≈ 24 horas (3 días)
13 puntos ≈ 39 horas (5 días)
```

⚠️ **Importante:** Este ratio es específico de tu equipo. NO lo compares con otros equipos.

### 5.5 Cuándo Usar Qué

```
USA STORY POINTS cuando:
✓ Equipo Scrum maduro
✓ Sprints regulares
✓ Quieres medir velocidad de equipo
✓ Stakeholders entienden la metodología

USA HORAS cuando:
✓ Equipo pequeño o solo
✓ Necesitas comprometer fechas
✓ Stakeholders no técnicos
✓ Proyectos de precio fijo
✓ Facturación por hora
```

---

## 6. Calibración y Mejora Continua

### 6.1 Tracking de Estimaciones

**Plantilla de Log:**
```
| US | Descripción | Estimado | Real | Diferencia | Razón |
|----|-------------|----------|------|------------|-------|
| 01 | Login form  | 6h       | 8h   | +33%       | Olvidé validaciones extras |
| 02 | API users   | 8h       | 7h   | -12%       | Más simple de lo pensado |
| 03 | Dashboard   | 12h      | 18h  | +50%       | No consideré responsive |
```

### 6.2 Métricas de Precisión

```
Precisión de Estimación = |Real - Estimado| / Real × 100

Ejemplo:
Estimado: 10h
Real: 12h
Precisión: |12 - 10| / 12 × 100 = 16.7% de error

Objetivos:
Excelente:  < 15% de error
Bueno:      15-30% de error
Aceptable:  30-50% de error
Malo:       > 50% de error → Mejorar proceso
```

### 6.3 Retrospectiva de Estimaciones

**Preguntas clave al final del sprint:**

```
1. ¿Qué subestimamos? ¿Por qué?
   - ¿Faltó considerar testing?
   - ¿Apareció complejidad oculta?
   - ¿Problemas técnicos inesperados?

2. ¿Qué sobreestimamos? ¿Por qué?
   - ¿Fue más simple de lo pensado?
   - ¿Reutilizamos código?
   - ¿Evitamos problemas anticipados?

3. ¿Qué aprendimos?
   - Nuevos factores a considerar
   - Ajustes en factores de complejidad
   - Mejoras en descomposición

4. ¿Qué cambiaremos?
   - Actualizar plantillas
   - Nuevos criterios
   - Training del equipo
```

### 6.4 Curva de Aprendizaje

```
Sprint    | Error Promedio | Observaciones
----------|---------------|------------------
Sprint 1  | 60%           | Primera vez estimando
Sprint 2  | 45%           | Mejor descomposición
Sprint 3  | 35%           | Calibrando factores
Sprint 4  | 25%           | Velocidad estable
Sprint 5+ | 15-20%        | Equipo maduro

Tiempo para madurez: 3-6 meses (6-12 sprints)
```

---

## 7. Errores Comunes

### Error 1: Estimar Solo el "Happy Path"

❌ **Mal:**
```
"Implementar login: 3 horas"
(Solo considera el caso donde todo funciona)
```

✅ **Bien:**
```
"Implementar login: 6-8 horas"
  - Happy path: 3h
  - Validaciones: 1h
  - Manejo de errores: 1h
  - Testing: 1h
  - Edge cases: 1h
```

### Error 2: No Considerar Contexto Switching

❌ **Mal:**
```
6 horas de estimación = 6 horas de calendario
```

✅ **Bien:**
```
6 horas de trabajo focalizado
+ Reuniones: 1h
+ Interrupciones: 1h
+ Email/Slack: 0.5h
= 8.5 horas de calendario

Productividad real: 60-75% del tiempo disponible
```

### Error 3: Ancla en la Primera Estimación

❌ **Mal:**
```
Jefe: "¿Cuánto tardará?"
Dev: "Hmm, 8 horas"
(Sesgo: el primero en hablar influencia a todos)
```

✅ **Bien:**
```
Usar Planning Poker para evitar ancla:
- Todos estiman en secreto
- Revelan al mismo tiempo
- Discuten diferencias
```

### Error 4: Confundir Esfuerzo con Duración

❌ **Mal:**
```
"Esta tarea es 40 horas, así que tomará 1 semana"
(Asume 100% de disponibilidad)
```

✅ **Bien:**
```
Esfuerzo: 40 horas de trabajo
Disponibilidad: 60% (reuniones, interrupciones)
Duración: 40h / (8h/día × 0.6) = 8.3 días ≈ 2 semanas
```

### Error 5: No Actualizar Estimaciones

❌ **Mal:**
```
Estimación inicial: 8h
(Descubres complejidad oculta, pero no actualizas)
Tiempo real: 16h
(Sorpresa para todos)
```

✅ **Bien:**
```
Estimación inicial: 8h
Descubrimiento día 2: "Necesito refactorizar primero"
Actualización: 8h → 14h
Comunicación: Aviso al equipo inmediatamente
```

### Error 6: Estimar sin Entender

❌ **Mal:**
```
PO: "¿Cuánto para implementar OAuth?"
Dev: "Ehh, 10 horas?"
(Sin hacer preguntas, sin entender requisitos)
```

✅ **Bien:**
```
Dev: "Antes de estimar, necesito saber:
  - ¿Qué providers? (Google, Facebook, ambos?)
  - ¿Tenemos cuenta de developer?
  - ¿Qué flujo? (popup, redirect, etc.)
  - ¿Qué hacemos con cuentas existentes?"

(Después de clarificar)
Dev: "Con Google y redirect: 8-10h"
```

### Error 7: Presión para Reducir Estimación

❌ **Mal:**
```
Dev: "Esto es 20 horas"
Jefe: "Tenemos que hacerlo en 10"
Dev: "Ok, 10 horas entonces"
(Promesa imposible de cumplir)
```

✅ **Bien:**
```
Dev: "Esto es 20 horas. Puedo reducirlo a 15h si:
  - Omitimos tests automatizados (riesgo)
  - Simplificamos la UI (menor UX)
  - Usamos librería X (deuda técnica)

¿Qué prefieres sacrificar?"
```

---

## 8. Plantillas y Herramientas

### 8.1 Template: Estimación de Historia de Usuario

```markdown
# [US-XXX] Título de la Historia

## Descripción
Como [rol]
Quiero [funcionalidad]
Para [beneficio]

## Criterios de Aceptación
- [ ] Criterio 1
- [ ] Criterio 2
- [ ] Criterio 3

## Descomposición Técnica
### Frontend
- [ ] Componente X (2h)
- [ ] Integración con API (1h)
- [ ] Manejo de errores (1h)

### Backend
- [ ] Endpoint Y (3h)
- [ ] Validaciones (1h)
- [ ] Tests (2h)

### Otros
- [ ] Documentación (1h)
- [ ] Code review (1h)

## Estimación

| Concepto | Tiempo |
|----------|--------|
| Tiempo base | 10h |
| Factor complejidad (×1.3) | 13h |
| Factor familiaridad (×1.0) | 13h |
| Overhead (+50%) | +6.5h |
| Buffer (+25%) | +4.9h |
| **TOTAL** | **24.4h** |

**Rango final:** 20-28 horas

## Incertidumbres y Riesgos
- [ ] Dependencia de API externa (riesgo: API no documentada)
- [ ] Primera vez usando librería X (spike necesario: 2h)

## Notas
- Requiere sync con equipo de backend el día 1
- Puede bloquear US-YYY si no se completa
```

### 8.2 Template: Retrospectiva de Estimaciones

```markdown
# Retrospectiva de Estimaciones - Sprint N

## Métricas Generales

| Métrica | Valor |
|---------|-------|
| Historias completadas | X/Y |
| Horas estimadas | A |
| Horas reales | B |
| Precisión promedio | C% |
| Velocidad (story points) | D |

## Análisis por Historia

| US | Estimado | Real | Diferencia | Razón |
|----|----------|------|------------|-------|
| 01 | 6h | 8h | +33% | Validaciones extras |
| 02 | 12h | 10h | -17% | Reutilizamos componente |
| 03 | 8h | 16h | +100% | Refactoring no planeado |

## Subestimaciones

### US-03: [Título]
**Estimado:** 8h
**Real:** 16h
**Razón:** No consideramos necesidad de refactoring
**Aprendizaje:** Revisar deuda técnica antes de estimar
**Acción:** Agregar "análisis de deuda técnica" al checklist

## Sobreestimaciones

### US-02: [Título]
**Estimado:** 12h
**Real:** 10h
**Razón:** Encontramos componente reutilizable
**Aprendizaje:** Auditar código existente antes de estimar
**Acción:** Agregar "buscar código reutilizable" al checklist

## Acciones de Mejora

- [ ] Acción 1: [Descripción] - Responsable: [Nombre]
- [ ] Acción 2: [Descripción] - Responsable: [Nombre]
- [ ] Acción 3: [Descripción] - Responsable: [Nombre]

## Ajustes para Próximo Sprint

- Factor de complejidad para [tipo de tarea]: ×1.3 → ×1.5
- Buffer general: 25% → 30%
- Overhead de testing: 20% → 25%
```

### 8.3 Checklist: Antes de Estimar

```markdown
## Checklist de Estimación

### Entendimiento
- [ ] ¿Leí y entendí la historia de usuario?
- [ ] ¿Entiendo TODOS los criterios de aceptación?
- [ ] ¿Hice preguntas de clarificación?
- [ ] ¿Revisé mockups/diseños si existen?
- [ ] ¿Entiendo el contexto de negocio?

### Análisis Técnico
- [ ] ¿Identifiqué todas las capas afectadas? (Frontend, Backend, DB)
- [ ] ¿Revisé si existe código similar reutilizable?
- [ ] ¿Identifiqué dependencias con otras historias?
- [ ] ¿Consideré deuda técnica existente?
- [ ] ¿Hay incertidumbres técnicas? (¿Necesito un spike?)

### Descomposición
- [ ] ¿Descompuse en tareas técnicas específicas?
- [ ] ¿Cada tarea es < 4 horas?
- [ ] ¿Consideré setup inicial?
- [ ] ¿Consideré testing?
- [ ] ¿Consideré manejo de errores?
- [ ] ¿Consideré casos edge?
- [ ] ¿Consideré documentación?
- [ ] ¿Consideré code review?

### Factores
- [ ] ¿Apliqué factor de complejidad?
- [ ] ¿Apliqué factor de familiaridad?
- [ ] ¿Agregué overhead (testing, debugging, etc.)?
- [ ] ¿Agregué buffer de incertidumbre (20-30%)?

### Validación
- [ ] ¿La estimación es un rango, no un número exacto?
- [ ] ¿Es > 24h? → Considerar descomponer
- [ ] ¿Cabe en el sprint actual?
- [ ] ¿Otro miembro del equipo revisó la estimación?
- [ ] ¿Documenté assumptions y riesgos?

### Comunicación
- [ ] ¿Comuniqué incertidumbres al equipo?
- [ ] ¿Identifiqué puntos de sincronización necesarios?
- [ ] ¿Actualicé la historia con la estimación?
```

### 8.4 Herramientas Recomendadas

```
ESTIMACIÓN EN EQUIPO:
- Planning Poker Online: scrum-poker-online.org
- Jira Poker: Integrado en Jira
- PlanITPoker: planitpoker.com

TRACKING:
- Jira / Azure DevOps / Linear
- Trello con Power-Ups de estimación
- Google Sheets (template personalizado)

RETROSPECTIVAS:
- Retrium: retrium.com
- FunRetro: funretro.io
- Miro con template de retro

ANÁLISIS DE DATOS:
- Jira Reports (Velocity, Burndown)
- Google Sheets para análisis custom
- Power BI / Tableau para dashboards
```

---

## 9. Casos de Estudio

### Caso 1: Startup con Equipo Pequeño

**Contexto:**
- Equipo: 2 developers full-stack
- Proyecto: MVP de app móvil
- Timeline: 3 meses

**Approach:**
```
Método: T-Shirt Sizing + Horas
Razón: Necesitan comprometer fechas con inversores

Proceso:
1. Backlog inicial en T-Shirt sizes (rápido)
2. Priorización con stakeholders
3. Top 20 historias → Estimación en horas detallada
4. Planning semanal (no sprints formales)
5. Retrospectiva mensual de estimaciones

Resultado:
- Error inicial: 50%
- Error después de 2 meses: 25%
- Entregaron MVP a tiempo
```

### Caso 2: Empresa Mediana con Scrum

**Contexto:**
- Equipo: 6 developers + 1 QA
- Proyecto: Refactoring de sistema legacy
- Timeline: 12 meses

**Approach:**
```
Método: Story Points (Fibonacci) + Velocity
Razón: Equipo maduro, necesitan predecibilidad

Proceso:
1. Planning Poker en refinement semanal
2. Historia de referencia: "Migrar módulo X" = 5 puntos
3. Sprints de 2 semanas
4. Medir velocidad cada sprint
5. Ajustar capacity planning basado en velocidad

Resultado:
- Velocidad estabilizó en sprint 4: 35-40 puntos/sprint
- Error de estimación: 15-20%
- Completaron refactoring 1 mes antes
```

### Caso 3: Freelancer Individual

**Contexto:**
- 1 developer full-stack
- Proyectos de clientes variados
- Facturación por hora

**Approach:**
```
Método: Horas con buffer agresivo
Razón: Compromiso contractual, protección de margen

Proceso:
1. Descomposición detallada de tareas
2. Estimación base por tarea
3. Factor de familiaridad honesto
4. Buffer del 50% (riesgo individual alto)
5. Comunicar rango al cliente
6. Tracking estricto de tiempo real vs estimado

Resultado:
- Primeros proyectos: 40% de error
- Después de 10 proyectos: 20% de error
- Rentabilidad mejoró 30%
```

---

## 10. Referencias y Recursos

### Libros Recomendados
```
- "Software Estimation: Demystifying the Black Art" - Steve McConnell
- "The Mythical Man-Month" - Frederick P. Brooks Jr.
- "User Stories Applied" - Mike Cohn
- "Agile Estimating and Planning" - Mike Cohn
```

### Artículos
```
- Cone of Uncertainty (construx.com)
- #NoEstimates movement
- Evidence-Based Scheduling (Joel Spolsky)
```

### Prácticas Recomendadas
```
✓ Estima en equipo, no solo
✓ Usa rangos, no números exactos
✓ Descompón historias grandes (>3 días)
✓ Mide y aprende de tus errores
✓ Comunica incertidumbres honestamente
✓ Re-estima cuando aparece nueva información
✓ Agrega buffer siempre (20-30%)
```

---

## Resumen Ejecutivo

### Las 10 Reglas de Oro de la Estimación

1. **Estima en rangos, nunca en números exactos**
   - ❌ "Esto es exactamente 8 horas"
   - ✅ "Esto es entre 6 y 10 horas"

2. **Descompón hasta que cada tarea sea < 1 día**
   - Si no puedes estimarla con confianza → Divide más

3. **Siempre agrega overhead invisible (50-100%)**
   - Testing, debugging, meetings, interrupciones

4. **Una estimación > 3 días es una épica → Descomponer**
   - Máximo por historia: 24 horas (3 días)

5. **Estima en equipo cuando sea posible**
   - Planning Poker > Estimación individual

6. **Usa historias de referencia**
   - "Esta es similar a X que tomó Y horas, pero con Z diferencia"

7. **Mide para mejorar**
   - Tracking: Estimado vs Real
   - Retrospectiva de estimaciones cada sprint

8. **Comunica incertidumbres y assumptions**
   - "Esto es 8h asumiendo que la API ya está lista"

9. **Re-estima cuando aprendes algo nuevo**
   - No te cases con estimación inicial si descubres complejidad

10. **La precisión perfecta no existe**
    - Objetivo: 15-30% de error
    - Aceptable: < 50% de error

---

**Última actualización:** Diciembre 2025
**Versión:** 1.0
**Autor:** Illescas, Michael Jonathan
**Licencia:** Uso libre para aprendizaje y aplicación profesional
