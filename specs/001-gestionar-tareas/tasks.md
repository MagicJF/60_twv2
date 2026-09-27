# Tareas: Gestión de tareas en MyWiki

**Documentos de entrada**: Diseño en `specs/001-gestionar-tareas/`

**Prerequisitos**: `spec.md` y `plan.md`; también `research.md`, `data-model.md`, `contracts/` y `quickstart.md`.

**Organización**: Tareas agrupadas por historia para construir y aceptar la funcionalidad por incrementos.

## Formato

Cada tarea usa `- [ ] Tnnn [P] [USn] descripción con ruta`. `[P]` aparece solo cuando
puede trabajarse en paralelo; las tareas de historias llevan su etiqueta de trazabilidad.

## Fase 1: Preparación

**Propósito**: Confirmar las condiciones existentes de ejecución sin añadir infraestructura.

- [ ] T001 Desde `/home/dev/projects/60_twv2/MyWiki`, iniciar la instancia con `tiddlywiki --listen`, confirmar versión 5.3.0 o posterior y respuesta de `/status` desde el mismo origen; anotar cualquier diferencia observada en `specs/001-gestionar-tareas/quickstart.md`.

## Fase 2: Fundamentos

**Propósito**: Crear el mecanismo compartido de peticiones que necesitan todas las historias.

- [ ] T002 Crear `MyWiki/tiddlers/Tareas - API.tid`, etiquetado `$:/tags/Global`, con procedimientos WikiText reutilizables para peticiones mediante `$action-sendmessage` y `tm-http-request`, incluyendo cabecera anti-CSRF, codificación URI de títulos, estados pendiente/éxito/error, almacenamiento de respuestas y mensajes de error visibles en castellano.

**Punto de control**: Las historias pueden comenzar cuando el tiddler API común esté cargado y disponible.

## Fase 3: Historia 1 — Consultar y explorar tareas (P1, incremento MVP)

**Objetivo**: Consultar el servidor, listar solo tareas y abrir su información completa.

**Prueba independiente**: Con el servidor disponible y al menos dos tiddlers con `tipo: tarea` más uno de otro tipo, la persona ve el estado del servidor, una lista que excluye el tiddler ajeno y puede abrir una tarea para consultar título, descripción, estado y proyecto. Si la lista está vacía, ve ese estado; si falla la petición, ve un error en castellano.

- [ ] T003 [P] [US1] Crear `MyWiki/tiddlers/Tareas - Inicio.tid` para invocar Get Server Status en `/status`, mostrar disponibilidad o error en castellano y enlazar a la lista de tareas.
- [ ] T004 [P] [US1] Crear `MyWiki/tiddlers/Tareas - Lista.tid` para invocar Get All Tiddlers en `/recipes/default/tiddlers.json`, filtrar por `tipo` igual a `tarea`, mostrar título/estado/proyecto y distinguir lista vacía de error.
- [ ] T005 [P] [US1] Crear `MyWiki/tiddlers/Tareas - Detalle.tid` como vista parametrizada que invoque Get Tiddler en `/recipes/default/tiddlers/{title}`, codifique el título y muestre título/texto/tipo/estado/proyecto o el mensaje de tarea inexistente.
- [ ] T006 [US1] Crear `MyWiki/tiddlers/$__DefaultTiddlers.tid` para abrir `Tareas - Inicio` al iniciar el wiki y completar el recorrido de entrada.

**Punto de control**: La consulta y exploración funcionan sin depender de las historias de escritura.

## Fase 4: Historia 2 — Crear una tarea (P2)

**Objetivo**: Registrar y persistir una tarea con valores predeterminados y validación.

**Prueba independiente**: Crear una tarea con título y descripción que contengan acentos; verla en la lista con los campos iniciales requeridos y reabrirla para verificar la persistencia. Un título vacío o duplicado no crea ni sobrescribe una tarea.

- [ ] T007 [P] [US2] Crear `MyWiki/tiddlers/Tareas - Nueva.tid` con formulario WikiText para título obligatorio (no vacío tras recortar espacios) y descripción opcional; rechazar títulos duplicados y usar Put Tiddler con `tipo: tarea`, `estado: pendiente`, `proyecto: MiProyecto` y cuerpo JSON TiddlyWeb correctamente escapado.
- [ ] T008 [US2] Añadir enlaces en `MyWiki/tiddlers/Tareas - Inicio.tid` y `MyWiki/tiddlers/Tareas - Lista.tid` para abrir `Tareas - Nueva`, mostrar errores de guardado y volver a consultar la lista solo después de una respuesta satisfactoria.

**Punto de control**: Crear una tarea no altera tiddlers existentes y el dato guardado aparece en la siguiente consulta.

## Fase 5: Historia 3 — Cambiar estado y eliminar (P3)

