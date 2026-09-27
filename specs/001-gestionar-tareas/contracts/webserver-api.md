# Contrato: WebServer API de MyWiki

## Alcance

Contrato del cliente WikiText de la MVP con la API del mismo servidor. Se asume que MyWiki
se abre bajo la raíz del origen y sin un prefijo de ruta personalizado. No se guardan
credenciales en los tiddlers.

## Operaciones

| Caso de uso | Operación HTTP | Petición | Respuesta esperada |
|---|---|---|---|
| Consultar estado | `GET /status` | Sin parámetros | `200`, JSON con sesión, permiso de solo lectura y versión |
| Listar tareas | `GET /recipes/default/tiddlers.json` | Sin filtro externo | `200`, array de metadatos no sistémicos; el campo `text` se omite por defecto |
| Ver tarea | `GET /recipes/default/tiddlers/{title}` | Título codificado como componente URI | `200`, campos JSON del tiddler; `404` si no existe |
| Crear o cambiar tarea | `PUT /recipes/default/tiddlers/{title}` | Título codificado, `Content-Type: application/json`, cuerpo JSON TiddlyWeb y `x-requested-with: TiddlyWiki` | `204` cuando se guarda; errores HTTP en otro caso |
| Eliminar tarea | `DELETE /bags/default/tiddlers/{title}` | Título codificado y `x-requested-with: TiddlyWiki` | `204` cuando se elimina; errores HTTP en otro caso |

Los nombres entre corchetes representan valores sustituidos en ejecución; no son parte
literal de la ruta.

## Reglas del cliente

- La lista se filtra localmente por `tipo` igual a `tarea`; la consulta no debe solicitar
  filtros externos que no estén habilitados por el servidor.
- La lista muestra título, estado y proyecto. Para mostrar descripción, se realiza la
  operación Get Tiddler con el título seleccionado.
- Crear envía `title`, `text`, `tipo`, `estado` y `proyecto`; los tres últimos reciben los
  valores `tarea`, `pendiente` y `MiProyecto`.
- Cambiar estado envía por PUT el tiddler actualizado, manteniendo todos los campos
  existentes y el texto.
- PUT y DELETE incluyen el encabezado anti-CSRF documentado por TiddlyWiki, salvo que la
  instancia tenga una política de servidor compatible que lo gestione de otra manera.
- Un cambio se confirma en la interfaz solo tras `204`. Los errores de red, permisos o
  validación producen mensajes en castellano y conservan la última vista confirmada.
- Después de una escritura correcta, la interfaz vuelve a consultar la lista o detalle al
  servidor para mostrar el dato persistido.

## Restricciones conocidas

- `Get All Tiddlers` omite `text` y, por defecto, no devuelve tiddlers del sistema.
- El servidor limita los filtros externos admitidos por seguridad; esta MVP usa la lista
  predeterminada y filtra en el cliente.
- El contenido del campo `text` dentro del objeto JSON es WikiText, pero el cuerpo de red
  debe ser JSON válido con todos los valores representados como cadenas.
- Se requiere TiddlyWiki 5.3.0 o posterior para disponer del mensaje integrado
  `tm-http-request`.

## Referencias oficiales

- [Get Server Status](https://tiddlywiki.com/static/WebServer%2520API%253A%2520Get%2520Server%2520Status.html)
- [Get All Tiddlers](https://tiddlywiki.com/static/WebServer%2520API%253A%2520Get%2520All%2520Tiddlers.html)
- [Get Tiddler](https://tiddlywiki.com/static/WebServer%2520API%253A%2520Get%2520Tiddler.html)
- [Put Tiddler](https://tiddlywiki.com/static/WebServer%2520API%253A%2520Put%2520Tiddler.html)
- [Delete Tiddler](https://tiddlywiki.com/static/WebServer%2520API%253A%2520Delete%2520Tiddler.html)
