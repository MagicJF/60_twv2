# Investigación: Gestión de tareas en MyWiki

## Decisiones

### Peticiones HTTP desde WikiText

**Decisión**: Usar el mensaje `tm-http-request` del núcleo de TiddlyWiki, lanzado desde
acciones WikiText mediante `$action-sendmessage`. La respuesta ofrece estado HTTP, error,
datos y cabeceras al procedimiento de finalización.

**Motivo**: Es la capacidad nativa existente que permite consumir servicios HTTP sin
escribir JavaScript en los tiddlers. Se incorporó en TiddlyWiki 5.3.0; la instancia objetivo
debe cumplir al menos esa versión.

**Alternativas consideradas**: Añadir un plugin o crear un puente personalizado en
JavaScript. Se descartan porque exceden las restricciones de la constitución.

### Rutas y formas de respuesta de WebServer API

**Decisión**: Utilizar los endpoints de la API del wiki por el mismo origen:

| Operación | Método y ruta | Contrato relevante |
|---|---|---|
| Get Server Status | `GET /status` | JSON con `anonymous`, `read_only`, versión y datos de sesión |
| Get All Tiddlers | `GET /recipes/default/tiddlers.json` | Array JSON; por defecto excluye `text` y tiddlers de sistema |
| Get Tiddler | `GET /recipes/default/tiddlers/{title}` | Campos del tiddler en JSON; 404 si no existe |
| Put Tiddler | `PUT /recipes/default/tiddlers/{title}` | Recibe JSON TiddlyWeb y requiere encabezado CSRF; responde 204 |
| Delete Tiddler | `DELETE /bags/default/tiddlers/{title}` | Requiere encabezado CSRF; responde 204 |

**Motivo**: Son las rutas documentadas para TiddlyWiki WebServer API. La lista compacta
sirve para descubrir metadatos; se usa Get Tiddler al abrir un detalle para recuperar el
texto completo.

**Alternativas consideradas**: Solicitar un filtro externo de tareas en Get All Tiddlers.
No se presupone configuración adicional: el servidor limita filtros por seguridad, por lo
que el cliente filtrará los metadatos recibidos por `tipo: tarea`.

### Cuerpo de guardado y seguridad de petición

**Decisión**: Enviar cuerpos JSON con campos del tiddler, escapando valores de texto con
los operadores JSON de TiddlyWiki o los generadores de JSON del núcleo. WikiText permite
producir el formato JSON TiddlyWeb sin añadir código imperativo. Incluir
`x-requested-with: TiddlyWiki` en PUT y DELETE. No almacenar credenciales en tiddlers; se
utilizará la sesión/autenticación ya configurada por el servidor.

**Motivo**: La API espera formato JSON TiddlyWeb (todos los campos serializados como
cadenas), no contenido `.tid` crudo. El encabezado CSRF es requisito documentado, salvo que
la instancia lo configure de otro modo.

**Alternativas consideradas**: Desactivar la comprobación CSRF o guardar un secreto en la
interfaz. Se descartan porque debilitan la protección o exponen credenciales.

## Fuentes primarias

- [Mensaje tm-http-request](https://tiddlywiki.com/static/WidgetMessage%253A%2520tm-http-request.html)
- [Ejemplos de tm-http-request en WikiText](https://tiddlywiki.com/static/WidgetMessage%253A%2520tm-http-request%2520Examples.html)
- [Get Server Status](https://tiddlywiki.com/static/WebServer%2520API%253A%2520Get%2520Server%2520Status.html)
- [Get All Tiddlers](https://tiddlywiki.com/static/WebServer%2520API%253A%2520Get%2520All%2520Tiddlers.html)
- [Get Tiddler](https://tiddlywiki.com/static/WebServer%2520API%253A%2520Get%2520Tiddler.html)
- [Put Tiddler](https://tiddlywiki.com/static/WebServer%2520API%253A%2520Put%2520Tiddler.html)
- [Delete Tiddler](https://tiddlywiki.com/static/WebServer%2520API%253A%2520Delete%2520Tiddler.html)
- [Formato JSON TiddlyWeb](https://tiddlywiki.com/static/TiddlyWeb%2520JSON%2520tiddler%2520format.html)
- [Construir tiddlers JSON](https://tiddlywiki.com/static/Constructing%2520JSON%2520tiddlers.html)
- [Operador jsonstringify](https://tiddlywiki.com/static/jsonstringify%2520Operator.html)

## Comprobaciones pendientes para ejecutar

- Confirmar en el servidor desplegado una versión TiddlyWiki igual o posterior a 5.3.0.
- Confirmar que el navegador carga MyWiki y resuelve `/status` y `/recipes/default/...`
  desde el mismo origen, incluida cualquier ruta base configurada.
- Confirmar permisos de lectura y escritura para la identidad activa; una cuenta de solo
  lectura debe poder consultar pero no guardar ni borrar.
