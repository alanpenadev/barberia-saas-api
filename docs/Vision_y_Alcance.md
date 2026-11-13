# Visión y alcance — BarberiaSaasApi
Nov 12, 2026 · @Alan

## Propósito y cómo leer este documento
Este documento fija qué se construye y qué queda fuera, y es el entregable que cierra la Fase 0.
Cada elemento lleva una etiqueta para no confundir lo decidido con lo propuesto:

| Etiqueta | Significado |
| --- | --- |
| CONFIRMADA | Decisión que tomaste de forma explícita |
| PROPUESTA | Solución derivada o recomendada que aún no has aprobado |
| PENDIENTE | Decisión o comprobación que falta, con la fase en que se cierra |
| LIMITACIÓN | Comportamiento que el MVP no soporta |

Todavía no existe código ni SQL: nada de lo descrito está implementado ni verificado.

## Visión del producto
BarberiaSaasApi es el backend de una plataforma donde los clientes descubren barberías y reservan citas sin crear una cuenta, y donde los barberos gestionan su agenda, sus ingresos y sus estadísticas.
El proyecto tiene dos fines: servir como primer gran proyecto individual en GitHub, y servir de base técnica para un producto comercial futuro. Se guía por estas prioridades:
- Reglas de negocio reales (disponibilidad, solapamientos, estados, ingresos), no un CRUD.
- SQL Server como parte central del diseño, con EF Core como puente hacia ASP.NET Core.
- Aislamiento entre barberías y protección de datos personales desde el principio.
- Un backend que se pueda desarrollar, ejecutar y probar sin el frontend de React.

## Actores
Hay cuatro actores: el cliente nunca tiene cuenta y la del barbero es opcional.

| Actor | Cuenta | Qué hace en el MVP |
| --- | --- | --- |
| Cliente | No (invitado) | Explora barberías, reserva con nombre y teléfono, y consulta o cancela su cita con el código de gestión y su teléfono |
| Barbero | Opcional | Con cuenta, ve sus citas, sus servicios autorizados y sus ingresos; sin cuenta, el propietario gestiona su agenda |
| Propietario | Sí, único por barbería | Puede administrar varias barberías; gestiona catálogo, barberos, horarios y citas, y ve los reportes de sus barberos |
| Administrador de la plataforma | Sí, rol global | Revisa y aprueba o rechaza barberías, las suspende de inmediato y ejecuta los cierres definitivos |

## Alcance del MVP
El MVP cubre el recorrido completo de una cita, desde que el cliente descubre la barbería hasta que el barbero registra el ingreso.

### Incluye
- Consultar barberías aprobadas, sus servicios y sus barberos, y buscarlas por cercanía (la precisión está pendiente de definir).
- Reservar como invitado con confirmación automática, y consultar o cancelar con el código de gestión.
- Catálogo de servicios por barbería y servicios que ofrece cada barbero.
- Horario semanal por barbero y cálculo de disponibilidad.
- Panel del barbero: citas del día, futuras e historial; marcar citas completadas o no presentadas; registrar atenciones presenciales.
- Ingresos según el importe cobrado y las estadísticas que se definan con reglas claras.
- Revisión de barberías por el administrador: aprobar, rechazar y suspender.

### Queda fuera
- Todo lo listado en Ideas futuras, y lo que figura como pendiente para fases posteriores.

## Decisiones confirmadas
Son 30 decisiones CONFIRMADAS, tomadas por ti; cuando no diste un motivo, la tabla lo indica.

