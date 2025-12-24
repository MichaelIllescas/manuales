# 🏛️ Arquitectura de Software

Esta carpeta contiene los manuales sobre **estilos y patrones arquitectónicos** para diseñar sistemas escalables, mantenibles y robustos.

## 📖 Manuales Incluidos

### 1. MANUAL_ARQUITECTURA_SOFTWARE.md
Visión general de arquitectura de software - Conceptos, estilos arquitectónicos, decisiones arquitectónicas.

**Cuándo leer:** Al inicio de cualquier proyecto para entender las opciones disponibles.

### 2. MANUAL_CLEAN_ARCHITECTURE.md
Clean Architecture (Uncle Bob) - Arquitectura limpia con capas concéntricas: Entities, Use Cases, Interface Adapters, Frameworks & Drivers.

**Cuándo leer:** Cuando necesites separar lógica de negocio de detalles técnicos.

### 3. MANUAL_ARQUITECTURA_HEXAGONAL.md
Arquitectura Hexagonal (Ports & Adapters) - Aislar el dominio de la infraestructura mediante puertos y adaptadores.

**Cuándo leer:** Cuando necesites flexibilidad para cambiar tecnologías sin afectar el core de negocio.

### 4. MANUAL_DDD.md
Domain-Driven Design - Modelado centrado en el dominio de negocio con Bounded Contexts, Entities, Value Objects, Aggregates.

**Cuándo leer:** En proyectos con lógica de negocio compleja y necesidad de colaboración cercana con expertos del dominio.

### 5. MANUAL_MICROSERVICIOS.md
Arquitectura de Microservicios - Servicios pequeños, autónomos y desplegables independientemente.

**Cuándo leer:** Cuando necesites escalar partes específicas del sistema o trabajar con múltiples equipos.

---

## 🎯 Orden de Lectura Recomendado

### Para Principiantes:
1. **MANUAL_ARQUITECTURA_SOFTWARE.md** - Panorama general
2. **MANUAL_CLEAN_ARCHITECTURE.md** - Fundamentos de separación en capas
3. **MANUAL_ARQUITECTURA_HEXAGONAL.md** - Profundización en Ports & Adapters

### Para Sistemas Complejos:
1. **MANUAL_DDD.md** - Modelado del dominio
2. **MANUAL_ARQUITECTURA_HEXAGONAL.md** - Estructura técnica
3. **MANUAL_MICROSERVICIOS.md** - Si necesitas escalar

---

## 🔗 Relación Entre Manuales

```
MANUAL_ARQUITECTURA_SOFTWARE (conceptos generales)
    ↓
MANUAL_CLEAN_ARCHITECTURE (capas + dependencias)
    ↓
MANUAL_ARQUITECTURA_HEXAGONAL (implementación práctica)
    ↓
MANUAL_DDD (modelado del dominio)
    ↓
MANUAL_MICROSERVICIOS (distribución del sistema)
```

**Compatibilidad:**
- Clean Architecture + Hexagonal + DDD = Combinación perfecta
- Microservicios puede usar Clean/Hexagonal/DDD en cada servicio

---

## 🔗 Relación con Otras Carpetas

- **01-Fundamentos/**: SOLID y patrones son la base de estas arquitecturas
- **03-Gestion-Proyectos/**: La arquitectura elegida afecta complejidad y estimaciones

---

## 📊 Matriz de Decisión Rápida

| Necesidad | Arquitectura Recomendada |
|-----------|-------------------------|
| Sistema simple CRUD | Monolito con Clean Architecture |
| Lógica de negocio compleja | Clean + Hexagonal + DDD |
| Múltiples equipos | Microservicios |
| Flexibilidad tecnológica | Hexagonal |
| Colaboración con expertos de negocio | DDD |
| Escalabilidad extrema | Microservicios |

---

**Nivel:** Intermedio a Avanzado
**Audiencia:** Arquitectos de software, Tech Leads, Desarrolladores Senior
**Prerequisitos:**
- Fundamentos de SOLID y Design Patterns
- Experiencia en desarrollo de software
