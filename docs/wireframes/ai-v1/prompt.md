# Prompt usado para el wireframe AI v1

Registro del prompt con el que se generó el wireframe AI v1, para declarar el uso de IA como pide la Clase 8: *primero el papel, luego la IA, revisar y declarar*. El papel fue el boceto a mano [Wireframe LoFi 1](../handmade/Wireframe%20LoFi%201.md).

## Herramienta

**Claude Opus 5.5** (Anthropic), ejecutado desde **Claude Code** en el entorno local de un integrante del equipo, con el comando `/design`, que crea un lienzo de diseño (artifact de tipo *Design*) en claude.ai.

Fecha: 2026-09-28.

## Prompt

Primero se invocó `/design` sin descripción, y el modelo preguntó qué se quería diseñar. La respuesta, tal cual se escribió, fue:

> Claude, en el vault de docs en el repo de chapatas-lab, vamos a hacer un wireframe lo-fi para la interfaz de prestamos. En el vault ya tenemos los documentos donde se especifican las características; además, en mi vault personal, en la materia de Diseño de Software, también hay más especificaciones, RNF, y demás que pueden apoyar con la construcción. Lo más relevante, es que dentro de mi vault personal, ya elaboramos un wireframe con Excalidraw, algo que puede apoyar con nuestra visión. Todo esto es parte de un ejercicio de la clase, el objetivo es hacer el wireframe de las pantallas (incluyendo las flechas de flujo). Las secciones de la interfaz son "Inicio"[resumen de prestamos activos y fecha de entrega proxima] "Catálogo"[vista cuadrículada de objetos y barra de búsqueda] e "Historial"[pedidos anteriores, detalles, fecha de inicio, de entrega].

## Contexto que leyó el modelo

Solo lectura, sin búsquedas en internet:

- **Este repositorio:** [analisis-diseno.md](../../analisis-diseno.md) (inventario de pantallas P1–P7, huecos, decisiones abiertas), [backlog.md](../../backlog.md) (HU1–HU6 y RNF), [modelo-datos.md](../../modelo-datos.md) (entidades y reglas RN1–RN7) y el boceto [Wireframe LoFi 1](../handmade/Wireframe%20LoFi%201.md), del que tomó la estructura: barra superior con pestañas, catálogo en cuadrícula e historial en lista.
- **Vault de Obsidian del integrante:** las notas de *Diseño de Software*, sobre todo *Diseño de pantallas* (Clase 8), de donde salieron los requisitos de la actividad: número de historia en cada pantalla, error de la regla, estado vacío, las tres marcas WCAG (3.3.2, 2.5.8, 1.4.3) y el diagrama de clase «El flujo de pantallas», con el camino de error «el equipo ya está prestado».

## Resultado

- Lienzo en línea: <https://claude.ai/artifact/65pDvBnXTT32g3arPTEB5e>. El mismo lienzo se actualizó después a [AI v2](../ai-v2/prompt.md); la v1 se conserva aquí y en el historial de versiones del lienzo.
- 7 pantallas y el flujo, en PNG en esta carpeta y como HTML navegable en [html/](html/Login.html).

| Pantalla | Imagen | Historias |
|---|---|---|
| Flujo de pantallas | [Main.png](Main.png) | — |
| P1 · Inicio de sesión | [Login.png](Login.png) | RNF1 |
| Inicio | [Inicio.png](Inicio.png) | HU3, HU5 |
| Inicio — estado vacío | [InicioVacio.png](InicioVacio.png) | HU3, HU5 |
| P2 · Catálogo | [Catalogo.png](Catalogo.png) | HU1, HU2, HU4 |
| P3/P4 · Confirmar préstamo | [Confirmar.png](Confirmar.png) | HU2, HU3 |
| P3/P4 · Error RN1 | [ErrorPrestado.png](ErrorPrestado.png) | RN1, HU4 |
| P6 · Historial | [Historial.png](Historial.png) | HU6 |

## Lo que decidió el modelo por su cuenta

Hay que revisarlo con el equipo:

- Que el error de la regla sea **RN1** (un material no puede estar en dos préstamos abiertos): otra persona lo pidió mientras el alumno confirmaba. Es el camino de error del diagrama de clase.
- Que el estado vacío vaya en **Inicio** («no tienes préstamos activos»).
- **Un material por préstamo** (1:N, como el modelo de datos), aunque HU2 dice «uno o varios» (decisión abierta 2).
- Mostrar una **fecha de entrega** que el modelo no tiene: `fecha_devolucion` es cuándo se devolvió, no la fecha límite.
- Que **Inicio** sea una pantalla propia, aunque no está en el inventario P1–P7.
- Que el Historial del alumno busque por material o número de registro, no por persona, porque RN6 solo le deja ver sus propios préstamos.
- Diseño de escritorio (1280 px) y datos como marcadores (`[Nombre del material]`, `[dd/mm/aaaa]`) en lugar de ejemplos inventados.