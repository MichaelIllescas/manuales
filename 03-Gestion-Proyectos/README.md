# 📊 Gestión de Proyectos de Software

Esta carpeta contiene los manuales sobre **estimación, planificación y gestión** de proyectos de desarrollo de software.

## 📖 Manuales Incluidos

### 1. MANUAL_ESTIMACION_SOFTWARE.md
Técnicas y métodos para estimar **esfuerzo** en proyectos de software - Story Points, Planning Poker, Estimación por Analogía, Three-Point Estimation, etc.

**Cuándo leer:** Al inicio de proyectos o sprints para estimar esfuerzo en horas/puntos.

### 2. MANUAL_COSTOS_Y_PRICING.md
Guía completa para calcular **costos económicos**, definir tarifas y establecer precios - Modelos de pricing, cálculo de tarifas por hora, presupuestos, negociación.

**Cuándo leer:** Cuando necesites definir cuánto cobrar por un proyecto o servicio, crear presupuestos profesionales, o negociar con clientes.

### 3. CHEATSHEET_ESTIMACION.md
Hoja de referencia rápida para estimaciones de esfuerzo - Fórmulas, factores, ejemplos prácticos.

**Cuándo leer:** Durante sesiones de planning poker o cuando necesites una referencia rápida de estimación.

---

## 🎯 Orden de Lectura Recomendado

### Para Estimar y Cotizar un Proyecto:
1. **MANUAL_ESTIMACION_SOFTWARE.md** - Aprende a estimar el esfuerzo (horas/puntos)
2. **MANUAL_COSTOS_Y_PRICING.md** - Convierte ese esfuerzo en precio y crea presupuestos
3. **CHEATSHEET_ESTIMACION.md** - Referencia rápida durante el trabajo

### Por Rol:

**Freelancers / Consultores:**
1. MANUAL_COSTOS_Y_PRICING.md (prioridad: cómo cobrar)
2. MANUAL_ESTIMACION_SOFTWARE.md (para estimar esfuerzo)

**Tech Leads / PMs:**
1. MANUAL_ESTIMACION_SOFTWARE.md (prioridad: planificación)
2. MANUAL_COSTOS_Y_PRICING.md (para presupuestos al cliente)

---

## 🔗 Relación con Otras Carpetas

- **01-Fundamentos/**: Código bien diseñado (SOLID, patterns) es más predecible y fácil de estimar
- **02-Arquitectura/**: La arquitectura elegida afecta complejidad y esfuerzo:
  - Clean Architecture: Mayor esfuerzo inicial, menor mantenimiento
  - Microservicios: Mayor complejidad, más tiempo de desarrollo
  - DDD: Inversión en modelado upfront

---

## 💡 Tips Rápidos

### Flujo Completo: De la Idea al Presupuesto

```
1. Cliente te contacta con una idea
   ↓
2. Reúnes requisitos (qué necesita)
   ↓
3. Estimas ESFUERZO usando MANUAL_ESTIMACION_SOFTWARE
   Resultado: "Este proyecto tomará 150-200 horas"
   ↓
4. Calculas PRECIO usando MANUAL_COSTOS_Y_PRICING
   Resultado: "150h × $80/hora × 1.4 (riesgo) = $16,800"
   ↓
5. Presentas presupuesto profesional al cliente
   ↓
6. Negocian (si es necesario)
   ↓
7. ¡Firman contrato y arrancan!
```

### Factores que Aumentan Estimaciones (Esfuerzo):
- ❌ Requisitos poco claros
- ❌ Tecnología nueva para el equipo
- ❌ Arquitectura compleja (microservicios sin experiencia)
- ❌ Deuda técnica alta
- ❌ Dependencias externas no controladas

### Factores que Aumentan Precios (Costos):
- ❌ Urgencia (necesito esto para ayer)
- ❌ Alto riesgo / incertidumbre
- ❌ Cliente difícil o indeciso
- ❌ Cambios de alcance frecuentes
- ❌ Tecnologías especializadas

### Factores que Reducen Estimaciones:
- ✅ Requisitos claros y estables
- ✅ Equipo experimentado con la stack
- ✅ Código bien diseñado (SOLID, Clean Architecture)
- ✅ Buena cobertura de tests
- ✅ CI/CD maduro

### Cómo Aumentar tus Tarifas:
- ✅ Especialízate en un nicho específico
- ✅ Construye portfolio con casos de éxito
- ✅ Cobra por valor, no solo por tiempo
- ✅ Sube precios cada 12-18 meses
- ✅ Rechaza proyectos baratos (atraes mejores clientes)

---

## 📚 Contenido Futuro Planeado

- Manual de Metodologías Ágiles (Scrum, Kanban)
- Manual de Gestión de Riesgos
- Manual de Métricas de Software
- Checklist de Arquitecto de Software

---

**Nivel:** Todos los niveles
**Audiencia:** Product Owners, Scrum Masters, Tech Leads, Desarrolladores
**Prerequisitos:** Ninguno (conceptos aplicables a cualquier nivel)
