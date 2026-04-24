# Backend Java - Microservicio de Autenticación y Persistencia

## Tecnologías
- Java 17
- Spring Boot 3.2.2
- Spring Security + JWT
- Spring Data JPA
- H2 Database (en desarrollo) / PostgreSQL (producción)
- Maven

## Estructura de paquetes
```
backend-java/src/main/java/com/auditoria/
├── AuditoriaApplication.java # Clase principal
├── config/
│   ├── SecurityConfig.java # Configuración de seguridad y filtros
│   ├── JwtUtil.java # Generación y validación de tokens JWT
│   └── JwtRequestFilter.java # Filtro para interceptar requests y validar JWT
├── controller/
│   └── AuthController.java # Endpoints /register y /login
├── dto/
│   ├── LoginRequest.java
│   ├── RegisterRequest.java
│   └── AuthResponse.java
├── entity/
│   └── User.java # Entidad JPA para usuarios
├── repository/
│   └── UserRepository.java # Interfaz JPA
└── service/
    └── UserService.java # Lógica de negocio (registro, autenticación, carga de usuario)
```

## Endpoints implementados

| Método | Endpoint              | Descripción                          | Autenticación |
|--------|----------------------|--------------------------------------|--------------|
| POST   | `/api/auth/register` | Registra un nuevo usuario            | No requiere  |
| POST   | `/api/auth/login`    | Autentica y devuelve un token JWT    | No requiere  |
| (Próx.) POST | `/api/audit`  | Envía código al microservicio Python | Requiere JWT |

## Base de datos

- **Desarrollo:** H2 (en memoria) – no requiere instalación externa.
- **Producción:** PostgreSQL (puerto 5432, BD `auditoria_db`, usuario `postgres`, password `secret`).

### Configuración en `application.properties` (H2 actual)

```properties
server.port=8080
spring.datasource.url=jdbc:h2:mem:auditoria_db;DB_CLOSE_DELAY=-1
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.jpa.hibernate.ddl-auto=update
spring.h2.console.enabled=true
jwt.secret=miClaveSecretaSuperSeguraParaJWT2026!QueDebeSerLarga
jwt.expiration=86400000
```

## Seguridad

- Las contraseñas se encriptan con `BCryptPasswordEncoder`.
- Los tokens JWT tienen una validez de 24 horas.
- Las rutas protegidas requieren header:

```
Authorization: Bearer <token>
```

## Cómo levantar el servicio

### Requisitos previos
- JDK 17
- Maven (o usar el wrapper `mvnw`)

### Comandos

```bash
cd backend-java
mvn clean compile
mvn spring-boot:run
```

El servidor corre en:  
http://localhost:8080

## Pruebas realizadas (con curl)

```bash
# Registrar usuario
curl -X POST http://localhost:8080/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"username":"maty","email":"maty@test.com","password":"1234"}'

# Login
curl -X POST http://localhost:8080/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username":"maty","password":"1234"}'

# Registrar duplicado (debe fallar)
curl -X POST http://localhost:8080/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"username":"maty","email":"maty@test.com","password":"1234"}'

# Login con contraseña incorrecta (401)
curl -X POST http://localhost:8080/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username":"maty","password":"wrong"}'
```

## Próximos pasos

- Endpoint `/api/audit` para comunicarse con el microservicio Python.
- Conexión real a PostgreSQL (cuando se defina el entorno Docker).
- Persistencia del historial de auditorías.
