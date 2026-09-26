# Arquitectura del Proyecto — ElectroFit

## Arquitectura elegida: Arquitectura en Capas (Layered Architecture)

El backend se organiza en capas horizontales con responsabilidades separadas, siguiendo el patrón estándar de Spring Boot:

Cliente (React SPA)
│
▼
┌─────────────────────┐
│ Controller │ → recibe requests HTTP, valida entrada, devuelve respuestas
├─────────────────────┤
│ Service │ → lógica de negocio (reglas de agenda, validación de ficha
│ │ médica, cálculo de estado de cuenta, etc.)
├─────────────────────┤
│ Repository │ → acceso a datos (Spring Data JPA), sin lógica de negocio
├─────────────────────┤
│ PostgreSQL │ → persistencia
└─────────────────────┘


### Por qué esta arquitectura y no otra

- **Frente a Microservicios:** el proyecto es single-tenant, de alcance acotado (un TFI con cronograma fijo) y lo desarrolla un equipo de 3 personas. Microservicios agregaría complejidad de infraestructura (orquestación, comunicación entre servicios, despliegue distribuido) que no se justifica para este tamaño de proyecto ni para el tiempo disponible.
- **Frente a Arquitectura Hexagonal / Clean Architecture:** son válidas para proyectos con reglas de negocio muy complejas y necesidad de intercambiar infraestructura (por ejemplo, cambiar de PostgreSQL a otro motor sin tocar el dominio). No es el caso de ElectroFit: el motor de base de datos y el framework ya están definidos y no van a cambiar durante el desarrollo del TFI, así que el costo extra de abstracción no se traduce en un beneficio real acá.
- **Por qué sí la arquitectura en capas:** es el patrón estándar y mejor documentado para Spring Boot, lo que reduce la curva de aprendizaje para el equipo. Separa claramente responsabilidades (un Controller nunca debería tener lógica de negocio, un Repository nunca debería tener validaciones), lo cual facilita testear cada capa por separado y mantener el código ordenado a medida que crecen los 8 módulos funcionales.

### Estructura de paquetes (backend)

backend/
├── build.gradle → dependencias y plugins
├── settings.gradle
├── gradlew / gradlew.bat
└── src/
├── main/
│ ├── java/com/electrofit/backend/
│ │ ├── config/ → configuración de Security, JWT, CORS, OpenAPI/Swagger
│ │ ├── security/ → JWT service, filtro de autenticación, user details
│ │ ├── controller/ → controladores REST, uno por módulo (AgendaController, PacienteController, etc.)
│ │ ├── service/ → lógica de negocio por módulo
│ │ ├── repository/ → interfaces Spring Data JPA, una por entidad
│ │ ├── model/ → las 12 entidades JPA del modelo de datos (Usuario, Paciente, Turno, etc.)
│ │ ├── dto/
│ │ │ ├── request/ → objetos de entrada (lo que el cliente envía)
│ │ │ └── response/ → objetos de salida (lo que el backend devuelve)
│ │ ├── enums/ → Rol, EstadoTurno, EstadoCobro, TipoDato, etc. (reflejan los VARCHAR con valores fijos del modelo de datos)
│ │ ├── exception/ → manejo global de excepciones (@ControllerAdvice)
│ │ └── BackendApplication.java
│ └── resources/
│ └── application.properties
└── test/java/com/electrofit/backend/


**Por qué separamos `config/` de `security/`:** `config/` agrupa configuración transversal de la aplicación (CORS, Swagger, beans generales), mientras que `security/` concentra específicamente la lógica de autenticación y autorización (el filtro JWT, la generación/validación de tokens, la carga de usuarios). Separarlos evita que un paquete termine mezclando dos responsabilidades distintas.

**Por qué `dto/request` y `dto/response` separados:** un mismo módulo suele necesitar una forma de entrada y una forma de salida distintas para la misma entidad (por ejemplo, `TurnoRequest` no incluye `id` ni `token_acceso`, que los genera el backend, mientras que `TurnoResponse` sí los incluye). Separar ambos evita ambigüedad sobre qué DTO usar en cada punto del flujo.

**Por qué `enums/` como paquete propio:** varios campos del modelo de datos son `VARCHAR` con un conjunto fijo de valores posibles (`usuario.rol`, `turno.estado`, `pago.tipo`, `pago.estado`, `ficha_medica_campo.tipo_dato`). Modelarlos como enums de Java en vez de strings sueltos evita errores de tipeo y centraliza los valores válidos en un solo lugar del código.

## Frontend: SPA desacoplada

El frontend es una Single Page Application en React + TypeScript, completamente desacoplada del backend y comunicándose solo vía API REST (JSON). Esto permite:
- Desarrollar y probar frontend y backend en paralelo entre los miembros del equipo.
- Que el mismo backend pueda eventualmente servir a otros clientes (una futura app móvil, por ejemplo) sin modificarse.

## Tecnologías definitivas y justificación

| Capa | Tecnología | Justificación |
|---|---|---|
| Backend | Spring Boot (Java 21) | Framework robusto y tipado, con soporte maduro para las reglas de negocio complejas del proyecto (validación de ficha médica, reglas de agenda configurables) |
| Build tool | Gradle (Groovy DSL) | Preferencia del equipo por sobre Maven; Groovy elegido por curva de aprendizaje más simple y mayor cantidad de documentación en español frente a Kotlin DSL |
| Base de datos | PostgreSQL | Relacional, con soporte JSONB nativo por si se necesita extender la ficha médica más allá del modelo EAV definido |
| ORM | Spring Data JPA / Hibernate | Estándar de facto junto a Spring Boot, reduce código repetitivo de acceso a datos |
| Autenticación | Spring Security + JWT | Stateless, apto para una SPA que no comparte sesión de servidor con el backend |
| Frontend | React + TypeScript | Tipado seguro para un estado de UI complejo (wizard de reserva, calendario con validaciones de reglas de agenda) |
| Documentación de API | Swagger / OpenAPI (springdoc) | Documentación autogenerada y siempre sincronizada con el código, estándar ya usado por el equipo en trabajos previos |
| Notificaciones | Spring Boot Starter Mail (SMTP) | Simple de configurar y de bajo costo para el alcance del MVP, sin depender de un proveedor externo de pago |
| Control de versiones | Git + GitHub | Trabajo colaborativo en equipo, historial de cambios y revisión de código vía Pull Requests |

## Comunicación Frontend-Backend

- Protocolo: HTTP/REST, formato JSON.
- Autenticación: JWT enviado en el header `Authorization: Bearer <token>` en cada request autenticado.
- CORS configurado en el backend para aceptar únicamente el origen del frontend desplegado.