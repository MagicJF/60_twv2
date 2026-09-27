# Feature Specification: Gestión de tareas en MyWiki

**Feature Branch**: `001-gestionar-tareas`

**Created**: 2026-09-27

**Status**: Draft

**Input**: User description: "Crear una MVP de gestión de tareas usando MyWiki y su WebServer API. Debe permitir consultar el estado del servidor, listar tareas, ver una tarea, crear una tarea, cambiar su estado y eliminarla. Cada tarea será un tiddler con campos tipo: tarea, estado: pendiente, proyecto: MiProyecto. La MVP debe usar Get Server Status, Get All Tiddlers, Get Tiddler, Put Tiddler y Delete Tiddler. Los tiddlers seguirán estando en MyWiki/tiddlers, en castellano y escritos en WikiText."

## Clarifications

### Session 2026-09-27

- Q: ¿Qué mecanismo ya disponible en MyWiki permite que los tiddlers WikiText invoquen las cinco operaciones de la WebServer API? → A: Una macro o widget existente.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Consultar y explorar tareas (Priority: P1)

Como persona usuaria, quiero comprobar que el servidor está disponible y explorar las
tareas existentes para saber qué trabajo hay pendiente y abrir el detalle de una tarea.

**Why this priority**: Es el punto de entrada y permite consultar información antes de
realizar cambios.

**Independent Test**: Con el servidor activo y varias tareas guardadas, consultar su estado,
ver la lista, abrir una tarea y comprobar que los datos mostrados coinciden con ella.

**Acceptance Scenarios**:

1. **Given** el servidor responde, **When** la persona abre la vista principal, **Then** ve
   una indicación de disponibilidad y una lista de tareas.
2. **Given** hay una tarea existente, **When** la persona la selecciona, **Then** ve su
   título, estado, proyecto y contenido.
3. **Given** no hay tareas que coincidan, **When** la persona consulta la lista, **Then** ve
   un mensaje claro de lista vacía.
4. **Given** el servidor no responde, **When** la persona consulta el estado o las tareas,
   **Then** recibe una indicación comprensible de que no se pudo completar la consulta.

---

### User Story 2 - Crear una tarea (Priority: P2)

Como persona usuaria, quiero registrar una tarea con título y descripción para incorporarla
al trabajo del proyecto.

**Why this priority**: La creación convierte la consulta en una herramienta útil de gestión.

**Independent Test**: Crear una tarea con título y descripción, volver a la lista y abrirla
para confirmar que se guardó con estado pendiente y proyecto MiProyecto.

**Acceptance Scenarios**:

1. **Given** el servidor está disponible, **When** la persona proporciona un título y crea
   la tarea, **Then** la tarea queda guardada, aparece en la lista y tiene estado pendiente
   y proyecto MiProyecto.
2. **Given** el título está vacío, **When** la persona intenta crear la tarea, **Then** se
   informa del dato requerido y no se crea una tarea sin título.
3. **Given** el servidor rechaza la creación, **When** la persona guarda una tarea,
   **Then** se informa que no se confirmó el guardado y los datos introducidos no se
   presentan como una tarea ya creada.

---

### User Story 3 - Cambiar estado y eliminar tarea (Priority: P3)

Como persona usuaria, quiero actualizar el estado de una tarea o eliminarla para mantener
la lista al día.

**Why this priority**: Completa el ciclo básico de mantenimiento una vez que las tareas ya
se pueden consultar y crear.

**Independent Test**: Cambiar una tarea de pendiente a completada y verificar el nuevo
estado; después eliminar otra tarea y comprobar que ya no aparece en la lista.

**Acceptance Scenarios**:

1. **Given** una tarea pendiente, **When** la persona cambia su estado a completada,
   **Then** el nuevo estado queda guardado y se muestra al volver a consultar la tarea.
2. **Given** una tarea existente, **When** la persona confirma su eliminación, **Then** deja
   de estar disponible en la lista y en su vista de detalle.
3. **Given** el guardado del cambio o la eliminación falla, **When** la persona confirma la
   acción, **Then** recibe un mensaje de error y la vista no afirma que el cambio tuvo éxito.

### Edge Cases

- La lista puede estar vacía y debe distinguirse de un error de consulta.
- Un título formado solo por espacios se considera vacío.
- El contenido o título de una tarea puede contener caracteres acentuados y signos de
  puntuación, que deben conservarse al guardarla y volver a mostrarla.
- Una tarea solicitada puede no existir o haber sido eliminada desde otra sesión; se informa
  de que ya no está disponible.