| ID | Decisión | Motivo |
| --- | --- | --- |
| D-01 | Stack: C#, ASP.NET Core Web API, .NET 10 (por verificar), EF Core, SQL Server, Swagger/OpenAPI, Git y GitHub, Visual Studio Community 2026; React después | Base prevista; el backend se desarrolla y prueba sin el frontend |
| D-02 | Database First: scripts SQL versionados como fuente de verdad y scaffold de EF Core | Demostrar dominio real de SQL Server (PK, FK, constraints, índices, consultas) |
| D-03 | Principios de Clean Architecture (separación de responsabilidades y regla de dependencia), lo más simple posible; sin CQRS, MediatR ni repositorios genéricos sin justificarlos | Aprender y practicar de verdad, aceptando el costo extra de código |
| D-04 | Roadmap de 10 fases (0 a 9) | Avanzar de manera ordenada |
| D-05 | Reserva como invitado: se explora y elige la cita sin cuenta; nombre y teléfono se piden al confirmar | Menos fricción al reservar |
| D-06 | El Cliente tiene Id propio, no tiene cuenta y es único por barbería y teléfono | Sin motivo explícito registrado; se analizaron estadísticas por cliente, integridad referencial y aislamiento entre barberías |
| D-07 | Código de gestión de 8 caracteres aleatorios criptográficos (formato XXXX-XXXX), usado junto con el teléfono; límite de intentos, error genérico y vigencia limitada; se guarda sin hash con índice único | Consultar o cancelar sin cuenta con una protección mínima y sin sobreingeniería |
| D-08 | Un mismo teléfono puede tener citas simultáneas | Sin motivo explícito registrado; se analizaron los teléfonos compartidos y no cruzar datos entre barberías |
| D-09 | Confirmación automática: la cita nace Reservada y bloquea el hueco; estados Reservada, Completada, Cancelada y NoPresentado | Respuesta inmediata y determinista |
| D-10 | La cancelación registra estado, fecha y origen (Cliente o Barbería) | Un dato objetivo que no se recupera si no se guarda |
| D-11 | Completada y NoPresentado se marcan solo a mano; una cita pasada sin marcar sigue Reservada, pendiente de resolver, y no cuenta como ingreso ni como inasistencia | Preferir estadísticas incompletas a datos falsos generados automáticamente |
| D-12 | Una atención presencial es una Cita con Canal App o Presencial | Comparten ocupación de horario, solapamientos y estadísticas |
| D-13 | El cliente es opcional solo en atenciones presenciales | Minimizar datos; evitar datos falsos y un Cliente genérico |
| D-14 | Horario semanal recurrente por barbero, con tramos por día; la disponibilidad se calcula, no se guarda | Cada barbero puede tener sus propios días y turnos |
| D-15 | La cita congela el precio y la duración del servicio al reservar | Ingresos y horas de fin no cambian si el servicio cambia |
| D-16 | PrecioAplicado (acordado) e ImporteCobrado (cobrado al completar) son columnas separadas; el ingreso es el importe cobrado de las citas Completada | Que los ingresos reflejen el dinero realmente recibido |
| D-17 | Toda barbería pasa por revisión previa: estados Pending, Approved, Rejected y Suspended; solo Approved es pública y reservable | Con reserva sin cuenta, una barbería falsa podría recopilar teléfonos |
| D-18 | No se puede desactivar a un barbero con citas futuras Reservada; el propietario las cancela antes | El propietario responde por los compromisos de su barbería |
| D-19 | El sistema no cancela citas por su cuenta en el MVP; no existe el origen Sistema | Sin notificaciones, el cliente no se enteraría |
| D-20 | El cierre voluntario de una barbería no tiene estado propio: el propietario resuelve las citas, desactiva a sus barberos y pide el cierre definitivo al administrador | Mantener el MVP simple |
| D-21 | La suspensión por el administrador es inmediata, no depende del propietario y conserva las citas existentes | Debe poder frenar abusos sin su cooperación |
| D-22 | Un propietario por barbería, que puede tener varias barberías; sin cuentas compartidas; la autorización pregunta en un solo componente si un usuario puede actuar sobre una barbería | No anticipar membresías sin requisito, pero poder evolucionar sin reescribir |
| D-23 | La cuenta del barbero es opcional (UsuarioId admite vacío), con índice único filtrado sobre (UsuarioId, BarberiaId) donde UsuarioId no sea vacío y EstaActivo = 1 | Funciona sin invitaciones por correo y permite ser barbero en varias barberías |
| D-24 | El propietario ve las citas y los reportes de los barberos de su barbería; el barbero independiente queda como idea futura | Evitar un concepto comercial sin requisito definido |
| D-25 | Baja lógica (columna de actividad y fecha de baja) en barberos, servicios y relación barbero–servicio, en lugar de borrar filas| Sin motivo explícito registrado; las citas históricas los referencian |
| D-26 | La cita referencia al barbero y al servicio con claves directas y repite BarberiaId en claves foráneas compuestas hacia Barbero y Servicio; que el barbero esté autorizado y activo para el servicio se valida en el código | Sin motivo explícito registrado; se analizó que SQL Server impida mezclar barberías en una cita, sin atar el historial a la relación barbero–servicio |
| D-27 | Las atenciones presenciales ocupan el hueco del barbero, con el mismo cálculo de solapamientos | Comparten la ocupación del horario (tu argumento en D-12) |
| D-28 | Una atención presencial nace directamente Completada | Sin motivo explícito registrado; es un hecho ya ocurrido |
| D-29 | Bloquean el hueco las citas Reservada y Completada; Cancelada y NoPresentado lo liberan | Consecuencia de los estados definidos en D-09 |
| D-30 | El ingreso se atribuye a la fecha del servicio, no a la del momento de marcar la cita | Sin motivo explícito registrado; estadísticas estables por día |

