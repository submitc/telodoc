# Telo · Alcance de la solución V1

Este es el documento de aprobación para el directorio del hotel. Telo permitirá conocer el hotel, consultar disponibilidad, solicitar reservas y administrarlas desde una única aplicación.

## Organización funcional de la V1

Telo se desplegará como un solo sistema, pero con límites claros para que pueda crecer sin mezclar responsabilidades.

| Límite | Clasificación | Responsabilidad |
| --- | --- | --- |
| Reservas | Módulo **Core** | Disponibilidad, solicitudes, bloques, vouchers, pagos manuales e historial de la reserva. |
| Catálogo de habitaciones y tarifas | Módulo de soporte | Oferta vendible, habitaciones físicas y precios. |
| Configuración del hotel | Módulo de soporte compartido | Datos institucionales, idioma, zona horaria, contenido público y medios de pago. |
| Identidad y acceso | Capacidad transversal | Personal autorizado, permisos y trazabilidad. |
| Web pública y panel administrativo | Capa de presentación | Canales desde los que visitante y personal usan los módulos; no son dueños de datos de negocio. |

## Capa de presentación: Web pública del hotel

**Descripción.** Presenta el hotel, sus habitaciones y servicios; guía al visitante hacia la reserva.

### Requerimientos

| ID | Requerimiento |
| --- | --- |
| W-01 | La web pública mostrará Inicio, Habitaciones, Servicios, Galería, Ubicación y Contacto. |
| W-02 | Cada sección mostrará contenido propio del hotel: presentación, tipos de habitación, servicios, imágenes, dirección, mapa o indicaciones y medios de contacto. |
| W-03 | El visitante podrá cambiar entre español e inglés. Los textos estáticos y el contenido administrable se mostrarán en el idioma elegido. |
| W-04 | La página será usable desde celular, tableta y computadora. |
| W-05 | Desde Inicio y Habitaciones existirá una llamada clara para iniciar la reserva. |
| W-06 | La galería mostrará imágenes proporcionadas o aprobadas por el hotel. La administración de archivos y un CMS no forman parte de V1. |
| W-07 | Ubicación y contacto mostrarán los datos configurados por el hotel; no se integra un servicio externo de mapas en V1. |

### Reglas confirmadas y precisiones restantes

- La web pública estará disponible en español e inglés.
- Se cargarán antes de publicar los datos reales, textos, servicios, imágenes y contacto del hotel.
- No se incluyen CMS, administración de archivos ni integración automática de mapas en V1.

## Módulo Core: Reservas, disponibilidad y pagos manuales

**Descripción.** Permite solicitar una o varias habitaciones bajo una referencia común y evita vender más habitaciones de las disponibles.

### Requerimientos

| ID | Requerimiento |
| --- | --- |
| R-01 | La web permitirá elegir llegada, salida y cantidad de huéspedes. |
| R-02 | Una solicitud podrá incluir varias habitaciones y distintos tipos, bajo una referencia común. |
| R-03 | El sistema verificará capacidad, habitaciones habilitadas y reservas superpuestas por cada noche. |
| R-04 | Una reserva pendiente retendrá el bloque completo de habitaciones. |
| R-05 | El sistema mostrará tarifas variables por fecha en soles, con impuesto del 18% incluido. |
| R-06 | La solicitud conservará el precio, tipo, contacto y condiciones mostradas al visitante. |
| R-07 | Una confirmación concurrente no podrá vender más habitaciones de las disponibles. |

### Reglas confirmadas y precisiones restantes

- Las reservas pueden incluir varias habitaciones y tipos distintos.
- Una reserva `PENDIENTE` bloquea el cupo; `CONFIRMADA` lo conserva; `CANCELADA` o `VENCIDA` lo libera.
- Las tarifas son variables por fecha, en `PEN`, con 18% incluido.
- Se conserva el precio registrado al enviar la solicitud aunque cambien las tarifas.
- Falta definir si un bloque puede tener fechas distintas por habitación, si admite cancelación parcial y si se retiene cupo mientras el visitante arma la selección antes de enviarla.

