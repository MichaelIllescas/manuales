# Cheat Sheet - Estimación de Software
> Referencia rápida para consulta diaria

---

## 🎯 Checklist Rápido de Estimación (2 minutos)

```
□ ¿Entiendo completamente la tarea?
□ ¿La descompuse en subtareas < 4h?
□ ¿Consideré testing + debugging + code review?
□ ¿Apliqué factor de complejidad?
□ ¿Apliqué factor de familiaridad?
□ ¿Agregué buffer 20-30%?
□ ¿Es > 24h? → DESCOMPONER
□ ¿La expresé como rango?
```

---

## ⚡ Estimación Express (30 segundos)

### Comparación Rápida
```
¿Esta tarea es...?

Similar a [tarea conocida]:     → Mismo tiempo
Mitad de compleja:              → Dividir por 2
Doble de compleja:              → Multiplicar por 2
Primera vez haciendo esto:      → Multiplicar por 1.5-2.0
Ya hice esto 10 veces:          → Multiplicar por 0.8
```

### Tiempos Base Comunes
```
Botón simple:                   1-2h
Formulario simple (3 campos):   2-3h
Formulario complejo:            4-6h
Componente con estado:          3-5h
Página completa:                4-8h
CRUD simple (backend):          6-8h
Endpoint con lógica compleja:   4-6h
Feature completa pequeña:       1-2 días
Feature completa media:         2-4 días
Feature completa grande:        1 semana → DESCOMPONER
```

---

## 📏 Reglas de Tamaño

### Criterios de Descomposición

```
DESCOMPONER SI:
❌ Estimación > 24h (3 días)
❌ Más de 2 "Y" en la descripción
❌ Más de 7 criterios de aceptación
❌ Involucra múltiples roles/módulos
❌ No cabe en el sprint
❌ No puedes estimar con confianza

ESTÁ BIEN SI:
✅ Estimación: 4-24h
✅ 1-7 criterios de aceptación
✅ Una sola funcionalidad clara
✅ Cabe cómodamente en el sprint
✅ Equipo puede estimar con consenso
```

---

## 🧮 Fórmula Rápida

```
Tiempo Total = Base × Complejidad × Familiaridad × 1.75

Donde:
Base         = Tiempo de código puro
Complejidad  = 1.0 (simple) a 2.0 (muy compleja)
Familiaridad = 0.8 (experto) a 2.0 (novato)
1.75         = Overhead promedio (testing, debugging, etc.)

Ejemplo:
4h × 1.3 (media) × 1.0 (competente) × 1.75 = 9.1h
→ Estimar: 8-10h
```

---

## 🎨 Tabla de Conversión

### T-Shirt Sizing → Horas

| Talla | Horas | Días | Story Points | Ejemplo |
|-------|-------|------|--------------|---------|
| XS | 1-4h | 0.5 | 1-2 | Fix typo, cambiar texto |
| S | 4-8h | 1 | 3 | Botón, validación simple |
| M | 8-16h | 1-2 | 5 | Formulario, CRUD simple |
| L | 16-32h | 2-4 | 8 | Feature pequeña completa |
| XL | 32-64h | 4-8 | 13 | Feature compleja |
| XXL | 64+h | 8+ | 21+ | → DESCOMPONER |

---

## 🔍 Factores Multiplicadores

### Complejidad
```
×1.0    CRUD simple, sin lógica
×1.3    Lógica de negocio moderada, 2-3 integraciones
×1.5    Lógica compleja, múltiples integraciones
×2.0    Algoritmos complejos, arquitectura nueva
×3.0    Nunca hecho antes, muy incierto → SPIKE primero
```

### Familiaridad
```
×0.8    Experto (hecho 50+ veces)
×1.0    Competente (hecho 10-50 veces)
×1.5    Aprendiendo (hecho 1-5 veces)
×2.0    Novato (primera vez)
```

### Overhead (agregar siempre)
```
Testing:            +20-30%
Debugging:          +20-40%
Code review:        +10-15%
Refactoring:        +10-20%
Documentación:      +5-10%
Meetings:           +10-20%
------------------------------
TOTAL OVERHEAD:     +75-135%

Promedio seguro:    +75% (×1.75)
```

---

## 📊 Matriz de Decisión Rápida

```
Puntos a sumar:

Base CRUD:                                  2-3h
+ Lógica de negocio compleja:              +2-4h
+ Cada integración externa:                +1-2h
+ Diseño responsive:                       +30% del total
+ Primera vez con tecnología:              ×1.5-2.0
+ Testing automatizado complejo:           +1-3h
+ Refactoring de código legacy:            +50% del total
```

---

## 🚨 Red Flags

### Señales de Estimación Peligrosa

```
🚩 "Esto es fácil, 2 horas"
   → Probablemente subestimaste

🚩 Estimaste sin hacer preguntas
   → No entiendes el problema

🚩 Solo consideraste el happy path
   → Falta manejo de errores, edge cases

🚩 Primera estimación que dijiste
   → Probablemente ancla, no análisis

🚩 "Entre 4 y 80 horas"
   → Rango muy amplio = no entiendes la tarea

🚩 Te presionaron a reducir estimación
   → Está comprometida la calidad
```

---

## 💡 Tips Rápidos

### Antes de Estimar
```
1. Lee TODOS los criterios de aceptación
2. Pregunta lo que no entiendas
3. Busca código similar existente
4. Identifica dependencias
```

### Durante la Estimación
```
1. Descompón en tareas específicas
2. Estima cada tarea
3. Suma y multiplica por 1.75
4. Redondea a rango
```

