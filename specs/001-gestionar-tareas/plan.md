# Plan de implementación: Gestión de tareas en MyWiki

**Rama**: `001-gestionar-tareas` | **Fecha**: 2026-09-27 | **Especificación**: [spec.md](spec.md)

**Entrada**: Especificación de funcionalidad en `specs/001-gestionar-tareas/spec.md`

## Resumen

Crear una interfaz de gestión de tareas compuesta por tiddlers WikiText en
`MyWiki/tiddlers`. Las interacciones HTTP se realizarán con el mensaje integrado
`tm-http-request`, disponible desde WikiText mediante widgets de acción nativos. No se
añadirán plugins ni código JavaScript, CSS o HTML. Los datos se consultarán y persistirán
en el servidor TiddlyWiki usando las cinco operaciones WebServer API requeridas.

## Contexto técnico

**Lenguaje y versión**: WikiText de TiddlyWiki; el mensaje HTTP requerido existe desde
TiddlyWiki 5.3.0. `MyWiki` no fija la versión, por lo que quickstart exige comprobar que el
servidor desplegado sea 5.3.0 o posterior.

**Dependencias principales**: Núcleo de TiddlyWiki, `tm-http-request`, WebServer API y
widgets nativos de WikiText. La configuración actual enumera los plugins `tiddlyweb`,
`filesystem` y `highlight`; no se presupone que haya macros personalizados.

**Almacenamiento**: Tiddlers de tarea persistidos por la WebServer API y alojados por la
edición Node.js en su almacén de archivos. Los artefactos fuente de la funcionalidad serán
archivos `.tid` en `MyWiki/tiddlers`.

**Validación**: Recorrido manual en la instancia real siguiendo
`quickstart.md`; sin pruebas automatizadas para esta implementación exclusivamente
WikiText.

**Plataforma objetivo**: MyWiki servido por TiddlyWiki en Node.js y navegador compatible,
con las peticiones a la API en el mismo origen.

**Tipo de proyecto**: Wiki Node.js cliente-servidor con interfaz compuesta por tiddlers.

**Objetivos de rendimiento**: Sin SLA numérico para la MVP; cada acción muestra el estado
final de su petición y no simula confirmación antes de recibir respuesta.

**Restricciones**: Tiddlers de la funcionalidad exclusivamente en `MyWiki/tiddlers`;
contenido y comentarios en castellano; cuerpo WikiText; sin JavaScript, CSS, HTML, plugins,
dependencias ni cambios de configuración. PUT y DELETE requieren el encabezado
`x-requested-with: TiddlyWiki` salvo configuración CSRF distinta del servidor.

**Escala y alcance**: Una instancia MyWiki y tareas del proyecto `MiProyecto`; sin gestión
de usuarios, múltiples proyectos, sincronización de conflictos ni recuperación de tareas
eliminadas.

## Comprobación de constitución

**Puerta previa a la investigación**: Aprobada. La integración se apoya en el mensaje HTTP
incorporado en el núcleo y en WikiText. El repositorio no contiene una macro propia, pero no
se necesita añadirla.

- La funcionalidad y sus tiddlers permanecen bajo `MyWiki/tiddlers`.
- Los textos de interfaz y comentarios se redactarán en castellano.
- No se añadirán JavaScript, CSS ni HTML.
- No se requieren cambios a `tiddlywiki.info`, paquetes, plugins ni temas.
- La validación será manual en TiddlyWiki real según la constitución.

**Puerta posterior al diseño**: Aprobada con dos condiciones operativas: comprobar que el
servidor corre TiddlyWiki 5.3.0 o posterior y que la interfaz se sirve desde el mismo origen
que la API. La comprobación se incluye en `quickstart.md`.

## Estructura del proyecto

### Documentación de la funcionalidad

```text
specs/001-gestionar-tareas/
├── plan.md
├── research.md
├── data-model.md
├── quickstart.md
├── contracts/
│   └── webserver-api.md
└── tasks.md              # Se genera en la fase $speckit-tasks
```

### Archivos fuente de la funcionalidad

```text
MyWiki/
└── tiddlers/
    ├── tiddlers de navegación y gestión de tareas (.tid, por crear)
    ├── procedimientos reutilizables de interacción con la API (.tid, por crear)
    └── vistas de lista, detalle, formulario y confirmación (.tid, por crear)
```

**Decisión de estructura**: Mantener toda la interfaz y su lógica declarativa en tiddlers
WikiText de `MyWiki/tiddlers`. Los artefactos de diseño de Spec Kit se guardan en el
directorio de la feature; no son archivos de implementación.

## Seguimiento de complejidad

No hay violaciones de la constitución ni proyectos adicionales que justificar.
