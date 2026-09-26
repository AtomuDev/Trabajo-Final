# Modelo de Datos — ElectroFit

## 1. usuario
| Campo | Tipo | Notas |
|---|---|---|
| id | BIGSERIAL PK | |
| nombre | VARCHAR(100) | |
| apellido | VARCHAR(100) | |
| email | VARCHAR(150) UNIQUE | login |
| password_hash | VARCHAR(255) | |
| rol | VARCHAR(20) | 'ADMIN' / 'PROFESIONAL' |
| activo | BOOLEAN | |
| creado_en | TIMESTAMP | |

## 2. paciente
| Campo | Tipo | Notas |
|---|---|---|
| id | BIGSERIAL PK | |
| dni | VARCHAR(20) UNIQUE | identificador clave |
| nombre | VARCHAR(100) | |
| apellido | VARCHAR(100) | |
| email | VARCHAR(150) | |
| telefono | VARCHAR(30) | |
| activo | BOOLEAN | |
| requiere_renovacion_ficha | BOOLEAN | marcado manual por el admin |
| creado_en | TIMESTAMP | |

## 3. servicio
| Campo | Tipo | Notas |
|---|---|---|
| id | BIGSERIAL PK | |
| nombre | VARCHAR(100) | ej: "Sesión individual" |
| descripcion | TEXT | |
| duracion_minutos | INT | |
| precio | DECIMAL(10,2) | |
| activo | BOOLEAN | |

## 4. disponibilidad_profesional 🆕
| Campo | Tipo | Notas |
|---|---|---|
| id | BIGSERIAL PK | |
| profesional_id | BIGINT FK → usuario | |
| dia_semana | INT | 0=Domingo ... 6=Sábado |
| hora_inicio | TIME | |
| hora_fin | TIME | |
| activo | BOOLEAN | |

> Configurable por profesional desde el panel admin.

## 5. turno
| Campo | Tipo | Notas |
|---|---|---|
| id | BIGSERIAL PK | |
| paciente_id | BIGINT FK → paciente | |
| profesional_id | BIGINT FK → usuario | |
| servicio_id | BIGINT FK → servicio | |
| fecha_hora | TIMESTAMP | |
| estado | VARCHAR(20) | 'PENDIENTE'/'CONFIRMADO'/'CANCELADO'/'COMPLETADO' |
| token_acceso | UUID UNIQUE | para el link del cliente |
| creado_en | TIMESTAMP | |

## 6. ficha_medica_campo
| Campo | Tipo | Notas |
|---|---|---|
| id | BIGSERIAL PK | |
| etiqueta | VARCHAR(150) | ej: "¿Tiene alguna lesión previa?" |
| tipo_dato | VARCHAR(20) | 'TEXTO'/'NUMERO'/'BOOLEANO'/'OPCION_MULTIPLE' |
| obligatorio | BOOLEAN | |
| orden | INT | orden de aparición en el form |
| activo | BOOLEAN | |

## 7. ficha_medica_opcion 🆕
| Campo | Tipo | Notas |
|---|---|---|
| id | BIGSERIAL PK | |
| campo_id | BIGINT FK → ficha_medica_campo | |
| valor | VARCHAR(150) | ej: "Sí" / "No" / "A veces" |
| orden | INT | orden de aparición |

## 8. ficha_medica_respuesta
| Campo | Tipo | Notas |
|---|---|---|
| id | BIGSERIAL PK | |
| paciente_id | BIGINT FK → paciente | |
| campo_id | BIGINT FK → ficha_medica_campo | |
| valor | TEXT | el backend la interpreta según tipo_dato |
| completado_en | TIMESTAMP | |
| modificado_por | BIGINT FK → usuario (nullable) | null si la completó el paciente directamente |
| modificado_en | TIMESTAMP | última fecha de edición |

> UNIQUE compuesto (paciente_id, campo_id).

## 9. consentimiento
| Campo | Tipo | Notas |
|---|---|---|
| id | BIGSERIAL PK | |
| titulo | VARCHAR(150) | |
| texto | TEXT | |
| version | INT | se incrementa cada vez que el admin lo edita |
| activo | BOOLEAN | |
| creado_en | TIMESTAMP | |

## 10. consentimiento_firma
| Campo | Tipo | Notas |
|---|---|---|
| id | BIGSERIAL PK | |
| paciente_id | BIGINT FK → paciente | |
| consentimiento_id | BIGINT FK → consentimiento | |
| version_firmada | INT | copia la versión al momento de firmar |
| firmado_en | TIMESTAMP | |

## 11. pago
| Campo | Tipo | Notas |
|---|---|---|
| id | BIGSERIAL PK | |
| turno_id | BIGINT FK → turno | |
| tipo | VARCHAR(20) | 'SEÑA' / 'PAGO_COMPLETO' / 'SALDO' |
| monto | DECIMAL(10,2) | |
| estado | VARCHAR(20) | 'PENDIENTE'/'CONFIRMADO' |
| metodo | VARCHAR(30) | 'TRANSFERENCIA'/'EFECTIVO'/etc |
| registrado_por | BIGINT FK → usuario | qué admin lo cargó |
| pagado_en | TIMESTAMP | |

## 12. regla_agenda
| Campo | Tipo | Notas |
|---|---|---|
| id | BIGSERIAL PK | |
| servicio_id | BIGINT FK → servicio (nullable) | si es null, aplica a todos |
| antelacion_minima_horas | INT | ej: 24 |
| permite_sobreagendamiento | BOOLEAN | |
| max_turnos_simultaneos | INT | si permite sobreagendar, cuántos |
| frecuencia_dias_por_dni | INT | ej: 7 (una vez por semana) |
| permite_cancelacion | BOOLEAN | |
| permite_reagenda | BOOLEAN | |
| plazo_cancelacion_horas | INT | ej: 12hs antes |

## Índices principales
- UNIQUE — `usuario.email`
- UNIQUE — `paciente.dni`
- UNIQUE — `turno.token_acceso`
- UNIQUE compuesto — `ficha_medica_respuesta(paciente_id, campo_id)`
- INDEX — `turno.fecha_hora`
- INDEX — `turno.paciente_id`, `turno.profesional_id`