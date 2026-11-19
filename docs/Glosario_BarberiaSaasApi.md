Glosario — BarberiaSaasApi
16 nov 2026

Este glosario fija una sola palabra para cada concepto del dominio, y esa palabra significa lo mismo en las reglas, en SQL Server y en el código. Solo define lo que ya está decidido; lo pendiente se marca como tal. Cada término indica su tipo (persona, registro, cuenta o rol), qué es y con qué no debe confundirse.
Grupo 1: actores y cuentas
Cliente (Registro)
El registro con ID propio que el sistema crea, o reutiliza si ya existía, al confirmarse una cita o atención presencial, identificado de forma única por la barbería y el número de teléfono (D-06). Sirve para asociar las citas y el historial en esa barbería, y es opcional en las atenciones presenciales (D-13).

No confundir con: Un Usuario: no tiene cuenta ni credenciales para iniciar sesión.
Invitado (Persona)
Cualquier persona que usa el sistema sin crear una cuenta, lo que le permite explorar barberías, elegir horarios, reservar una cita (D-05) y consultar o cancelar después con su código de gestión. Al confirmar una cita con su nombre y teléfono, el sistema crea o asocia un registro de Cliente.

No confundir con: Un Usuario: no tiene cuenta. Tampoco es el Cliente, que es el registro y no la persona.
Usuario (Cuenta)
Toda cuenta que posee credenciales para iniciar sesión y operar dentro del sistema.

No confundir con: Un Cliente o un Invitado: ninguno de los dos tiene cuenta.
Barbero (Registro)
El perfil de quien presta servicios en una barbería y atiende citas; pertenece a una sola barbería. Puede estar vinculado a una cuenta de Usuario, que es la del propio barbero o, si el propietario también corta, la del propietario; el vínculo es opcional (D-23).

No confundir con: Un Usuario, que es la cuenta, o el rol de Propietario: el rol pertenece a la cuenta y el Barbero es un registro.
Propietario (Rol)
El rol de un Usuario que administra una o varias barberías, siendo único por cada barbería (D-22), y que responde por la gestión de las citas (D-18) y maneja su catálogo, sus barberos y sus horarios.

No confundir con: Un Administrador de la plataforma: no puede aprobar, rechazar, suspender ni cerrar barberías.
Administrador de la plataforma (Rol)
El rol de un Usuario con autoridad sobre el estado de las barberías dentro de la plataforma, encargado de aprobarlas, rechazarlas, suspenderlas o cerrarlas (D-17, D-20, D-21). Sus demás permisos están pendientes (Q-07).

No confundir con: Un Propietario de barbería.
Grupo 2: la barbería y su estado
Barbería (Registro)
La unidad de negocio registrada en la plataforma, gestionada por un único propietario (D-22) y sujeta a un ciclo de vida con un estado específico (D-17).

No confundir con: Una cuenta de Usuario.
Revisión previa (Proceso)
El filtro operativo obligatorio que ejecuta el Administrador sobre una barbería recién registrada para impedir que sea visible o acepte reservas mientras no se aprueba (D-17).

No confundir con: Una validación automática.
En revisión (Estado)
El estado inicial de una barbería recién creada, que no es visible al público ni acepta reservas mientras espera la revisión del Administrador (D-17).

No confundir con: Aprobada.
Aprobada / Barbería activa (Estado)
El estado en el que la barbería cuenta con la autorización del Administrador para operar, siendo visible al público y aceptando reservas (D-17).

No confundir con: El indicador EstaActivo de micro-entidades como barberos o servicios.
Rechazada (Estado)
El estado en el que el Administrador rechaza la solicitud de apertura de la barbería (D-17), por lo que no es visible ni acepta reservas en el sistema.

No confundir con: En revisión.
Suspendida (Estado)
El estado en el que la barbería deja de ser visible al público y de aceptar reservas nuevas, abarcando dos casos: una sanción inmediata dictada por el Administrador que conserva las citas existentes sin cancelarlas (D-21), o un cierre definitivo solicitado por el Propietario (D-31).

No confundir con: Rechazada: una barbería suspendida sí fue aprobada alguna vez.
Cierre definitivo (Proceso)
El flujo donde el Propietario resuelve las citas futuras y desactiva a sus barberos antes de solicitar el cierre, que ejecuta el Administrador dejando la barbería en estado Suspendida con su motivo de cierre (D-20, D-31).

No confundir con: Una suspensión por sanción (D-21).
Motivo de suspensión (Atributo)
El dato asociado al estado Suspendida que distingue su origen: una sanción aplicada por el Administrador o un cierre definitivo solicitado por el Propietario.

No confundir con: El estado Suspendida en sí: es el mismo en ambos casos, y el motivo es el dato que lo explica.
Baja lógica (Mecanismo técnico)
La acción de marcar un registro con un indicador de inactividad en la base de datos (D-25) en lugar de borrar físicamente la fila, abarcando a barberos, servicios y la relación barbero–servicio para preservar la integridad referencial del historial.

No confundir con: Borrar físicamente el registro (SQL DELETE).
Grupo 3: catálogo y agenda
Servicio (Registro)
Unidad de prestación ofrecida por una barbería con un precio y una duración de catálogo definidos (D-15). Pertenece exclusivamente a una barbería y se desactiva mediante baja lógica para conservar el historial de citas pasadas sin borrarlo físicamente (D-25).

No confundir con: Una Cita, que es la reserva de un servicio en una fecha, ni un Servicio autorizado para un barbero, que es el vínculo con un barbero.
Catálogo de servicios (Colección de registros)
Conjunto único de servicios administrado por una barbería. Es centralizado a nivel de local: no se duplica ni se crean listas independientes por cada barbero.

No confundir con: Una lista de servicios por barbero.
Servicio autorizado para un barbero (Relación — muchos a muchos entre barbero y servicio)
Vínculo explícito entre un barbero y un servicio de una misma barbería. Al igual que el servicio, admite baja lógica mediante un indicador de estado para dejar de habilitarlo sin perder el registro histórico (D-25).

No confundir con: Un Servicio del catálogo: el servicio pertenece a la barbería, y la autorización es la habilitación específica para que un barbero en concreto pueda realizarlo.
Horario de trabajo (Configuración)
Configuración semanal y recurrente definida por barbero, compuesta por uno o más tramos horarios por día (D-14). Delimita los bloques de tiempo en los que el barbero trabaja y puede recibir citas.

No confundir con: La Disponibilidad: el horario es lo configurado, y la disponibilidad es lo que queda tras descontar citas.
Disponibilidad (Cálculo)
Resultado dinámico de cruzar los tramos del horario de trabajo de un barbero contra sus citas que bloquean la agenda, es decir, las Reservada o Completada (D-14, D-29). No se almacena en base de datos: se calcula al consultar una fecha.

No confundir con: Un barbero activo: estar activo no implica tener disponibilidad.
Hueco (Intervalo de tiempo)
Intervalo de tiempo continuo dentro de un mismo tramo del horario de trabajo de un barbero (D-29). Un hueco libre es un espacio sin bloqueos con capacidad suficiente para alojar íntegramente la duración de un servicio; un hueco ocupado es el intervalo bloqueado por una cita Reservada o Completada que impide nuevas reservas en ese rango.

No confundir con: Una Cita: la cita ocupa un hueco, pero el hueco es el intervalo.
Solapamiento (Concepto)
Intersección temporal entre dos intervalos donde uno comienza antes de que el otro haya terminado. No equivale a tener la misma hora de inicio: dos citas con distinta hora de inicio se solapan si sus rangos se cruzan en cualquier punto, mientras que dos citas contiguas (una termina exactamente cuando empieza la otra) no se solapan.

No confundir con: Tener la misma hora de inicio.
Duración de catálogo (Dato)
Tiempo estimado configurado actualmente en el servicio de la barbería; es editable y sirve de referencia para nuevas reservas (D-15).

No confundir con: La duración de cita: la del catálogo puede cambiar, y la de cita no cambia por ello.
Duración de cita (congelada) (Dato)
Tiempo capturado y guardado en la cita al confirmarla; no cambia cuando se edita el servicio en el catálogo (D-15).

No confundir con: La duración de catálogo.
Hora de fin (Dato calculado)
Momento en que concluye una cita y se libera el barbero. Se calcula sumando la duración de cita (congelada) a su hora de inicio (D-15).

No confundir con: La hora de fin recalculada con la duración actual del catálogo.
Grupo 4: la cita
Cita (Registro)
Registro de la prestación de un servicio asignado a un barbero en una fecha y hora determinadas, ya sea como una reserva futura o como una atención presencial ya realizada (D-09, D-12, D-15).

No confundir con: Un Servicio, que es el concepto del catálogo, ni un Hueco, que es el intervalo de tiempo.
Canal (Atributo)
Dato de la cita que indica el medio por el cual se originó el registro; admite únicamente los valores App o Presencial (D-12).

No confundir con: El Origen de la cancelación: el canal indica cómo nació la cita, no quién la canceló.
Atención presencial (Variante de cita)
Registro de un servicio prestado directamente en el local sin reserva previa en línea. Se guarda como una Cita con Canal Presencial que nace directamente en estado Completada, bloquea el hueco según D-27 y admite que el cliente sea opcional (D-12, D-13, D-28).

No confundir con: Una reserva web hecha por un Invitado, ni una cita que pasa por el estado Reservada.
Atención anónima (Variante de cita)
Atención presencial registrada sin vincular un registro de Cliente, dejando vacío el identificador del cliente (D-13).

No confundir con: Una atención presencial normal, que sí asocia los datos a un registro de Cliente.
Reservada (Estado)
Estado inicial de toda cita agendada a través de la plataforma (Canal App) mediante confirmación automática; bloquea el hueco en la agenda del barbero hasta que se resuelve (D-09, D-29).

No confundir con: El estado inicial de una Atención presencial, que nace directamente Completada.
Completada (Estado)
Estado final de una cita que efectivamente se realizó, o estado inicial de una atención presencial. Mantiene bloqueado el hueco y es el único estado cuyo ImporteCobrado suma a los ingresos y estadísticas del barbero y la barbería (D-11, D-16, D-29).

No confundir con: El estado Reservada: una cita ya pasada en el reloj pero sin marcar no pasa a Completada automáticamente.
Cancelada (Estado)
Estado final de una cita anulada antes de su realización, ya sea por el cliente o por la barbería. Libera inmediatamente el hueco ocupado para futuras reservas y registra la fecha y el origen de la cancelación (D-10, D-29).

No confundir con: El estado NoPresentado: la cancelación ocurre antes de la atención y registra su origen, mientras que la inasistencia ocurre cuando el cliente no acude.
NoPresentado (Estado)
Estado final marcado manualmente cuando el cliente no acude a una cita Reservada. Libera el hueco en la agenda y registra la inasistencia en las estadísticas, pero no genera ingresos (D-11, D-29).

No confundir con: Una Cita pendiente de resolver ni una cita Cancelada.
Cita pendiente de resolver (Condición operativa)
Cita en estado Reservada cuya hora de fin ya transcurrió sin haber sido marcada manualmente como Completada, Cancelada o NoPresentado (D-11).

No confundir con: El estado En revisión de una barbería ni una cita que solo ha comenzado.
Origen de la cancelación (Atributo)
Dato obligatorio cuando una cita pasa al estado Cancelada que identifica quién ejecutó la anulación; en el MVP admite únicamente Cliente o Barbería (D-10). No existe el origen Sistema porque el sistema no cancela citas de forma automática (D-19).

No confundir con: El Canal de la cita (App o Presencial).
Código de gestión (Atributo)
Cadena aleatoria de 8 caracteres (formato XXXX-XXXX) generada al crear una cita por App, que permite al Invitado consultar o gestionar su cita, junto con su teléfono, sin tener una cuenta (D-07).

No confundir con: Una contraseña de Usuario o una cuenta de acceso al sistema.
Grupo 5: dinero y estadísticas
Precio aplicado (Dato)
Valor monetario del servicio fijado y congelado en el momento exacto en que se confirma la cita o se registra la atención presencial, independientemente de futuros cambios en el catálogo (D-15, D-16).

No confundir con: El precio actual del catálogo, que puede cambiar, y el Importe cobrado, que es lo realmente cobrado al completar la cita.
Importe cobrado (Dato)
Monto monetario realmente cobrado al completarse una cita, que puede coincidir o diferir del precio aplicado al agendar (D-16).

No confundir con: El Precio aplicado: uno es el valor fijado al confirmar la cita y el otro es lo que efectivamente se cobra al completarla.
Ingreso (Dato calculado)
Suma total de los importes cobrados exclusivamente de aquellas citas que se encuentran en estado Completada (D-16).

No confundir con: Las reservas futuras (Reservada), las cancelaciones o las citas marcadas como NoPresentado, que no suman ingresos; tampoco es la suma de los precios aplicados.
Cliente atendido (Condición de registro)
Condición de un registro de Cliente asociado a una cita o atención presencial completada (D-13). Al no existir registro de Cliente en una atención anónima, esta no constituye un cliente atendido; su reporte en estadísticas queda pendiente de Q-15.

No confundir con: Un Cliente frecuente: para ser cliente atendido basta con tener una cita completada, sin depender del umbral de frecuencia pendiente en Q-15.
Cliente frecuente (Concepto estadístico)
Cliente atendido que acude con recurrencia a la misma barbería; el umbral de recurrencia está pendiente de definir (Q-15).

No confundir con: Un Cliente atendido: cliente atendido aplica a partir de una cita completada, mientras que el criterio de recurrencia de cliente frecuente está pendiente en Q-15.
Servicio reservado (Concepto)
Hecho de que un servicio haya sido seleccionado al agendar una cita por la plataforma (Canal App), independientemente de que la cita posteriormente se complete, cancele o termine en NoPresentado (D-11, D-15). Su forma de conteo en estadísticas queda pendiente de Q-15.

No confundir con: Un Servicio realizado: reservar es seleccionar el servicio al agendar, mientras que realizado es haber completado su prestación.
Servicio realizado (Concepto)
Instancia de un servicio cuya prestación física ya ocurrió y cuya cita se encuentra en estado Completada (D-11, D-16).

No confundir con: Un Servicio reservado: el servicio realizado es el que efectivamente genera el Importe cobrado y alimenta los ingresos.
Convención de nombres
Todos los identificadores (estados, tablas, columnas y clases) van en español y sin tildes ni eñes; en los textos se escriben con tilde. Los estados de la barbería que figuran en inglés en la versión 1.1 de la visión se renombran así: (pendiente de completar en el documento original).