### Estados de reserva

| Estado | Significado |
| --- | --- |
| `PENDIENTE` | Retiene habitaciones mientras espera o revisa el pago. |
| `CONFIRMADA` | Pago aprobado por el personal; conserva las habitaciones. |
| `CANCELADA` | Deja de retener habitaciones; no genera devolución. |
| `VENCIDA` | No se recibió voucher oportuno; libera habitaciones. |

### Pago y voucher

**Descripción.** Informa cómo pagar, recibe el comprobante y permite al personal validar manualmente el pago antes de confirmar.

#### Requerimientos

| ID | Requerimiento |
| --- | --- |
| P-01 | Se mostrarán QR de Yape y/o cuentas de depósito configuradas por el hotel. |
| P-02 | El voucher podrá recibirse por la web o WhatsApp del personal. |
| P-03 | El plazo para recibir voucher será de dos horas para llegada el mismo día y cuatro horas para llegada posterior. |
| P-04 | El personal podrá aprobar cualquier monto, sea pago total o adelanto, para confirmar una reserva. |
| P-05 | Los pagos aprobados serán no reembolsables. |
| P-06 | Sin voucher oportuno, la reserva vencida liberará el bloque completo. |
| P-07 | Subir o recibir un voucher no confirma por sí solo; requiere revisión del personal. |

#### Reglas confirmadas y precisiones restantes

- La web mostrará QR de Yape o cuentas de depósito; no habrá integración automática con Yape, bancos o WhatsApp.
- El voucher puede subirlo el visitante en la web o enviarlo al WhatsApp del personal.
- El plazo es de dos horas para llegada el mismo día y cuatro horas para llegada posterior.
- El personal puede aprobar cualquier monto, total o adelanto. Todo pago aprobado es no reembolsable.
- Un voucher recibido no confirma automáticamente; el personal verifica la operación externa.
- Falta definir si un voucher rechazado puede reenviarse y hasta cuándo.

## Capacidad transversal: Identidad y acceso

**Descripción.** Controla la identidad del personal, sus permisos de acceso y la trazabilidad de las operaciones. La administración es la interfaz desde la que el personal autorizado usa los módulos del sistema; no es un módulo de negocio que sea dueño de datos del hotel, reservas o contabilidad.

### Requerimientos

| ID | Requerimiento |
| --- | --- |
| A-01 | El personal autorizado iniciará sesión en la administración. |
| A-02 | Recepción gestionará reservas y vouchers. Administración podrá además operar catálogo y configuración del hotel. |
| A-03 | Las reservas conservarán historial de cambios, vouchers y decisiones del personal. |
| A-04 | Las acciones administrativas requerirán autorización verificada en el servidor. |

### Reglas confirmadas y precisiones restantes

- Recepción administra reservas y vouchers.
- Administración tiene permiso para operar catálogo y configuración, pero estos datos siguen perteneciendo a su propio módulo.
- Las decisiones del personal y los vouchers se conservarán en el historial de la reserva.
- No se incluye gestión configurable de permisos ni integración con Supabase Auth.

## Módulo de soporte: Configuración del hotel

**Descripción.** Es el módulo fundacional que administra los datos compartidos de un hotel. Reservas, Catálogo y futuros módulos como Contabilidad los consultan, pero no los modifican directamente.

### Requerimientos

| ID | Requerimiento |
| --- | --- |
| CFG-01 | Administración podrá mantener nombre, dirección, contacto, ubicación, zona horaria, textos públicos, QR de Yape y cuentas de depósito del hotel. |
| CFG-02 | Administración podrá activar idiomas disponibles, elegir el idioma predeterminado y registrar traducciones del contenido administrable. La V1 inicia con español e inglés. |
| CFG-03 | Un idioma solo podrá publicarse para el visitante cuando tenga traducciones completas del contenido dinámico y mensajes estáticos disponibles en la aplicación. |

### Reglas confirmadas y precisiones restantes

