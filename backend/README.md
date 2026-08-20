# ElectroFit — Backend

API REST desarrollada en **Spring Boot (Java)** con **PostgreSQL** y **Spring Data JPA**.

## Estado

Aún no iniciado. La implementación comienza tras la validación del modelo de base de datos y los módulos en la 2.ª Entrega (27/09).

## Estructura prevista

```
backend/
├── src/main/java/com/electrofit/
│   ├── config/          → configuración de Spring Security, JWT, CORS
│   ├── controller/       → controladores REST por módulo
│   ├── service/          → lógica de negocio
│   ├── repository/       → interfaces Spring Data JPA
│   ├── model/ (entity/)  → entidades JPA (usuario, paciente, turno, etc.)
│   ├── dto/              → DTOs de entrada/salida
│   └── exception/        → manejo global de excepciones
├── src/main/resources/
│   └── application.properties
├── src/test/java/         → tests
└── pom.xml
```

## Cómo correrlo (a completar cuando el proyecto esté inicializado)

```bash
./mvnw spring-boot:run
```

Documentación de la API vía Swagger/OpenAPI, disponible en `/swagger-ui.html` una vez levantado el servidor.

Ver el modelo de datos completo en [`../docs/04-modelo-datos.md`](../docs/04-modelo-datos.md).