## Propuestas resueltas
Las seis propuestas se aprobaron el 10 de noviembre de 2026 y pasaron a la tabla de decisiones como D-25 a D-30; P-02 se aprobó con otra redacción (ver D-26).

| ID | Propuesta | Por qué se propone |
| --- | --- | --- |
| P-01 | Baja lógica (columna de actividad y fecha de baja) en barberos, servicios y relación barbero–servicio, en lugar de borrar filas | Las citas históricas los referencian; ya usas EstaActivo en el índice, pero no lo confirmaste como regla general |
| P-02 | La cita referencia el par (barbero, servicio) de la relación barbero–servicio | SQL Server garantizaría por sí solo que el barbero está autorizado para ese servicio |
| P-03 | Las atenciones presenciales ocupan el hueco del barbero, con el mismo cálculo de solapamientos | Lo usaste como argumento en D-12, sin confirmarlo como regla aparte |
| P-04 | Una atención presencial nace directamente Completada | Es un hecho ya ocurrido; no pasa por Reservada |
| P-05 | Bloquean el hueco las citas Reservada y Completada; Cancelada y NoPresentado lo liberan | Consecuencia de los estados definidos en D-09 |
| P-06 | El ingreso se atribuye a la fecha del servicio, no a la del momento de marcar la cita | Estadísticas estables por día |

## Decisiones pendientes
Dieciséis decisiones siguen PENDIENTES; solo la zona horaria debe cerrarse antes del modelo físico de datos.

| ID | Pendiente | Se cierra en |
| --- | --- | --- |
| Q-01 | Zona horaria: tipo de dato de las fechas de la cita (hora local o instante en UTC) y zona de cada barbería | Antes del modelo físico, en la Fase 2 (decisión 4b) |
| Q-02 | Qué pasa con las citas existentes al desactivar un servicio (inclinación: respetarlas) | Fase 6 |
| Q-03 | Estructura formal de Clean Architecture (proyectos y capas) y relación entre el modelo de dominio y las entidades del scaffold | Fase 2 |
| Q-04 | ASP.NET Core Identity con Database First, tipo de Id (entero o GUID), herramienta de versionado de scripts SQL y convención de nombres (por ejemplo Activo o EstaActivo) | Fase 2 |
| Q-05 | Concurrencia al crear citas y estrategia de pruebas de integración con SQL Server | Fases 2 y 6 |
| Q-06 | Quién inicia el alta de una barbería: autoservicio o administrador | Fase 7 |
| Q-07 | Matriz de permisos por rol, incluido quién marca Completada o NoPresentado y quién edita el importe | Borrador en la Fase 1; implementación en la Fase 7 |
| Q-08 | Reglas de disponibilidad: granularidad de huecos, antelación mínima y máxima, descansos y feriados | Fase 6 |
| Q-09 | Bloqueos de agenda (vacaciones, ausencias) y si entran en el MVP | Fase 6 |
| Q-10 | Cancelaciones: plazo límite, penalizaciones y reprogramación | Fase 6 |
| Q-11 | Canal de notificación al cliente (SMS, WhatsApp o correo) | Posterior al MVP |
| Q-12 | Búsqueda por proximidad: precisión necesaria y método de cálculo de distancias | Fases 2 y 5 |
| Q-13 | Privacidad: normativa de protección de datos del mercado objetivo y conflicto entre conservar el historial y borrar datos personales (anonimización) | Documento de seguridad y privacidad |
| Q-14 | Verificación de documentos del propietario y nueva revisión tras editar datos de una barbería aprobada | Posterior al MVP |
| Q-15 | Definición exacta de cada estadística, incluida la de clientes atendidos con atenciones anónimas | Fase 8 |
| Q-16 | Idioma de los nombres de estados y columnas (inglés o español) | Fase 1 (glosario) |

## Limitaciones conocidas
El MVP acepta once LIMITACIONES; las más delicadas son L-01 (abuso del endpoint de reservas) y L-04 (sin notificaciones).