- Configuración del hotel es la única propietaria de los datos institucionales, medios de pago, zona horaria, contenido público e idiomas.
- Contabilidad futura consultará identidad legal, moneda, impuestos y zona horaria desde este módulo; no duplicará esos datos.
- Idioma es parte de la configuración del hotel, no un módulo separado. Español e inglés están activos en V1; se podrán habilitar otros idiomas cuando estén traducidos.
- El personal puede cargar traducciones de contenido del hotel. Los textos estáticos de la aplicación requieren su archivo de traducción entregado por el equipo técnico antes de habilitar un idioma para el público.
- Falta cargar los datos reales del hotel, textos, imágenes, QR, cuentas y zona horaria antes de publicar.

## Módulo de soporte: Catálogo de habitaciones y tarifas

**Descripción.** Administra el inventario vendible del hotel y sus precios. Reservas consulta este módulo para conocer qué se puede vender, pero conserva snapshots para no alterar contratos ya registrados.

### Requerimientos

| ID | Requerimiento |
| --- | --- |
| CAT-01 | Administración podrá crear, editar, activar o desactivar tipos de habitación con código, capacidad y contenido en español e inglés. |
| CAT-02 | Administración podrá crear, editar, habilitar o deshabilitar habitaciones físicas, identificadas por código y asociadas a un tipo de habitación. |
| CAT-03 | Administración podrá definir tarifas variables por tipo de habitación y fecha, en `PEN` con impuesto del 18% incluido. |
| CAT-04 | Los cambios de catálogo y tarifas no alterarán la información histórica conservada en reservas existentes. |

### Reglas confirmadas y precisiones restantes

- Una habitación física pertenece a un tipo; el visitante reserva el tipo y el personal asigna la habitación posteriormente.
- Una habitación deshabilitada deja de ofrecerse para nuevas reservas.
- Las tarifas pueden variar por fecha y se conservan como historial para respetar precios ya aceptados.
- Catálogo no es dueño de los datos institucionales ni de idiomas: los consume desde Configuración del hotel.

## Fuera de alcance V1

- Pasarela de pagos e integración con Yape, bancos o WhatsApp.
- Facturación electrónica, contabilidad, caja e inventarios.
- Housekeeping, aplicación móvil y API pública.
- Devoluciones automáticas o gestión contable de pagos.

## Evolución modular prevista

La V1 se construye como una sola aplicación, organizada internamente por módulos. Esto evita incorporar complejidad de microservicios antes de necesitarla y permite que cada futuro módulo tenga sus propios datos y reglas.

| Módulo futuro | Responsabilidad | Situación en V1 |
| --- | --- | --- |
| Recepción y estadía | Check-in, check-out y operación durante la estadía. | Fuera de alcance; candidato Core cuando tenga alcance propio. |
| Contabilidad | Libro contable, ingresos, gastos y conciliación. | Fuera de alcance; no se crean tablas contables. |
| Caja y facturación electrónica | Cobros, comprobantes fiscales y caja. | Fuera de alcance; el voucher solo confirma manualmente la reserva. |
| Personal y planilla | Colaboradores, turnos, asistencia y remuneraciones. | Fuera de alcance. |
| Inventario y compras | Insumos, proveedores y existencias. | Fuera de alcance. |
| Housekeeping | Limpieza, estado operativo y tareas de habitación. | Fuera de alcance. |
| Reportes | Indicadores operativos y financieros. | Se definirá cuando existan los módulos fuente. |

Estos módulos futuros son candidatos surgidos del ciclo hotelero; requieren su propio alcance antes de modelar entidades o tablas. Cada uno deberá integrarse mediante contratos internos claros y conservar su propio modelo de datos. No debe reutilizar tablas de reservas, catálogo o usuarios para almacenar sus operaciones particulares.

## Documentación técnica

El equipo técnico dispone de [C1 · Contexto](tecnica/c1-contexto.md), [C2 · Contenedores](tecnica/c2-contenedores.md) y [diseño del dominio y datos](tecnica/dominio-v1.md). Esos documentos explican cómo se implementarán estos módulos.
