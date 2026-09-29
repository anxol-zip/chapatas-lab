# Prompt usado para el wireframe AI v2

Registro del prompt con el que se generó el wireframe AI v2, la versión vigente, para declarar el uso de IA como pide la Clase 8: *primero el papel, luego la IA, revisar y declarar*. La v2 parte de [AI v1](../ai-v1/prompt.md), que a su vez parte del boceto a mano [Wireframe LoFi 1](../handmade/Wireframe%20LoFi%201.md).

## Herramienta

**Claude Opus 5.5** (Anthropic), ejecutado desde **Claude Code** en el entorno local de un integrante del equipo, en la misma sesión que la v1. Se actualizó el mismo lienzo de diseño (artifact de tipo *Design*) en claude.ai.

Fecha: 2026-09-28.

## Prompt

Se escribió como respuesta al resultado de la v1, tal cual:

> sí, guárdalo en docs/wireframes en una rama llamada "docs: wireframe AI v1". Además, quiero que avances con el resto de historias de usuario que determinamos, ya sabes, usarlas junto a los RF y RNF para seguir avanzando con todo lo que tenemos. Además, me gustaría añadir algunos elementos de interfaz más llamativos, como una barra que represente la cantidad de stock disponible, y un espacio para que los materiales tengan una descripción breve, y si finalmente haces una tabla donde relaciones las pantallas con las historias lo agradecería mucho (aunque ya se que señalas cada historia en la esquina de cada pantalla, detallazo, dejalo también). Avancemos para que mi profesor vea que realmente hemos puesto atención en toda la clase.

## Contexto que leyó el modelo

Solo lectura, sin búsquedas en internet. Además de todo lo de la [v1](../ai-v1/prompt.md#contexto-que-leyó-el-modelo):

- **Vault de Obsidian del integrante:** *Actividad 1 — Backlog priorizado y RNF*, con las 21 historias depuradas (HU-01…HU-21), su prioridad MoSCoW, los cinco RNF con métrica (RNF-01…05) y las reglas que salieron de la depuración INVEST, como la devolución en dos pasos entre HU-10 y HU-05.

## Resultado

- Lienzo en línea: <https://claude.ai/artifact/65pDvBnXTT32g3arPTEB5e> (versión más reciente).
- 15 pantallas en dos roles (alumno / profesor y responsable del laboratorio), el flujo y la matriz pantalla × historia, en PNG en esta carpeta y como HTML navegable en [html/](html/Login.html).
- Tablas pantalla → historias e historia → pantallas: [MOC Wireframes](../MOC%20Wireframes.md#pantalla--historias).

## Lo que decidió el modelo por su cuenta

Hay que revisarlo con el equipo:

- **Numeración:** usar HU-01…HU-21 y RNF-01…05 de la Actividad 1, no HU1–HU6 de [backlog.md](../../backlog.md), porque es la lista completa.
- **Nombre de la rama:** `docs-wireframe-ai-v1` en lugar de «docs: wireframe AI v1». Git no acepta espacios ni `:` en el nombre, y `docs/…` choca con la rama `docs`.
- **Ciclo de vida del préstamo en 5 estados** (enviada → aprobada → recogida → devolución registrada → cerrada) en lugar del booleano `activo`, para que quepan HU-04 y la regla HU-10 → HU-05.
- **Solicitud con varios materiales** (HU-16), que empuja la decisión abierta 2 hacia N:M.
- **Barra de existencias:** un segmento por unidad hasta 10 y barra continua con más unidades. El modelo todavía no tiene el campo de cantidad (hueco 1).
- **Descripción breve** de hasta 140 caracteres: campo nuevo, `Material.descripcion`, que el modelo no tiene.
- **Pantallas Could incluidas** (R-06 Reportes, aviso de HU-20, aviso de HU-21), marcadas con borde punteado.
- **Elementos que ninguna historia pide** («Rechazar…», «Contactar», «Reportar problema», «Exportar CSV»): se dibujaron y se listaron en «¿Qué sobra?» para que el equipo decida.
- **Error de la regla:** A-06 «Sin existencias» (RN7 / RNF-05), porque con cantidades la regla ya no es RN1 sino no prestar más de lo que hay.
- **Quién fija la fecha límite** quedó marcado como regla por definir.
- **PNG renderizados sin conexión**, con fuentes locales de respaldo en lugar de IBM Plex y Kalam.