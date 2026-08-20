# ElectroFit

Sistema de gestión de turnos, pacientes y cobros para centros de kinesiología.

Trabajo Final Integrador — Tecnicatura Universitaria en Programación (UTN), a distancia.

**Repositorio:** https://github.com/AtomuDev/Trabajo-Final

---

## 📋 1. Problema y Justificación

### ¿Qué problema se busca resolver?

Un centro de kinesiología es, en esencia, un negocio de servicios de salud con una particularidad: cada turno involucra al mismo tiempo una cuestión **operativa** (¿hay lugar en la agenda?), una cuestión **clínica** (¿esta persona puede acceder al tratamiento sin evaluación previa?) y una cuestión **contable** (¿está pagado, señado o pendiente?). Hoy, en el centro que tomamos como caso real, estas tres dimensiones se gestionan de forma manual o con herramientas que no fueron pensadas para resolver las tres juntas — planillas, WhatsApp, o un sistema desarrollado con IA que presenta fallas conocidas. Esto deriva en problemas concretos y recurrentes:

- **Descontrol de la agenda:** sin reglas automáticas de antelación mínima, un paciente puede reservar (o cancelar) a último momento, dejando un espacio muerto que ya no se puede reasignar. Sin reglas de frecuencia, un mismo paciente puede acaparar múltiples turnos en poco tiempo, perjudicando a otros pacientes.
- **Falta de trazabilidad contable:** no existe un registro sistemático de quién pagó, quién señó y quién quedó con saldo pendiente. Esto obliga al administrador a llevar el control "de memoria" o en planillas paralelas, con el consiguiente riesgo de errores, pérdida de ingresos no cobrados, y falta de visibilidad financiera del negocio.
- **Ausencia de un filtro de admisión clínica:** al tratarse de un centro de salud, no cualquier persona puede iniciar un tratamiento sin antes completar una ficha médica básica (antecedentes, lesiones previas, contraindicaciones). Hoy ese paso, si se hace, depende de que el profesional se acuerde de pedirlo manualmente en el momento del turno — no está sistematizado.
- **Falta de consentimiento formal registrado:** en un entorno de salud, contar con un consentimiento informado firmado (y poder demostrar cuál versión del mismo aceptó cada paciente) es una práctica esperable, y hoy no existe un mecanismo que lo garantice.
- **Fricción para el cliente:** al mismo tiempo, el sistema no puede resolver estos controles a costa de hacerle la vida difícil al paciente. La mayoría de los clientes nuevos llegan buscando el centro en Google Maps y esperan poder reservar en el momento, sin fricciones como crear una cuenta — un problema de UX que cualquier solución debe resolver junto con los problemas operativos.

En síntesis: el problema no es únicamente "no hay una agenda digital", sino que **no existe un sistema que integre control de agenda, filtro clínico y control contable en un mismo flujo**, adaptado a las particularidades de un centro de salud.

### Por qué ElectroFit

ElectroFit no busca ser un calendario más ni una alternativa genérica a Google Calendar o a gestionar turnos por redes sociales — busca ser la herramienta que integra las tres dimensiones del problema (agenda, admisión clínica y cobros) bajo reglas que el propio centro puede configurar sin depender de un desarrollador:

- **Resuelve el descontrol de agenda** exigiendo antelación mínima configurable y limitando la frecuencia de reservas por paciente (vía DNI), evitando tanto las reservas de último momento como el acaparamiento de turnos.
- **Resuelve la falta de trazabilidad contable** centralizando el registro de señas, pagos y saldos por paciente, dándole al administrador visibilidad real del estado financiero del centro en cualquier momento.
- **Resuelve la ausencia de filtro clínico** incorporando una ficha médica configurable como paso obligatorio antes de la primera reserva, y permitiendo que el profesional la mantenga actualizada.
- **Resuelve la falta de consentimiento formal** mediante un sistema de consentimientos versionados, donde cada firma queda asociada a la versión exacta del texto que el paciente aceptó — algo que ni una planilla ni un WhatsApp pueden garantizar.
- **Resuelve la fricción del cliente nuevo** permitiendo reservar sin necesidad de cuenta, identificándose solo con DNI, y evitando pedirle de nuevo la ficha médica si ya es un paciente conocido.