| ID | Limitación | Consecuencia |
| --- | --- | --- |
| L-01 | El endpoint de creación de citas no tiene defensa contra abuso hasta añadir límite de solicitudes, CAPTCHA u otras | Un bot podría ocupar huecos de un barbero |
| L-02 | Un mismo teléfono puede tener citas solapadas | Se aceptan los casos legítimos |
| L-03 | El cliente se reconoce por un teléfono sin verificar | Las métricas de clientes son aproximadas; un teléfono compartido cuenta como una persona |
| L-04 | No hay notificaciones al cliente | No se entera si la barbería cancela o es suspendida |
| L-05 | El importe cobrado es editable sin permisos ni auditoría definidos | Posible error o alteración sin rastro |
| L-06 | Una barbería tiene un solo propietario | Una cuenta es punto único de fallo; el cambio de propietario o la recuperación los hace el administrador a mano |
| L-07 | Una barbería aprobada puede editar sus datos sin nueva revisión | Posible abuso tras la aprobación |
| L-08 | Si no entran en el MVP, no hay bloqueos de agenda | Un barbero no puede cerrar un día sin cancelar citas una a una |
| L-09 | Las citas pasadas sin marcar no cuentan | Ingresos y estadísticas incompletos si no se marcan |
| L-10 | El cierre definitivo de una barbería lo ejecuta el administrador | No es un trámite de autoservicio |
| L-11 | Las atenciones presenciales pueden ser anónimas | Los clientes atendidos se informan por separado |

## Ideas futuras
Estas ideas quedan fuera del MVP; ninguna es una decisión.
- Barbero independiente, con ingresos privados según su tipo de contrato.
- Membresías con roles y varios administradores por barbería.
- Notificaciones al cliente y verificación del teléfono con un código (OTP).
- Pagos, depósitos y anticipos.
- Penalizaciones, motivos de cancelación e historial de incidencias o reputación de clientes.
- Auditoría de cambios, incluido quién modifica un importe.
- Descuentos, extras y propinas como conceptos propios.
- Historial de precios del catálogo y varias monedas.
- Reprogramación de citas.
- Cierre voluntario de barberías con estado propio.
- Cancelación automática por el sistema, que exigiría un origen Sistema y notificaciones.

## Requisitos de seguridad registrados
Estos requisitos se diseñan en la Fase 2 y se implementan a partir de la Fase 7; ya quedan fijados para no perderlos.

| ID | Requisito | Origen |
| --- | --- | --- |
| S-01 | Aislamiento entre barberías: el backend nunca confía en un Id enviado por el cliente, y toda comprobación de acceso pasa por un único componente de autorización | Tu prioridad y D-22 |
| S-02 | Defensa del endpoint de creación de citas: límite de solicitudes por IP y CAPTCHA validado en el servidor, u otras medidas | Limitación L-01 que aceptaste |
| S-03 | La vinculación entre barbero y usuario usa un mecanismo de consentimiento (PIN de 6 dígitos o confirmación de solicitud), no un correo escrito por el propietario. Propuesta: que el PIN caduque y limite los intentos | Tu requisito para la Fase 7 |
| S-04 | Contraseñas nunca en texto plano; secretos y cadenas de conexión fuera del código y del repositorio; cuenta semilla del administrador sin contraseña en el código | Brief del proyecto |
| S-05 | Validación de entrada en el backend y errores que no filtran detalles internos | Brief del proyecto |

## Estado de implementación y supuestos por verificar
No hay nada implementado ni verificado: no existe código, base de datos ni pruebas.
Estos supuestos técnicos se comprueban en la Fase 3, en tu máquina, con comandos que te indicaré y cuya salida me pegarás:
1. Que el SDK de .NET 10 está instalado y que es compatible con la versión de EF Core elegida y con el proveedor de SQL Server.
2. Que Visual Studio Community 2026 está disponible.
3. Que existe una instancia de SQL Server accesible para desarrollo.
4. Que la herramienta `dotnet ef` está instalada, para generar el scaffold de Database First.

## Qué sigue
La Fase 0 queda cerrada con esta versión y empieza la Fase 1, que produce cuatro documentos en este orden:
1.  Glosario: el significado de cada término del dominio.
2.  Reglas de negocio, con los diagramas de estados de la cita y de la barbería.
3.  Requisitos funcionales y no funcionales, con un borrador de la matriz de permisos.
4.  Modelo conceptual: entidades y relaciones, sin tablas ni tipos de dato.
La zona horaria (Q-01) sigue pendiente y se cierra antes de terminar la Fase 2.
Desde esta versión, el repositorio es la fuente de verdad: los cambios posteriores se hacen allí y se commitean.

La Fase 1 produce los documentos de requisitos funcionales y no funcionales, reglas de negocio con diagramas de estados, glosario y modelo conceptual. La zona horaria (Q-01) se cierra antes de terminar la Fase 2.
Hasta la Fase 3 estos documentos viven aquí; después se exportan a Markdown y pasan a la carpeta `docs/` del repositorio.