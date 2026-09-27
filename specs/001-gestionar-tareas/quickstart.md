# Guía de validación: Gestión de tareas en MyWiki

## Requisitos previos

- Node.js y el comando `tiddlywiki` disponibles.
- Instancia TiddlyWiki 5.3.0 o posterior, con la configuración actual de MyWiki y su
  WebServer API.
- Acceso al wiki con permisos de lectura y escritura para validar todas las acciones.
- Navegador que abra MyWiki y la API desde el mismo origen y sin prefijo de ruta especial.

Comprueba la versión instalada:

```sh
tiddlywiki --version
```

Si no hay ya una instancia en ejecución, inicia el servidor desde el directorio
`/home/dev/projects/60_twv2/MyWiki` con este comando:

```sh
cd /home/dev/projects/60_twv2/MyWiki
tiddlywiki --listen
```

Abre la dirección que indique el servidor. Si usa la configuración predeterminada,
normalmente será `http://127.0.0.1:8080/`; si no, usa la URL que muestre el servidor y
confirma que la API comparte el mismo origen.

## Comprobaciones de aceptación

1. Consulta el endpoint `/status` en la URL indicada por el servidor. Con la dirección
   predeterminada, abre `http://127.0.0.1:8080/status` o ejecuta:

   ```sh
   curl -i http://127.0.0.1:8080/status
   ```

   Debe responder `200` con JSON de estado.
2. Abre la vista principal de gestión. Confirma que indica la disponibilidad del servidor
   y muestra solo tiddlers con `tipo: tarea`; si no hay ninguno, presenta estado vacío.
3. Abre una tarea existente. Confirma que se consultan y muestran título, descripción,
   estado, tipo y proyecto desde los datos del servidor.
4. Crea una tarea con título y descripción que incluyan acentos. Confirma que aparece en la
   lista con `tipo: tarea`, `estado: pendiente` y `proyecto: MiProyecto`, y que su contenido
   se conserva al reabrirla.
5. Intenta crear otra tarea con el mismo título. Confirma que aparece el conflicto y que el
   tiddler existente no se sobrescribe.
6. Cambia una tarea de `pendiente` a `completada`. Confirma el resultado y vuelve a
   consultar desde el servidor para verificar que persiste.
7. Solicita eliminar una tarea, cancela una vez y confirma otra vez. La primera vez debe
   conservarse; tras confirmar, debe desaparecer de la lista y el servidor debe devolver
   éxito.
8. Para validar errores, intenta abrir un título inexistente y realiza una consulta con el
   servidor detenido. Los mensajes deben ser comprensibles y no afirmar éxito.
9. Con una identidad de solo lectura, repite una escritura. La consulta debe seguir
   disponible y el rechazo del servidor debe mostrarse como error.

## Criterios de revisión de archivos

- Los tiddlers fuente de la MVP están únicamente en `MyWiki/tiddlers`.
- El texto del cuerpo de cada tiddler está en castellano y usa WikiText.
- No se añadieron bloques de JavaScript, CSS ni HTML, ni se cambiaron configuración,
  paquetes, plugins o temas.
- Las peticiones PUT y DELETE llevan `x-requested-with: TiddlyWiki` y solo se anuncian
  como exitosas tras una respuesta satisfactoria.

La lista de endpoints y respuestas está en [contracts/webserver-api.md](contracts/webserver-api.md);
los campos y reglas de transición están en [data-model.md](data-model.md).
