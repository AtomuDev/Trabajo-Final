# Módulos Funcionales — ElectroFit

Los módulos están enumerados en el orden en que conviene construirlos: cada uno depende de que el anterior ya esté funcionando. Esto no es necesariamente el orden en que se le presenta al usuario final, sino el orden de desarrollo.

## Resumen

| # | Módulo | Prioridad | Depende de |
|---|---|---|---|
| 1 | Autenticación | Alta | — |
| 2 | Servicios | Alta | Autenticación |
| 3 | Administración / Configuración | Alta | Autenticación |
| 4 | Clientes / Pacientes | Alta | Administración / Configuración |
| 5 | Agenda / Turnos | Alta | Servicios, Administración/Configuración, Clientes/Pacientes |
| 6 | Cobros / Cuentas | Media | Agenda / Turnos |
| 7 | Notificaciones | Baja | Agenda / Turnos |
| 8 | Reportes | Baja | Agenda / Turnos, Cobros / Cuentas |

---

## 1. Autenticación
**Descripción funcional:** login con email/password para Admin y Profesional (JWT), middleware de autorización por rol en cada endpoint. No hay registro público — los usuarios los crea el propio Admin desde su panel.

**Prioridad:** Alta — bloqueante, todo el resto del sistema depende de saber quién opera y con qué rol.

**Depende de:** —

**Roles que lo usan:** Administrador, Profesional

---

## 2. Servicios
**Descripción funcional:** ABM de tipos de servicio (nombre, duración en minutos, precio, activo/inactivo). Es la entidad base que consume Agenda/Turnos y Administración/Configuración (reglas de agenda por servicio).

**Prioridad:** Alta — sin servicios definidos no se puede crear ningún turno.

**Depende de:** Autenticación (solo Admin accede)

**Roles que lo usan:** Administrador

---

## 3. Administración / Configuración
**Descripción funcional:** ABM de campos de ficha médica dinámica (con sus opciones si son de tipo Opción Múltiple), ABM de consentimientos (el texto legal, versionado automático al editar), ABM de reglas de agenda (antelación mínima, frecuencia por DNI, sobreagendamiento — global o por servicio), y disponibilidad horaria configurable por profesional.

**Prioridad:** Alta — define las reglas que todos los módulos siguientes van a consumir; sin esto, Pacientes y Agenda no tienen qué mostrar ni qué validar.

**Depende de:** Autenticación

**Roles que lo usan:** Administrador

---

## 4. Clientes / Pacientes
**Descripción funcional:** alta de paciente por DNI (sin necesidad de cuenta), completar la ficha médica dinámica (renderizada según los campos definidos en Administración), firma del consentimiento vigente (guarda la versión firmada), vista de historial del paciente, y marcado de "requiere renovación de ficha" por parte de Admin/Profesional.

**Prioridad:** Alta — la Agenda necesita validar que el paciente tenga ficha médica y consentimiento al día antes de reservar.

**Depende de:** Administración / Configuración (usa los campos y el consentimiento ya definidos)

**Roles que lo usan:** Administrador, Profesional, Cliente/Paciente

---

## 5. Agenda / Turnos
**Descripción funcional:** calendario visual (vista completa para Admin, vista acotada a lo propio para Profesional), reserva pública sin login (identificación por DNI, validación de ficha médica/consentimiento pendiente, aplicación de reglas de antelación/frecuencia según disponibilidad del profesional), reserva manual por Admin/Profesional, cancelación y reagenda vía link con token único, y cambios de estado del turno (Pendiente → Confirmado → Completado / Cancelado).

**Prioridad:** Alta — es el módulo core del sistema, el que resuelve el problema principal planteado en la propuesta.

**Depende de:** Servicios, Administración/Configuración, Clientes/Pacientes

**Roles que lo usan:** Administrador, Profesional, Cliente/Paciente

---

## 6. Cobros / Cuentas
**Descripción funcional:** registro de seña o pago completo asociado a un turno (exclusivo Admin), estado de cuenta por paciente (precio del servicio vs. pagos confirmados), historial de pagos por paciente.

**Prioridad:** Media — importante para el alcance del proyecto, pero el flujo de reserva y agenda puede probarse de punta a punta sin este módulo activo.

**Depende de:** Agenda / Turnos (un pago siempre está atado a un turno ya creado)

**Roles que lo usan:** Administrador

---

## 7. Notificaciones
**Descripción funcional:** email automático al confirmar una reserva (con el link/token de gestión), email de recordatorio antes del turno, y email al cancelar o reagendar.

**Prioridad:** Baja — mejora la experiencia y reduce ausentismo, pero el sistema funciona operativamente sin notificaciones automáticas (se podría avisar manualmente en una primera versión).

**Depende de:** Agenda / Turnos (necesita eventos reales sobre los que enganchar el envío)

**Roles que lo usan:** Cliente/Paciente (receptor), Administrador (configuración de plantillas si aplica)

---

## 8. Reportes
**Descripción funcional:** estadísticas de turnos por período y estado, ocupación por profesional/servicio, ventas totales y señas pendientes de cobro.

**Prioridad:** Baja — es el módulo más postergable si el tiempo de desarrollo se acorta, ya que necesita que Agenda y Cobros ya tengan datos reales para tener sentido.

**Depende de:** Agenda / Turnos, Cobros / Cuentas

**Roles que lo usan:** Administrador