> **Por qué lo pensamos así:** partimos de un caso real —el centro de un compañero del equipo, que además ya cuenta con un sistema desarrollado con IA pero con fallas conocidas— en lugar de partir de una necesidad hipotética. Esto nos permite justificar cada funcionalidad con un problema concreto y verificable, no con una suposición genérica de "todo centro de salud necesita un sistema de turnos". Frente a un jurado o tutor, esta base empírica es lo que le da peso real a la propuesta: no estamos resolviendo un problema inventado, sino uno que ya existe y que incluso ya se intentó resolver una vez sin éxito completo.

Ver detalle completo en [`docs/01-propuesta.md`](docs/01-propuesta.md).

---

## 👥 2. Roles y Acceso

| Rol | Acceso | Descripción |
|---|---|---|
| **Administrador** | Login con cuenta | Acceso total: agenda completa, ventas/reservas, clientes, reportes y configuración del sistema |
| **Profesional** | Login con cuenta | Acceso limitado a su propia agenda. Puede ver y editar la ficha médica de sus pacientes, pero no tiene acceso a la parte contable (cobros, pagos, saldos) |
| **Cliente / Paciente** | Sin cuenta | Se identifica por DNI al reservar. Gestiona su turno mediante un link con token único enviado por email |

---

## 🧩 3. Módulos principales

1. **Agenda / Turnos** — calendario visual, reserva pública y manual, reglas de antelación/frecuencia/sobreagendamiento, cancelación vía token
2. **Servicios** — ABM de tipos de servicio (nombre, duración, precio)
3. **Clientes / Pacientes** — datos personales, ficha médica configurable, consentimientos versionados, historial
4. **Cobros / Cuentas** — registro de seña y pago, estado de cuenta por cliente (exclusivo del Administrador)
5. **Reportes** — estadísticas de ventas, turnos y ocupación
6. **Administración / Configuración** — personalización de landing pública, reglas de agenda, campos de ficha médica y consentimientos
7. **Autenticación** — login con rol (admin / profesional)
8. **Notificaciones** — email automático (WhatsApp planificado como mejora futura)

Detalle completo en [`docs/03-modulos.md`](docs/03-modulos.md).

---

## ✅ 4. Requerimientos Funcionales (resumen)

- Reserva pública de turnos sin cuenta, identificando al paciente por DNI
- Reglas configurables de antelación mínima, frecuencia y sobreagendamiento
- Ficha médica dinámica configurable por el admin, editable por el profesional
- Consentimientos versionados con registro de qué versión firmó cada paciente
- Registro de señas/pagos y estado de cuenta por paciente, exclusivo del rol Administrador
- Reportes de ventas, turnos y ocupación
- Login con roles diferenciados (Administrador / Profesional)
- Notificaciones automáticas por email

## ⚙️ 5. Requerimientos No Funcionales (resumen)

- **Seguridad:** JWT, contraseñas hasheadas, control de acceso por rol a nivel de API
- **Usabilidad:** flujo de reserva optimizado para mobile (la mayoría de los clientes llegan desde Google Maps)
- **Disponibilidad:** accesible de forma continua durante el horario de atención
- **Escalabilidad del modelo de datos:** la ficha médica se puede ampliar sin tocar código ni base de datos
- **Trazabilidad:** respuestas médicas y firmas de consentimiento quedan registradas con fecha y usuario
- **Rendimiento:** consultas de disponibilidad con tiempos de respuesta aceptables
- **Mantenibilidad:** backend y frontend desacoplados, documentados con Swagger/OpenAPI