### Después de Estimar
```
1. Valida con alguien más si es posible
2. Documenta assumptions
3. Comunica incertidumbres
4. Re-estima si descubres algo nuevo
```

---

## 🎯 Ejemplos Rápidos

### Ejemplo 1: Tarea Simple
```
"Agregar botón de logout"

Base:           1h (HTML + onClick)
Complejidad:    ×1.0 (trivial)
Familiaridad:   ×0.8 (ya lo hice 100 veces)
Overhead:       ×1.5 (testing ligero)

Cálculo: 1h × 1.0 × 0.8 × 1.5 = 1.2h
Rango: 1-2h
```

### Ejemplo 2: Tarea Media
```
"Formulario de registro con validaciones"

Descomposición:
- HTML form:               1h
- Validaciones client:     1h
- Integrar API:            1h
- Manejo de errores:       1h
- Tests:                   1h
Base:                      5h

Complejidad:    ×1.2 (validaciones moderadas)
Familiaridad:   ×1.0 (competente)
Overhead:       ×1.75

Cálculo: 5h × 1.2 × 1.0 × 1.75 = 10.5h
Rango: 9-12h
```

### Ejemplo 3: Tarea Compleja
```
"Implementar sistema de notificaciones en tiempo real"

Base estimado:              12h
Complejidad:    ×2.0 (WebSocket, muy complejo)
Familiaridad:   ×1.5 (primera vez con WS)
Overhead:       ×2.0 (testing complejo, debugging)

Cálculo: 12h × 2.0 × 1.5 × 2.0 = 72h

⚠️ MUY GRANDE (>24h) → DESCOMPONER

Descomponer:
- Spike: Investigar WebSocket (4h)
- Backend: Setup WS server (8h)
- Backend: Endpoints de notificaciones (6h)
- Frontend: Cliente WebSocket (6h)
- Frontend: UI de notificaciones (6h)
- Testing e integración (8h)
Total: 38h en 6 historias
```

---

## 📝 Template de Estimación (1 minuto)

```
Tarea: _________________________________

Descomposición:
□ ___________________ ___h
□ ___________________ ___h
□ ___________________ ___h
                Base: ___h

Factores:
Complejidad:    ×_____ (1.0-2.0)
Familiaridad:   ×_____ (0.8-2.0)
Overhead:       ×_____ (1.5-2.0)

Cálculo:
___h × ___ × ___ × ___ = ___h

Rango: ___-___h

Incertidumbres:
_________________________________
_________________________________
```

---

## 🔢 Conversión Story Points

### Calibración de Tu Equipo

```
Mide velocidad de 3-5 sprints:

Sprint 1: ___ puntos en ___ horas
Sprint 2: ___ puntos en ___ horas
Sprint 3: ___ puntos en ___ horas

Promedio: ___ puntos en ___ horas
Ratio: 1 punto = ___ horas

Ejemplo:
Sprint 1: 25 pts en 80h
Sprint 2: 30 pts en 90h
Sprint 3: 28 pts en 85h
Promedio: 27.7 pts en 85h
→ 1 punto ≈ 3 horas (solo para tu equipo)
```

---

## ⚖️ Precisión de Estimación

### Objetivo de Precisión

```
| Precisión | Error | Estado |
|-----------|-------|--------|
| Excelente | <15%  | 🟢 Seguir así |
| Buena     | 15-30%| 🟡 Mejorar |
| Aceptable | 30-50%| 🟠 Revisar proceso |
| Mala      | >50%  | 🔴 Cambiar approach |

Fórmula:
Error = |Real - Estimado| / Real × 100
```

---

## 🎓 Las 5 Leyes de la Estimación

1. **Ley de Hofstadter**
   > "Siempre toma más tiempo del estimado, incluso cuando consideras la Ley de Hofstadter"
   → Agrega buffer 20-30%

2. **Ley de Brooks**
   > "Agregar personas a un proyecto retrasado lo retrasa más"
   → No dividas estimación entre más personas linealmente

3. **Ley de Parkinson**
   > "El trabajo se expande para llenar el tiempo disponible"
   → No sobre-estimes para tener margen

4. **Regla 90-90**
   > "Los primeros 90% del código toma 90% del tiempo. El 10% restante toma el otro 90%"
   → Los últimos detalles son caros

5. **Principio de Peter**
   > "En toda jerarquía, uno tiende a ascender hasta su nivel de incompetencia"
   → No estimes fuera de tu zona de competencia sin indicarlo

---

## 🚀 Acciones Inmediatas

### Para Tu Próxima Estimación

```
1. □ Imprimir esta cheat sheet
2. □ Usar checklist de estimación
3. □ Aplicar fórmula rápida
4. □ Expresar como rango, no número exacto
5. □ Documentar estimación en la tarea
6. □ Trackear tiempo real después
7. □ Revisar precisión en retrospectiva
```

### Para Mejorar en 30 Días

```
Semana 1: Estimar + Medir
  - Estima todas tus tareas
  - Trackea tiempo real vs estimado
  - Calcula error

Semana 2: Analizar Patrones
  - ¿Qué subestimo siempre?
  - ¿Qué sobreestimo siempre?
  - Ajusta factores

Semana 3: Aplicar Aprendizajes
  - Usa factores ajustados
  - Mejora descomposición
  - Agrega más buffer donde fallas

Semana 4: Validar Mejora
  - Compara precisión vs semana 1
  - Objetivo: Reducir error 20%
  - Iterar proceso
```

---

**Última actualización:** Diciembre 2025
**Versión:** 1.0
**Para manual completo:** Ver [MANUAL_ESTIMACION_SOFTWARE.md](MANUAL_ESTIMACION_SOFTWARE.md)
