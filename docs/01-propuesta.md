# Propuesta — ElectroFit

## Concepto de Alcance del Proyecto

El alcance de ElectroFit comprende el conjunto de funcionalidades y entregables que se desarrollarán para digitalizar la gestión de turnos, pacientes y cobros de un centro de kinesiología, cubriendo tanto los requerimientos funcionales (qué hace el sistema) como los no funcionales (cómo lo hace).

Respondiendo a las tres preguntas base que plantea la gestión del alcance:

1. **¿Qué problema se busca resolver?** La gestión manual/desordenada de turnos, cobros y admisión de pacientes en un centro de kinesiología, sin control riguroso de saldos ni filtro de admisión médica.
2. **¿Qué funcionalidades tendrá el sistema?** Agenda con reglas configurables, ficha médica dinámica, control de cobros, reportes y notificaciones.
3. **¿Qué entregables se esperan al finalizar?** Aplicación web funcional (backend + frontend desplegados), documentación técnica, y el repositorio con el código fuente versionado.

## ¿Qué problema se busca resolver?

Un centro de kinesiología es, en esencia, un negocio de servicios de salud con una particularidad: cada turno involucra al mismo tiempo una cuestión **operativa** (¿hay lugar en la agenda?), una cuestión **clínica** (¿esta persona puede acceder al tratamiento sin evaluación previa?) y una cuestión **contable** (¿está pagado, señado o pendiente?). Hoy, en el centro que tomamos como caso real, estas tres dimensiones se gestionan de forma manual o con herramientas que no fueron pensadas para resolver las tres juntas — planillas, WhatsApp, o un sistema desarrollado con IA que presenta fallas conocidas. Esto deriva en problemas concretos y recurrentes:

- **Descontrol de la agenda:** sin reglas automáticas de antelación mínima, un paciente puede reservar (o cancelar) a último momento, dejando un espacio muerto que ya no se puede reasignar. Sin reglas de frecuencia, un mismo paciente puede acaparar múltiples turnos en poco tiempo, perjudicando a otros pacientes.
- **Falta de trazabilidad contable:** no existe un registro sistemático de quién pagó, quién señó y quién quedó con saldo pendiente. Esto obliga al administrador a llevar el control "de memoria" o en planillas paralelas, con el consiguiente riesgo de errores, pérdida de ingresos no cobrados, y falta de visibilidad financiera del negocio.
- **Ausencia de un filtro de admisión clínica:** al tratarse de un centro de salud, no cualquier persona puede iniciar un tratamiento sin antes completar una ficha médica básica (antecedentes, lesiones previas, contraindicaciones). Hoy ese paso, si se hace, depende de que el profesional se acuerde de pedirlo manualmente en el momento del turno — no está sistematizado.
- **Falta de consentimiento formal registrado:** en un entorno de salud, contar con un consentimiento informado firmado (y poder demostrar cuál versión del mismo aceptó cada paciente) es una práctica esperable, y hoy no existe un mecanismo que lo garantice.
- **Fricción para el cliente:** al mismo tiempo, el sistema no puede resolver estos controles a costa de hacerle la vida difícil al paciente. La mayoría de los clientes nuevos llegan buscando el centro en Google Maps y esperan poder reservar en el momento, sin fricciones como crear una cuenta — un problema de UX que cualquier solución debe resolver junto con los problemas operativos.

En síntesis: el problema no es únicamente "no hay una agenda digital", sino que **no existe un sistema que integre control de agenda, filtro clínico y control contable en un mismo flujo**, adaptado a las particularidades de un centro de salud.

## Por qué ElectroFit

ElectroFit no busca ser un calendario más ni una alternativa genérica a Google Calendar o a gestionar turnos por redes sociales — busca ser la herramienta que integra las tres dimensiones del problema (agenda, admisión clínica y cobros) bajo reglas que el propio centro puede configurar sin depender de un desarrollador:

- **Resuelve el descontrol de agenda** exigiendo antelación mínima configurable y limitando la frecuencia de reservas por paciente (vía DNI), evitando tanto las reservas de último momento como el acaparamiento de turnos.
- **Resuelve la falta de trazabilidad contable** centralizando el registro de señas, pagos y saldos por paciente, dándole al administrador visibilidad real del estado financiero del centro en cualquier momento.
- **Resuelve la ausencia de filtro clínico** incorporando una ficha médica configurable como paso obligatorio antes de la primera reserva, y permitiendo que el profesional la mantenga actualizada.
- **Resuelve la falta de consentimiento formal** mediante un sistema de consentimientos versionados, donde cada firma queda asociada a la versión exacta del texto que el paciente aceptó — algo que ni una planilla ni un WhatsApp pueden garantizar.
- **Resuelve la fricción del cliente nuevo** permitiendo reservar sin necesidad de cuenta, identificándose solo con DNI, y evitando pedirle de nuevo la ficha médica si ya es un paciente conocido.

