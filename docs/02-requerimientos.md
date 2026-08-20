# Requerimientos — ElectroFit

## Requerimientos Funcionales

Lo que el sistema **hace**, organizado por módulo:

### Agenda / Turnos
- Permitir reservar turnos públicamente sin necesidad de cuenta (identificación por DNI)
- Mostrar disponibilidad respetando reglas de antelación mínima configuradas por el admin
- Permitir al admin/profesional bloquear horarios y agendar turnos manualmente
- Permitir cancelar/reagendar un turno mediante link con token único enviado por email
- Restringir la frecuencia de reservas por paciente (vía DNI) según configuración del admin

### Servicios
- Permitir al admin dar de alta, editar y desactivar tipos de servicio (nombre, duración, precio)

### Pacientes
- Registrar datos personales y ficha médica del paciente al momento de la reserva
- Permitir que el admin configure dinámicamente los campos de la ficha médica
- Permitir que el profesional edite la ficha médica de sus pacientes
- Registrar la aceptación de consentimientos, versionados
- Evitar solicitar nuevamente la ficha médica a un paciente ya conocido, salvo que el admin marque renovación

### Cobros
- Registrar señas y pagos asociados a cada turno
- Mostrar el estado de cuenta (saldado/pendiente) de cada paciente
- Restringir el acceso a este módulo únicamente al rol Administrador

### Reportes
- Generar estadísticas de ventas, turnos y ocupación mensual

### Administración
- Permitir personalizar la landing pública (información del local, ubicación, actividades)
- Permitir configurar las reglas de agenda (antelación, frecuencia, sobreagendamiento)

### Autenticación
- Permitir el login diferenciado por rol (Administrador / Profesional)
- Restringir el acceso a cada módulo según el rol autenticado

### Notificaciones
- Enviar automáticamente un email de confirmación, recordatorio, cancelación/reagenda y aviso de saldo pendiente

## Requerimientos No Funcionales

Lo que define **cómo** el sistema cumple esas funciones:

- **Seguridad:** autenticación mediante JWT, contraseñas almacenadas con hash, control de acceso por rol a nivel de API (ej. el profesional no puede acceder a los endpoints de cobros aunque conozca la URL)
- **Usabilidad:** el flujo de reserva pública debe poder completarse sin fricción desde un dispositivo móvil, dado que la mayoría de los clientes llegan desde Google Maps
- **Disponibilidad:** el sistema debe estar accesible de forma continua durante el horario de atención del centro
- **Escalabilidad del modelo de datos:** la ficha médica debe poder ampliarse (nuevos campos) sin requerir cambios en la base de datos ni en el código
- **Trazabilidad:** las respuestas de ficha médica y las firmas de consentimiento deben quedar registradas con fecha y, en el caso de la ficha médica, con el usuario que las modificó
- **Rendimiento:** las consultas de disponibilidad de agenda deben responder en tiempos aceptables para no generar demoras perceptibles en el flujo de reserva
- **Mantenibilidad:** backend y frontend desacoplados (API REST + SPA), documentados con Swagger/OpenAPI, para facilitar su mantenimiento posterior

## Alcance y Límites del Proyecto

### Inclusiones (qué SÍ forma parte del proyecto)

- Desarrollo de una aplicación web (backend API + frontend SPA)
- Módulo de autenticación con roles (Administrador / Profesional)
- Módulo de agenda con reglas configurables de antelación, frecuencia y sobreagendamiento
- Módulo de ficha médica dinámica y consentimientos versionados
- Módulo de cobros con registro manual de señas y pagos
- Módulo de reportes con estadísticas básicas
- Notificaciones automáticas por email
- Documentación técnica (propuesta, modelo de base de datos, diagramas)

### Exclusiones (qué NO forma parte del proyecto)

- Desarrollo de aplicación móvil nativa (el sistema es web, responsive)
- Integración con pasarela de pago real (Mercado Pago u otra) — el registro de pagos es manual en esta versión
- Integración con WhatsApp Business API — queda como mejora futura (roadmap v2)
- Soporte multi-tenant (el sistema está pensado para un único centro, arquitectura single-tenant)
- Mantenimiento evolutivo posterior a la entrega del TFI
- Integraciones con sistemas externos no especificados (ej. sistemas contables de terceros)

### Límites del proyecto

- **Límite de usuarios/roles:** el sistema contempla únicamente los roles Administrador, Profesional y Cliente (sin cuenta). No se contempla un rol adicional (ej. recepcionista) en esta primera versión.
- **Límite de arquitectura:** al ser single-tenant, el sistema se limita a dar servicio a un único centro de salud por instancia desplegada; no está diseñado para escalar a múltiples clientes/centros sobre la misma base de datos.
- **Límite temporal:** el desarrollo se enmarca en el cronograma académico del TFI, con entregas parciales el 30/08 (propuesta y repositorio) y el 27/09 (diseño y módulos), lo cual condiciona la profundidad de las funcionalidades a lo que sea viable implementar en ese plazo.
- **Límite de datos de prueba:** el sistema se validará con datos de prueba y, de ser posible, con el caso real del centro de kinesiología de referencia, pero no se garantiza una migración de datos históricos reales del sistema actual del centro (dado que ese sistema tiene fallas conocidas y no se define como fuente confiable).
