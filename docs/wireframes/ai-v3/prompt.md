# Prompt usado para el wireframe LoFi 2

Registro del prompt con el que se generó el [wireframe LoFi 2](README.md), para declarar el uso de IA como pide la [Clase 8](README.md#contra-lo-que-pide-la-actividad-clase-8): *primero el papel, luego la IA, revisar y declarar*.

## Herramienta

**Claude Opus 5.5** (Anthropic), ejecutado desde **Claude Code** en el entorno local de un integrante del equipo, con el comando `/design`, que crea un lienzo de diseño (artifact de tipo *Design*) en claude.ai.

Fecha: 2026-09-28.

## Prompt

Primero se invocó `/design` sin descripción, y el modelo preguntó qué se quería diseñar. La respuesta, tal cual se escribió, fue:

> Quiero generar un wireframe de acuerdo a lo que nos estan pidiendo para el prestamo de laboratorio, tienes los archivos en este repositorio de docs, tambien puedes guiarte de mi otro baul de Obsidian que esta en ONEDRIVE/AREPOLLAS para tener todo el contexto de que debemos de cumplir y que sea el mejor wireframe para este tipo de proyecto.

## Contexto que leyó el modelo

Solo lectura, sin búsquedas en internet:

- **Este repositorio:** [analisis-diseno.md](../../analisis-diseno.md) (matriz de trazabilidad, huecos, inventario de pantallas P1–P7, decisiones abiertas), [backlog.md](../../backlog.md) (HU1–HU6 y RNF1–RNF6), [modelo-datos.md](../../modelo-datos.md) (entidades y reglas RN1–RN7) y el boceto previo [Wireframe LoFi 1](../handmade/Wireframe%20LoFi%201.md).
- **Vault de Obsidian:** las notas de la materia *Diseño de Software*, sobre todo *DISEÑO DE PANTALLAS* (Clase 8). De ahí salieron los requisitos de la actividad: el número de historia en cada pantalla, una pantalla con el error de la regla, una con estado vacío, las tres marcas WCAG (3.3.2, 2.5.8, 1.4.3) y el flujo con el camino de error.

## Resultado

- Lienzo en línea: <https://claude.ai/artifact/188Rn9nRVPHJDvVzwazyXc>
- Fuentes: esta carpeta, [ai-v3/](./)
- Tabla pantalla-historia y flujo: [README.md](README.md)

## Lo que decidió el modelo por su cuenta

Hay que revisarlo con el equipo:

- Que la regla del error sea RN7 («no prestar más de lo que hay»), en el caso de que otra persona confirme la última unidad mientras el alumno revisa su solicitud.
- Que el estado vacío vaya en P5 Devoluciones («sin préstamos activos»).
- Que P3 permita varios materiales por préstamo (N:M), aunque el modelo de datos dice 1:N. Queda marcado como decisión abierta 2.
- Incluir P7 Alta de material aunque ninguna historia la pida. Queda marcada como hueco 6.
- Materiales, registros, categorías y fechas de ejemplo.

## Revisión humana

> [!question] Completar antes de entregar
> - Quién revisó el wireframe y cuándo:
> - Qué se cambió después de generarlo:
> - Qué supuestos de la lista anterior se confirmaron o descartaron:
