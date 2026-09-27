# Modelo de datos: Gestión de tareas

## Entidad: Tarea

Un tiddler no perteneciente al sistema que representa una unidad de trabajo del proyecto.

| Campo | Tipo | Regla |
|---|---|---|
| `title` | Texto | Obligatorio, no vacío tras recortar espacios y único dentro del wiki; identifica el tiddler |
| `text` | WikiText | Descripción opcional; debe conservar caracteres castellanos y puntuación |
| `tipo` | Texto | Valor obligatorio `tarea` |
| `estado` | Texto | `pendiente` o `completada`; valor inicial `pendiente` |
| `proyecto` | Texto | Valor inicial y único admitido en MVP: `MiProyecto` |

La API lista tiddlers no pertenecientes al sistema y devuelve metadatos sin `text` por
defecto. La lista se filtra por `tipo`; el texto completo se solicita al abrir un detalle.
Al guardar, todos los campos viajan como cadenas en un objeto JSON TiddlyWeb.

## Ciclo de vida

```text
Creación → pendiente ⇄ completada → eliminación confirmada
```

- La creación requiere título único; un conflicto no debe sobrescribir datos existentes.
- Cambiar el estado modifica solo `estado` y conserva título, contenido, tipo y proyecto.
- La eliminación no tiene recuperación desde esta MVP.
- Las escrituras se consideran completadas únicamente tras una respuesta de éxito del
  servidor; en error se conserva el estado visible previamente confirmado.

## Entidad: Resultado de petición

Estado transitorio de interfaz, no persistido como tiddler de tarea:

| Atributo | Valores o uso |
|---|---|
| Operación | Consulta de estado, lista, detalle, guardado o eliminación |
| Estado | Pendiente, completada o error |
| Respuesta | Código HTTP, datos o mensaje de error disponible |

Los mensajes presentados a la persona usuaria estarán en castellano. Las respuestas de
error no deben reemplazar una tarea existente ni aparecer como confirmación de escritura.

## Entidad: Servidor

La instancia WebServer de MyWiki que valida identidad y permisos, sirve tiddlers y aplica
las operaciones GET, PUT y DELETE definidas en
[el contrato de API](contracts/webserver-api.md). Una identidad de solo lectura puede
consultar; las operaciones de escritura deben informar del rechazo.
