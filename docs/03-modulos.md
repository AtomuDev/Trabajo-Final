# Módulos, Navegación y Stack — ElectroFit

## Módulos y Funcionalidades Principales

### 1. Módulo de Agenda/Turnos
- Calendario visual (día/semana/mes) con turnos, disponibilidad y bloqueos
- Reserva de turno público (sin cuenta) desde landing o vía link de Google Maps
- Reserva manual por admin/profesional
- Reglas configurables: antelación mínima, sobreagendamiento sí/no, frecuencia máxima por DNI
- Cancelación/reagenda vía token del cliente

### 2. Módulo de Servicios
- ABM de tipos de servicio (nombre, duración, precio, descripción)
- Asociación de servicio a turno

### 3. Módulo de Clientes/Pacientes
- Ficha con datos personales + DNI
- Ficha médica configurable (el admin define qué campos pedir, el profesional puede editarla)
- Consentimientos (versionados, configurables por admin)
- Historial de turnos y estado (activo/inactivo)

### 4. Módulo de Cobros/Cuentas
- Registro de seña y pago
- Estado de cuenta por cliente (saldado / pendiente)
- Vinculado a cada turno/servicio
- Acceso exclusivo del rol Administrador

### 5. Módulo de Reportes
- Estadísticas de ventas, turnos por mes, ocupación, etc.

### 6. Módulo de Administración/Configuración
- Personalización de la landing pública (info del local, actividades, ubicación)
- Configuración de reglas de agenda (antelación, frecuencia, etc.)
- Configuración de campos de ficha médica y consentimientos

### 7. Módulo de Autenticación
- Login con rol (admin / profesional)

### 8. Módulo de Notificaciones
- MVP: Email automático (vía SendGrid o similar) en: confirmación de turno (con link+token), recordatorio previo, aviso de cancelación/reagenda, aviso de saldo pendiente
- Roadmap/v2: Integración WhatsApp Business API para los mismos eventos

> **Por qué lo pensamos así:** cada módulo mapea a una necesidad puntual que surgió del relevamiento con el centro de referencia (agenda desordenada → módulo agenda con reglas; falta de control de pagos → módulo cobros; admisión sin filtro → ficha médica). Notificaciones queda como módulo aparte porque involucra un servicio externo (SendGrid) distinto al resto del sistema. WhatsApp se deja para una v2 porque requiere aprobación de Meta o un servicio pago tipo Twilio — el email cumple la misma función a costo cero y sin fricción de aprobación, algo razonable para el alcance de un TFI.

## Mapa de Navegación / Pantallas

**Público (sin login):**
- `/` → Landing (info del local, actividades, ubicación)
- `/reservar` → Wizard de reserva (servicio → disponibilidad → DNI → datos/ficha → seña → confirmación)
- `/turno/:token` → Detalle del turno (cancelar / info)

**Admin/Profesional (con login):**
- `/login`
- `/dashboard` → resumen del día
- `/agenda` → calendario (admin: todos los profesionales / profesional: solo el suyo)
- `/ventas` → listado de reservas + configuración de reglas *(solo admin)*
- `/clientes` → listado + ficha de cada paciente *(admin: lectura/escritura — profesional: lectura y escritura solo en ficha médica)*
- `/reportes` → métricas *(solo admin)*
- `/administracion` → config landing, ficha médica, consentimientos *(solo admin)*

> **Por qué lo pensamos así:** separamos las rutas públicas de las privadas porque tienen audiencias y necesidades de seguridad distintas — las públicas no requieren autenticación y deben ser livianas para no perder al cliente que llega desde Maps; las privadas requieren rol validado en cada request. Este mapeo sirve directo como base para las rutas de React Router en el frontend.

## Stack Tecnológico

| Capa | Tecnología | Justificación |
|---|---|---|
| Backend | Spring Boot (Java) | Framework robusto, tipado fuerte, ecosistema maduro para reglas de negocio complejas (validaciones, roles) |
| Base de datos | PostgreSQL | Relacional, soporta JSONB (clave para ficha médica con campos dinámicos), robusto para datos sensibles |
| ORM | Spring Data JPA / Hibernate | Estándar de facto con Spring Boot |
| Frontend | React + TypeScript | SPA con estado complejo (wizard, calendario), tipado seguro |
| Autenticación | Spring Security + JWT | Roles (admin/profesional), stateless, apto para SPA |
| Notificaciones | Email vía SendGrid (o similar SMTP) | MVP simple y gratuito/económico |
| Documentación API | Swagger / OpenAPI | Estándar ya usado en trabajos anteriores |
| Control de versiones | Git + GitHub | Trabajo en equipo, historial de cambios |

> **Por qué lo pensamos así:** Spring Boot + PostgreSQL es el stack que el equipo ya venía trabajando, con experiencia previa comprobada. React + TypeScript se justifica porque el sistema tiene bastante interactividad real (calendario, wizard de reserva multi-paso, formularios dinámicos de ficha médica) — justo donde una SPA rinde mejor que un enfoque server-rendered. Al ser el trabajo final para recibirse, también tiene sentido mostrar dominio de un stack moderno y desacoplado en vez de achicar el alcance.