- Las acciones de escritura requieren que el servidor esté disponible; los fallos no deben
  representarse como cambios confirmados.
- La eliminación es definitiva dentro del flujo MVP y requiere confirmación explícita.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: La persona usuaria MUST poder consultar si el servidor está disponible y ver
  el resultado de la consulta.
- **FR-002**: La persona usuaria MUST poder obtener y consultar la lista de tiddlers que
  representan tareas, sin incluir tiddlers de otros tipos.
- **FR-003**: La persona usuaria MUST poder abrir una tarea individual y consultar su
  título, descripción y campos de tipo, estado y proyecto.
- **FR-004**: La persona usuaria MUST poder crear una tarea proporcionando un título
  obligatorio y una descripción opcional.
- **FR-005**: Cada tarea MUST tener `tipo: tarea`, `estado: pendiente` y
  `proyecto: MiProyecto` al crearse.
- **FR-006**: La persona usuaria MUST poder cambiar el estado de una tarea entre
  `pendiente` y `completada`, y ver el resultado guardado.
- **FR-007**: La persona usuaria MUST poder eliminar una tarea después de confirmar la
  acción.
- **FR-008**: El sistema MUST informar de resultados vacíos, recursos inexistentes y fallos
  de consulta o escritura con mensajes en castellano, sin declarar exitosas acciones no
  confirmadas.
- **FR-009**: Las tareas MUST conservar sus campos y contenido al guardarse y consultarse,
  incluidos caracteres propios del castellano.
- **FR-010**: Todo tiddler añadido o modificado para la MVP MUST ubicarse en
  `MyWiki/tiddlers`, estar escrito en WikiText y presentar sus textos explicativos en
  castellano.
- **FR-011**: La MVP MUST utilizar las operaciones disponibles denominadas Get Server
  Status, Get All Tiddlers, Get Tiddler, Put Tiddler y Delete Tiddler para sus flujos
  correspondientes.
- **FR-012**: La interfaz de la MVP MUST permitir completar las seis acciones solicitadas:
  consultar estado, listar, ver, crear, cambiar estado y eliminar tareas.

### Key Entities *(include if feature involves data)*

- **Tarea**: Unidad de trabajo identificada por un título único dentro del wiki; incluye
  descripción opcional, tipo `tarea`, estado `pendiente` o `completada` y proyecto
  `MiProyecto`.
- **Servidor**: Servicio que expone el wiki y permite consultar disponibilidad y leer,
  guardar o eliminar tiddlers.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: En una sesión con servidor disponible, la persona puede ejecutar y observar
  las seis acciones principales (consultar estado, listar, ver, crear, cambiar estado y
  eliminar) sin salir de la experiencia de gestión de tareas.
- **SC-002**: En una comprobación de aceptación, el 100 % de las tareas creadas incluyen
  tipo `tarea`, estado inicial `pendiente` y proyecto `MiProyecto`.
- **SC-003**: Tras crear, actualizar o eliminar una tarea, la siguiente consulta refleja el
  estado persistido, sin depender de una recarga manual de los datos locales.
- **SC-004**: Ante servidor inaccesible, título vacío o tarea inexistente, la persona recibe
  un mensaje comprensible y no observa una acción fallida como exitosa.
- **SC-005**: Todos los tiddlers de la MVP se cargan desde `MyWiki/tiddlers` y su contenido
  de autoría usa exclusivamente WikiText, sin bloques añadidos de JavaScript, CSS ni HTML.

## Assumptions

- La instancia MyWiki ya ofrece la WebServer API y sus cinco operaciones requeridas; la
  MVP las invoca mediante una macro o widget ya existente y no añade ni configura endpoints
  o plugins.
- El servidor requiere que su configuración y permisos existentes permitan guardar y
  eliminar tiddlers. La gestión de usuarios y permisos queda fuera del alcance.
- Cada tarea se identifica por un título único; si el título ya existe, se informa del
  conflicto y no se sobrescribe silenciosamente una tarea existente.
- Los estados admitidos por la MVP son `pendiente` y `completada`.
- El proyecto predeterminado para las tareas nuevas es `MiProyecto`; seleccionar otros
  proyectos no forma parte de esta MVP.
- La eliminación se confirma antes de ejecutarse y no cuenta con recuperación desde la
  interfaz de esta MVP.
- Las cinco operaciones WebServer API nombradas son un requisito de integración dado por
  el usuario; los nombres de operaciones se conservan literalmente aunque la interfaz y
  los textos visibles estén en castellano.