> **Por qué lo pensamos así:** partimos de un caso real —el centro de un compañero del equipo, que además ya cuenta con un sistema desarrollado con IA pero con fallas conocidas— en lugar de partir de una necesidad hipotética. Esto nos permite justificar cada funcionalidad con un problema concreto y verificable, no con una suposición genérica de "todo centro de salud necesita un sistema de turnos". Frente a un jurado o tutor, esta base empírica es lo que le da peso real a la propuesta: no estamos resolviendo un problema inventado, sino uno que ya existe y que incluso ya se intentó resolver una vez sin éxito completo.

## Roles y Acceso

- **Administrador:** cuenta con login. Acceso total — agenda completa, ventas/reservas, clientes, reportes, configuración del sistema.
- **Profesional:** cuenta con login, rol de "admin limitado" — ve y gestiona únicamente su propia agenda (turnos asignados, bloqueo de horarios de descanso). Puede ver y editar la ficha médica de sus pacientes. No tiene acceso a la parte contable, resúmenes de venta, ni caja.
- **Cliente/Paciente:** sin cuenta. Se identifica por DNI al reservar. Recibe un link con token único (vía email) para consultar o cancelar su turno dentro del plazo permitido. Cualquier reagendamiento a otro horario pasa por el admin.

> **Por qué lo pensamos así:** descartamos que el cliente necesite cuenta propia porque en la práctica llega desde Google Maps buscando reservar rápido — pedirle que se registre agrega fricción innecesaria (el patrón "guest checkout" es el mismo que usan sistemas como Doctoralia o Booksy). En cambio, el link con token cumple la misma función de "autenticación" sin la complejidad de un sistema de login para el cliente. El profesional se modela como un admin con permisos recortados en vez de un rol totalmente distinto, porque comparte la mayoría de las acciones (ver agenda, atender pacientes) y solo difiere en el alcance de lo que puede ver/tocar.

## Flujo de Negocio — Administrador

1. Login → Dashboard
2. Ve agenda del día/semana en calendario
3. Puede: bloquear horario (descanso), agendar turno manual, revisar detalle de un turno (datos del cliente, servicio, estado de pago)
4. Entra a un turno → marca seña/pago recibido → actualiza estado de cuenta del cliente
5. Consulta pestaña Clientes → revisa ficha médica, historial, estado activo/inactivo
6. Consulta pestaña Reportes → ve métricas del mes
7. Configura reglas (antelación, frecuencia, campos de ficha médica) desde Administración

> **Por qué lo pensamos así:** el flujo sigue el orden natural del día a día de un admin: primero mira la agenda (lo urgente/inmediato), después gestiona pagos y clientes (lo operativo), y por último reportes y configuración (lo estratégico, que no se hace todos los días). Este orden también ayuda a priorizar qué pantallas hay que optimizar primero en el desarrollo.

## Flujo del Cliente Final

**Camino A — Cliente nuevo o recurrente:**

1. Ve el local en Maps → entra al link de reserva
2. Landing pública: info del local, actividades, ubicación
3. Elige "Reservar turno"
4. Elige servicio (precio y duración visibles)
5. Ve disponibilidad (según reglas configuradas por el admin)
6. Ingresa DNI → sistema chequea si ya existe como paciente:
   - Nuevo: completa datos personales + ficha médica + consentimiento
   - Conocido, sin renovación pendiente: solo confirma datos de contacto
   - Conocido, con renovación marcada por el admin: vuelve a completar ficha/consentimiento
7. Indica/confirma seña (según config del admin)
8. Confirma turno → recibe email con detalle + link/token

**Camino B — Cliente con turno ya reservado:**

1. Abre el link con token recibido por mail
2. Ve detalle del turno
3. Cancela (si está en plazo) o pide reagenda → deriva a contacto con el local

> **Por qué lo pensamos así:** el chequeo por DNI evita pedirle al mismo paciente que complete la ficha médica cada vez que reserva — solo se le vuelve a pedir si el admin marcó explícitamente que necesita renovación (por ejemplo, una vez al año). Esto reduce fricción para el cliente recurrente sin perder el control médico que necesita el centro.