**Objetivo**: Actualizar o eliminar una tarea y reflejar el resultado confirmado por el servidor.

**Prueba independiente**: En una tarea existente, cambiar `pendiente` a `completada` y consultar de nuevo para confirmar persistencia; cancelar una eliminación conserva la tarea y confirmarla la elimina. Los errores de permisos o red no se muestran como éxito.

- [ ] T009 [P] [US3] Crear `MyWiki/tiddlers/Tareas - Cambiar estado.tid` con un control WikiText que use Put Tiddler para alternar únicamente `estado` entre `pendiente` y `completada`, conservando `title`, `text`, `tipo` y `proyecto`.
- [ ] T010 [P] [US3] Crear `MyWiki/tiddlers/Tareas - Eliminar.tid` con confirmación explícita en WikiText y Delete Tiddler en `/bags/default/tiddlers/{title}`; codificar el título y comunicar éxito solo tras respuesta satisfactoria.
- [ ] T011 [US3] Integrar `MyWiki/tiddlers/Tareas - Cambiar estado.tid` y `MyWiki/tiddlers/Tareas - Eliminar.tid` en `MyWiki/tiddlers/Tareas - Detalle.tid`; tras cada operación confirmada, volver a consultar o cerrar el detalle y actualizar la lista.

**Punto de control**: El ciclo de consulta, creación, actualización y eliminación puede completarse desde la interfaz.

## Fase 6: Revisión y validación final

**Propósito**: Comprobar las restricciones de la constitución y el recorrido completo.

- [ ] T012 Revisar `MyWiki/tiddlers/Tareas - API.tid`, `MyWiki/tiddlers/Tareas - Inicio.tid`, `MyWiki/tiddlers/Tareas - Lista.tid`, `MyWiki/tiddlers/Tareas - Detalle.tid`, `MyWiki/tiddlers/Tareas - Nueva.tid`, `MyWiki/tiddlers/Tareas - Cambiar estado.tid`, `MyWiki/tiddlers/Tareas - Eliminar.tid` y `MyWiki/tiddlers/$__DefaultTiddlers.tid`: confirmar cuerpos en WikiText y castellano, y ausencia de JavaScript, CSS y HTML añadidos.
- [ ] T013 Ejecutar en MyWiki real los escenarios de `specs/001-gestionar-tareas/quickstart.md` y corregir únicamente los tiddlers afectados en `MyWiki/tiddlers/` hasta completar estado, lista, detalle, creación, cambio de estado, eliminación y errores.

## Dependencias y orden de ejecución

```text
T001 → T002
T002 → T003, T004, T005, T007, T009 y T010 (trabajo independiente)
T003 → T006
T003 + T004 + T007 → T008
T005 + T009 + T010 → T011
T006 + T008 + T011 → T012 → T013
```

- **Preparación**: T001 no depende de otras tareas y verifica los requisitos del entorno.
- **Fundamentos**: T002 depende de T001 y bloquea las historias porque aporta el transporte compartido.
- **Historia 1**: T003, T004 y T005 pueden realizarse en paralelo tras T002; T006 depende de T003 para fijar la vista inicial.
- **Historia 2**: T007 puede empezar tras T002; T008 integra la creación con las vistas de inicio/lista de US1.
- **Historia 3**: T009 y T010 pueden realizarse en paralelo tras T002; T011 depende de esas acciones y de la vista de detalle T005.
- **Revisión final**: T012 y T013 dependen de que estén completas las historias que se desean entregar.

### Oportunidades de paralelización

- Tras T002: T003, T004 y T005 trabajan en archivos distintos.
- Tras T002: T009 y T010 trabajan en archivos distintos.
- T007 puede desarrollarse a la vez que US1 porque su formulario usa el transporte compartido; T008 espera a que existan Inicio y Lista.

## Estrategia de implementación

1. Completar T001 y T002 para validar compatibilidad y preparar las peticiones WikiText.
2. Completar US1 como primer incremento: disponibilidad del servidor, lista y detalle.
3. Añadir US2 y después US3, validando cada historia con su prueba independiente.
4. Completar T012 y recorrer todos los escenarios de quickstart con T013.

US1 constituye el primer incremento demostrable; la MVP solicitada queda completa al terminar US1, US2 y US3.

## Resumen de tareas

| Grupo | Tareas |
|---|---:|
| Preparación y fundamentos | 2 |
| US1 — Consultar y explorar (P1) | 4 |
| US2 — Crear (P2) | 2 |
| US3 — Cambiar estado y eliminar (P3) | 3 |
| Revisión final | 2 |
| **Total** | **13** |

No se incluyen tareas para pruebas automatizadas: la validación prevista es manual en la instancia TiddlyWiki real.