Detalle completo en [`docs/02-requerimientos.md`](docs/02-requerimientos.md).

---

## 📐 6. Alcance del Proyecto

### Inclusiones

- Aplicación web (backend API + frontend SPA)
- Módulo de autenticación con roles
- Agenda con reglas configurables
- Ficha médica dinámica y consentimientos versionados
- Cobros con registro manual de señas y pagos
- Reportes con estadísticas básicas
- Notificaciones automáticas por email
- Documentación técnica completa

### Exclusiones

- Aplicación móvil nativa (el sistema es web responsive)
- Integración con pasarela de pago real (Mercado Pago u otra) — el registro es manual en esta versión
- Integración con WhatsApp Business API — queda como mejora futura (roadmap v2)
- Soporte multi-tenant — arquitectura single-tenant, un único centro por instancia
- Mantenimiento evolutivo posterior a la entrega del TFI
- Integraciones con sistemas externos no especificados

### Límites del proyecto

- **Usuarios/roles:** solo Administrador, Profesional y Cliente sin cuenta. No se contempla un rol adicional (ej. recepcionista) en esta versión.
- **Arquitectura:** single-tenant — una instancia sirve a un único centro de salud.
- **Temporal:** desarrollo enmarcado en el cronograma académico, con entregas parciales el 30/08 y el 27/09.
- **Datos de prueba:** se validará con datos de prueba y, de ser posible, con el caso real del centro de referencia. No se garantiza migración de datos históricos del sistema actual (fuente no confiable por sus fallas conocidas).

---

## 🏗️ 7. Stack Tecnológico

| Capa | Tecnología | Justificación |
|---|---|---|
| Backend | Spring Boot (Java) | Framework robusto y tipado, ideal para reglas de negocio complejas |
| Base de datos | PostgreSQL | Relacional, con soporte JSONB para estructuras flexibles como la ficha médica |
| ORM | Spring Data JPA / Hibernate | Estándar de facto junto a Spring Boot |
| Frontend | React + TypeScript | SPA con estado complejo (wizard de reserva, calendario), tipado seguro |
| Autenticación | Spring Security + JWT | Manejo de roles, stateless, apto para SPA |
| Notificaciones | Email vía SendGrid (o SMTP similar) | Simple y de bajo costo para el alcance del MVP |
| Documentación de API | Swagger / OpenAPI | Estándar ya usado en trabajos previos del equipo |
| Control de versiones | Git + GitHub | Trabajo colaborativo en equipo |

---

## 🗄️ 8. Modelo de Datos

El esquema de base de datos (tablas, atributos, tipos y relaciones) se presentará en la **2.ª Entrega — Diseño y Módulos** (27/09), junto con el listado definitivo de módulos, ambos sujetos a aprobación previa del tutor.

---

## 📁 Estructura del repositorio

```
Trabajo-Final/
├── README.md              → este archivo
├── docs/                  → documentación de gestión y diseño del proyecto
│   ├── 01-propuesta.md
│   ├── 02-requerimientos.md
│   └── 03-modulos.md
│   (el modelo de datos y ERD se suman en la 2.ª Entrega)
├── backend/                → API REST en Spring Boot (Java)
└── frontend/                → SPA en React + TypeScript
```

## 🚧 Estado actual

Proyecto en etapa de **1.ª Entrega — Propuesta de Proyecto y Repositorio** (propuesta, alcance, stack y estructura del repositorio). El esquema de base de datos y el listado definitivo de módulos se presentarán en la 2.ª Entrega (27/09), sujetos a aprobación previa del tutor. La implementación de backend y frontend comienza una vez aprobada esa instancia.

## 👨‍💻 Equipo

Proyecto desarrollado por un equipo de 3 estudiantes de la Tecnicatura Universitaria en Programación (UTN) como Trabajo Final Integrador.

- Backend: *(completar)*
- Frontend: *(completar)*
- *(completar roles restantes)*
