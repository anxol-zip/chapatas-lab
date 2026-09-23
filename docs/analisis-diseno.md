# Análisis y diseño del sistema

Documento integrador: describe **el sistema**, no el orden en que lo fuimos decidiendo. Las secciones se cruzan entre sí a propósito — eso es lo que hace posible la [matriz de trazabilidad](#4-matriz-de-trazabilidad).

Documentos relacionados: [Backlog](backlog.md) · [Modelo de datos](modelo-datos.md) · [Modelo de documentos](modelo-documentos.md) · [Decisión de motor](decision-motor.md)

---

## 1. El sistema en una pagina

**El problema.** El responsable del laboratorio presta material a alumnos y profesores y lo anota en una libreta. No sabe con certeza qué está prestado, a quién, ni desde cuándo. Cada semestre se pierde material.

**Lo que el cliente pidió y lo que necesita.** Pidió "una base de datos de equipo". Lo que necesita es **que el equipo regrese**. Por eso el sistema no es un catálogo: es un registro de quién tiene qué y desde cuándo, con una devolución que cierra el ciclo.

**Quién lo usa.**

| Rol | Qué hace |
|---|---|
| **Usuario** (alumno o profesor) | Consulta el catálogo, selecciona el material que se lleva, consulta y registra su devolución. |
| **Administrador** (responsable del laboratorio) | Ve el inventario completo, registra y confirma préstamos y devoluciones, y consulta el historial de todos. |

**El flujo principal.**

```
El usuario consulta el catálogo  →  selecciona material disponible  →  confirma
        ↓
El sistema crea el préstamo (usuario + material + fecha) y baja la disponibilidad
        ↓
El usuario se lleva el material
        ↓
El responsable registra la devolución  →  el préstamo se cierra y la disponibilidad se recupera
        ↓
Todo queda en el historial, buscable por persona o por material
```

La regla que sostiene todo el flujo —**no prestar más de lo que hay**— es la que ningún motor garantiza solo; ver [la regla no garantizada](decision-motor.md#la-regla-no-garantizada).

---

## 2. Que hace

Seis historias funcionales, todas **Must**, definen el alcance de esta versión: consultar el inventario ([HU1](backlog.md#hu1-ver-el-inventario)), seleccionar material ([HU2](backlog.md#hu2-seleccionar-el-material-del-prestamo)), registrar el préstamo con usuario y fecha ([HU3](backlog.md#hu3-registrar-el-prestamo-con-usuario-y-fecha)), descontar la disponibilidad ([HU4](backlog.md#hu4-descontar-la-cantidad-disponible)), registrar la devolución ([HU5](backlog.md#hu5-registrar-la-devolucion)) y consultar el historial ([HU6](backlog.md#hu6-consultar-el-historial-de-prestamos)). Cada una con sus criterios Gherkin en el [backlog](backlog.md#historias-de-usuario).

Cómo se comporta lo definen seis [RNF](backlog.md#requerimientos-no-funcionales): [RNF1](backlog.md#rnf1-seguridad-y-aislamiento-de-datos) (seguridad y aislamiento, Must), [RNF2](backlog.md#rnf2-accesibilidad-wcag-21-aa) (accesibilidad), [RNF3](backlog.md#rnf3-rendimiento-bajo-carga) (rendimiento), [RNF4](backlog.md#rnf4-respaldo-y-recuperacion) (respaldo y recuperación, Must), [RNF5](backlog.md#rnf5-disponibilidad-en-horario-de-laboratorio) (disponibilidad) y [RNF6](backlog.md#rnf6-facilidad-de-uso) (facilidad de uso). Los cuatro primeros tienen métrica y forma de verificación; los dos últimos **no**, y eso los convierte en deseos — está reportado como hueco en la [sección 5](#5-huecos-detectados-y-que-hicimos).

Lo que **no** hace: cobrar el material no devuelto. Quedó [fuera de alcance](backlog.md#fuera-de-alcance-wont-have) de forma explícita y justificada.

---

## 3. Que guarda

El sistema guarda cinco entidades: [Usuario](modelo-datos.md#usuario), [Administrador](modelo-datos.md#administrador), [Categoria](modelo-datos.md#categoria), [Material](modelo-datos.md#material) y [Prestamo](modelo-datos.md#prestamo), con sus [relaciones y cardinalidades](modelo-datos.md#relaciones-y-cardinalidades) y siete [reglas de negocio](modelo-datos.md#reglas-de-negocio). Préstamo es una **entidad de relación**: existe porque Usuario, Administrador y Material se relacionan, y tiene datos propios (fechas, estado).

Ese mismo modelo está traducido a una base de documentos en [modelo-documentos.md](modelo-documentos.md), donde la decisión ya no es cómo normalizar sino **qué se lee junto** (se embebe) y **qué cambia por su cuenta** (se referencia). El caso más discutido es la categoría: la dejamos **referenciada** aunque el ejemplo de clase la embebía, porque ya sabemos que los nombres de categoría se renombran.

Qué motor se implementa **todavía no está decidido** — ver [decision-motor.md](decision-motor.md#motor-elegido).

**La regla no garantizada.** Elijamos el motor que elijamos, **[RN7](modelo-datos.md#reglas-de-negocio) — no prestar más material del disponible — no la garantiza el motor por sí solo.** No cabe en un `check` porque cruza dos tablas, y es una condición de carrera: dos personas que piden la última unidad al mismo tiempo leen "1 disponible" las dos. La resuelve la aplicación, con una transacción que lea y descuente en la misma operación — o desaparece por completo si modelamos Material por unidad física, porque entonces el índice único parcial de [RN1](modelo-datos.md#reglas-de-negocio) la hace cumplir sola. El detalle está en [la regla no garantizada](decision-motor.md#la-regla-no-garantizada).

---

## 4. Matriz de trazabilidad

Una fila por criterio de aceptación. Las celdas vacías se dejan en `—` a propósito: cada `—` es un hueco real, no un pendiente de formato.

### Hacia adelante: requerimiento → diseño

| Historia | Criterio | Entidad.campo que lo soporta | Quién lo escribe (HU) | Quién lo lee (HU) | Pantalla |
|---|---|---|---|---|---|
| [HU1](backlog.md#hu1-ver-el-inventario) | Aparece cada material **con su cantidad disponible actualizada** | `Material.estado`, `Material.activo` · **cantidad disponible: —** (no existe el campo) | — (ninguna HU1–HU6 da de alta material) | HU1, HU2 | P2 · Catálogo |
| [HU1](backlog.md#hu1-ver-el-inventario) | El material se presenta clasificado por categoría | `Material.categoria_id`, `Categoria.nombre` | — | HU1 | P2 · Catálogo |
| [HU2](backlog.md#hu2-seleccionar-el-material-del-prestamo) | Elige **uno o varios** y confirma → el sistema crea el préstamo | `Prestamo.id`, `Prestamo.material_id` | HU2 | HU6 | P3 · Solicitud |
| [HU3](backlog.md#hu3-registrar-el-prestamo-con-usuario-y-fecha) | Queda guardado el **artículo** | `Prestamo.material_id`, `Material.registro` | HU2, HU3 | HU6 | P4 · Registro |
| [HU3](backlog.md#hu3-registrar-el-prestamo-con-usuario-y-fecha) | Queda guardado el **nombre del usuario** | `Prestamo.usuario_id` → `Usuario.` **—** (no hay campo `nombre`) | HU3 | HU6 | P4 · Registro |
| [HU3](backlog.md#hu3-registrar-el-prestamo-con-usuario-y-fecha) | Queda guardada la **fecha/hora** | `Prestamo.fecha_inicio` | HU3 | HU6 | P4 · Registro |
| [HU4](backlog.md#hu4-descontar-la-cantidad-disponible) | De 5 disponibles se presta una → muestra 4 | **—** (no existe campo de cantidad en [Material](modelo-datos.md#material)) | **—** | HU1 | P2 · Catálogo |
| [HU5](backlog.md#hu5-registrar-la-devolucion) | El préstamo cambia a estado "devuelto" | `Prestamo.activo`, `Prestamo.fecha_devolucion` | HU5 | HU6 | P5 · Devolución |
| [HU5](backlog.md#hu5-registrar-la-devolucion) | La cantidad disponible **sube en uno** | **—** (mismo campo faltante que HU4) | **—** | HU1 | P5 · Devolución |
| [HU6](backlog.md#hu6-consultar-el-historial-de-prestamos) | Se busca **por nombre** de persona | **—** (no hay `Usuario.nombre`) | — | HU6 | P6 · Historial |
| [HU6](backlog.md#hu6-consultar-el-historial-de-prestamos) | Se busca **por artículo** | `Material.registro` | — | HU6 | P6 · Historial |
| [HU6](backlog.md#hu6-consultar-el-historial-de-prestamos) | Muestra los préstamos con **fecha y estado** | `Prestamo.fecha_inicio`, `Prestamo.fecha_devolucion`, `Prestamo.activo` | HU3, HU5 | HU6 | P6 · Historial |
| [RNF1](backlog.md#rnf1-seguridad-y-aislamiento-de-datos) | Un alumno no puede consultar préstamos de otro → `403` | `Prestamo.usuario_id`, `Usuario.rol` · **credencial: —** (no hay `correo` ni hash de contraseña) | — | Backend (RN6) | P1 · Inicio de sesión |
| [RNF2](backlog.md#rnf2-accesibilidad-wcag-21-aa) | Los tres flujos críticos se completan solo con teclado | No aplica (propiedad de la interfaz, no del dato) | — | — | P2, P3, P5 |
| [RNF3](backlog.md#rnf3-rendimiento-bajo-carga) | Catálogo p95 ≤ 2 s con ≥ 50 concurrentes | No aplica (propiedad de operación) | — | — | P2 · Catálogo |
| [RNF4](backlog.md#rnf4-respaldo-y-recuperacion) | El historial se restaura completo tras una falla | Todas las entidades | — | — | — (operación) |
| [RNF5](backlog.md#rnf5-disponibilidad-en-horario-de-laboratorio) | Se mantiene accesible "sin caídas frecuentes" | No aplica | — | — | — |
| [RNF6](backlog.md#rnf6-facilidad-de-uso) | Un novato registra un préstamo sin ayuda | No aplica | — | — | P3 · Solicitud |

### Hacia atras: diseño → requerimiento

Un recorrido por cada campo del [modelo de datos](modelo-datos.md), preguntando qué historia lo justifica.

| Entidad.campo | ¿Qué historia lo justifica? | ¿Quién lo escribe? |
|---|---|---|
| `Usuario.id` | [HU3](backlog.md#hu3-registrar-el-prestamo-con-usuario-y-fecha), [RNF1](backlog.md#rnf1-seguridad-y-aislamiento-de-datos) | — (no hay HU de alta de usuario) |
| `Usuario.rol` | [RNF1](backlog.md#rnf1-seguridad-y-aislamiento-de-datos) (distinguir Usuario de Administrador) | — |
| `Administrador.id` | [HU3](backlog.md#hu3-registrar-el-prestamo-con-usuario-y-fecha) (quién registró) | — |
| `Categoria.id` | [HU1](backlog.md#hu1-ver-el-inventario) | — |
| `Categoria.nombre` | [HU1](backlog.md#hu1-ver-el-inventario) (mostrar/filtrar por categoría) | — |
| `Categoria.descripcion` | **— ninguna** | **—** |
| `Material.id` | [HU1](backlog.md#hu1-ver-el-inventario), [HU2](backlog.md#hu2-seleccionar-el-material-del-prestamo) | — |
| `Material.categoria_id` | [HU1](backlog.md#hu1-ver-el-inventario) | — |
| `Material.activo` | [HU1](backlog.md#hu1-ver-el-inventario) (no listar lo dado de baja) | — |
| `Material.registro` | [HU6](backlog.md#hu6-consultar-el-historial-de-prestamos) (búsqueda por artículo) | — |
| `Material.materia` | **— ninguna** | **—** |
| `Material.estado` | [HU1](backlog.md#hu1-ver-el-inventario) | — (ninguna HU lo pone en `prestado`/`disponible`) |
| `Prestamo.id` | [HU2](backlog.md#hu2-seleccionar-el-material-del-prestamo) | HU2 |
| `Prestamo.usuario_id` | [HU3](backlog.md#hu3-registrar-el-prestamo-con-usuario-y-fecha) | HU3 |
| `Prestamo.material_id` | [HU2](backlog.md#hu2-seleccionar-el-material-del-prestamo), [HU3](backlog.md#hu3-registrar-el-prestamo-con-usuario-y-fecha) | HU2 |
| `Prestamo.administrador_id` | [HU3](backlog.md#hu3-registrar-el-prestamo-con-usuario-y-fecha), parcialmente | HU3 |
| `Prestamo.fecha_inicio` | [HU3](backlog.md#hu3-registrar-el-prestamo-con-usuario-y-fecha), [HU6](backlog.md#hu6-consultar-el-historial-de-prestamos) | HU3 |
| `Prestamo.fecha_devolucion` | [HU5](backlog.md#hu5-registrar-la-devolucion), [HU6](backlog.md#hu6-consultar-el-historial-de-prestamos) | HU5 |
| `Prestamo.activo` | [HU5](backlog.md#hu5-registrar-la-devolucion), [RN1](modelo-datos.md#reglas-de-negocio) | HU5 |

---

## 5. Huecos detectados y que hicimos

| # | Tipo de hueco | Dónde | Evidencia | Qué hicimos / proponemos |
|---|---|---|---|---|
| 1 | **Criterio sin dato** | [HU4](backlog.md#hu4-descontar-la-cantidad-disponible) y [HU1](backlog.md#hu1-ver-el-inventario) ↔ [Material](modelo-datos.md#material) | Las filas de HU4 y de "cantidad disponible sube en uno" (HU5) tienen `—` en la columna de entidad. [Material](modelo-datos.md#material) no tiene ningún campo numérico: solo `estado`, que es texto. | **No inventamos el campo.** Lo dejamos vacío y lo elevamos a [decisión abierta](#7-decisiones-abiertas): o Material se modela **por unidad física** (una fila por aparato, y "cantidad" es un conteo de filas), o hace falta `cantidad_total` / `cantidad_disponible`. La primera opción además convierte [RN7](modelo-datos.md#reglas-de-negocio) en algo que el motor sí garantiza. |
| 2 | **Criterio sin dato** | [HU3](backlog.md#hu3-registrar-el-prestamo-con-usuario-y-fecha) y [HU6](backlog.md#hu6-consultar-el-historial-de-prestamos) ↔ [Usuario](modelo-datos.md#usuario) | HU3 pide guardar "el nombre del usuario" y HU6 pide buscar "por nombre". [Usuario](modelo-datos.md#usuario) solo tiene `id` y `rol` — es lo único que quedó en el pizarrón. | Propusimos agregar `nombre`, `identificador` (matrícula/nómina, `unique`) y `correo`, anotado como `> [!question]` en el modelo. **No lo dimos por hecho**: la diapositiva de clase sí los tenía, pero nuestras entidades no. |
| 3 | **Criterio sin dato** | [RNF1](backlog.md#rnf1-seguridad-y-aislamiento-de-datos) ↔ [Usuario](modelo-datos.md#usuario) | RNF1 exige sesión autenticada y "0 contraseñas en texto plano", pero no hay ningún campo de credencial en el modelo. | Lo reportamos aquí. La autenticación puede delegarse a un proveedor externo (institucional), y entonces el campo que falta es solo el `identificador` que lo liga. Pendiente de decidir. |
| 4 | **Dato sin dueño** | `Material.materia` | En el recorrido hacia atrás, la fila de `materia` dice "ninguna" en las dos columnas. Viene de la foto del pizarrón, sin historia que lo pida. | Lo dejamos en el modelo (es dato del cliente) pero marcado. Si nadie lo lee ni lo escribe, o falta una historia de filtrado por materia (parecida a HU-12 de la Actividad 1), o el campo sobra. |
| 5 | **Dato sin dueño** | `Categoria.descripcion` | Nuestras propias notas lo declaran "propuesta propia, no viene de la observación del profesor". Ninguna historia lo menciona. | Proponemos **quitarlo** de esta versión. Un campo nullable que nadie llena ni lee es ruido en el modelo. |
| 6 | **Todos leen, nadie escribe** | Toda la entidad [Material](modelo-datos.md#material) | `Material.registro`, `activo`, `categoria_id`, `estado` y `Categoria.nombre` tienen lectores (HU1, HU2, HU6) y **ningún escritor** en la columna "quién lo escribe". | **Falta la historia de alta de material.** En la Actividad 1 sí existe (HU-03, "ingresar nuevos materiales al inventario", Must), pero no está en HU1–HU6. Sin ella el catálogo arranca vacío y se queda vacío para siempre. Proponemos incorporar HU-03 al conjunto de historias de esta entrega. |
| 7 | **Todos leen, nadie escribe** | `Material.estado` | HU1 y HU2 lo leen para saber si el material está disponible, pero ninguna historia lo cambia a `prestado` al abrir el préstamo ni a `disponible` al cerrarlo. | Es una consecuencia de duplicar a propósito: el dato redundante necesita a alguien que lo sincronice. Proponemos que sea **efecto de HU2 y HU5**, explícito en sus criterios, y no un paso implícito que se nos olvide. |
| 8 | **RNF sin verificación** | [RNF5](backlog.md#rnf5-disponibilidad-en-horario-de-laboratorio) | El criterio dice "sin caídas frecuentes". No hay porcentaje de disponibilidad, ni horario definido, ni forma de medirlo. | Marcado con `> [!warning] Sin verificación` en el backlog. **No inventamos un 99.5 %**: hay que acordarlo con el equipo y con el responsable del laboratorio. |
| 9 | **RNF sin verificación** | [RNF6](backlog.md#rnf6-facilidad-de-uso) | "Logra hacerlo sin ayuda externa": no dice con cuántas personas se prueba, en cuánto tiempo, ni qué tasa de éxito aprueba. | Igual que el anterior. Tal como está es un deseo. Propuesta a discutir: prueba de usabilidad con N personas que nunca hayan visto el sistema, midiendo tasa de éxito sin ayuda. |
| 10 | **Inconsistencia de diseño** (no es de los cuatro tipos, pero rompe la matriz) | [HU2](backlog.md#hu2-seleccionar-el-material-del-prestamo) ↔ [cardinalidad](modelo-datos.md#relaciones-y-cardinalidades) | HU2 dice "elige **uno o varios**", pero Material—Préstamo está modelado 1:N (un solo `material_id`). Tres fuentes más de nuestras notas apuntan a N:M. | Lo dejamos anotado en los dos modelos. Hay que resolverlo **antes** de que haya código que asuma un material por préstamo. |

---

## 6. Inventario de pantallas

| # | Pantalla | Historias que cubre | Qué muestra o captura |
|---|---|---|---|
| **P1** | Inicio de sesión | [RNF1](backlog.md#rnf1-seguridad-y-aislamiento-de-datos) | **Captura:** credencial del usuario. **Usa:** `Usuario.rol` para decidir a qué vista entra. *(El campo de credencial no existe todavía — hueco 3.)* |
| **P2** | Catálogo / Inventario | [HU1](backlog.md#hu1-ver-el-inventario), [HU4](backlog.md#hu4-descontar-la-cantidad-disponible) | **Muestra:** `Material.registro`, `Material.estado`, `Categoria.nombre`, y la cantidad disponible *(campo faltante — hueco 1)*. Filtra por `Material.activo`. |
| **P3** | Solicitud de préstamo | [HU2](backlog.md#hu2-seleccionar-el-material-del-prestamo), [RNF6](backlog.md#rnf6-facilidad-de-uso) | **Captura:** selección de uno o varios materiales. **Escribe:** `Prestamo.material_id`. |
| **P4** | Registro / confirmación del préstamo | [HU3](backlog.md#hu3-registrar-el-prestamo-con-usuario-y-fecha) | **Escribe:** `Prestamo.usuario_id`, `Prestamo.administrador_id`, `Prestamo.fecha_inicio`, `Prestamo.activo`. **Muestra:** el material y el usuario del préstamo. |
| **P5** | Devolución | [HU5](backlog.md#hu5-registrar-la-devolucion) | **Muestra:** préstamos con `Prestamo.activo = true`. **Escribe:** `Prestamo.fecha_devolucion`, `Prestamo.activo = false`. |
| **P6** | Historial de préstamos | [HU6](backlog.md#hu6-consultar-el-historial-de-prestamos) | **Captura:** término de búsqueda (nombre de persona *(faltante — hueco 2)* o `Material.registro`). **Muestra:** `Prestamo.fecha_inicio`, `Prestamo.fecha_devolucion`, `Prestamo.activo`. |
| **P7** | Alta de material | **Ninguna de HU1–HU6** | Pantalla que el modelo **exige** pero el backlog de esta entrega no pide (hueco 6). **Escribiría:** todos los campos de [Material](modelo-datos.md#material). |

> [!note] P7 es el hueco hecho pantalla
> La incluimos aunque ninguna historia de esta entrega la pida, precisamente para que se vea que falta. Sin ella, P2 nunca tiene nada que mostrar.

---

## 7. Decisiones abiertas

1. **¿Material se modela por unidad física o con un contador de cantidad?** Es la decisión que desbloquea [HU1](backlog.md#hu1-ver-el-inventario), [HU4](backlog.md#hu4-descontar-la-cantidad-disponible) y [HU5](backlog.md#hu5-registrar-la-devolucion), y la que define si [RN7](modelo-datos.md#reglas-de-negocio) la garantiza el motor o la aplicación.
2. **¿Material—Préstamo es 1:N o N:M?** Nuestro diagrama dice 1:N; [HU2](backlog.md#hu2-seleccionar-el-material-del-prestamo) y tres fuentes de clase dicen N:M.
3. **¿Administrador es una entidad propia o es [Usuario](modelo-datos.md#usuario) con otro `rol`?** Hoy es un documento con un solo campo.
4. **¿Qué motor se implementa?** Sin decidir; ver [decision-motor.md](decision-motor.md#motor-elegido).
5. **¿Agregamos `nombre`, `identificador` y `correo` a Usuario?** Los piden HU3, HU6 y RNF1, pero no salieron del pizarrón.
6. **¿Incorporamos HU-03 (alta de material) a esta entrega?** Sin ella el inventario no se llena nunca.
7. **¿Se queda `Material.materia`? ¿Se queda `Categoria.descripcion`?** Hoy ninguna historia los usa.
8. **Umbrales de [RNF5](backlog.md#rnf5-disponibilidad-en-horario-de-laboratorio) y [RNF6](backlog.md#rnf6-facilidad-de-uso).** Sin métrica no son verificables.
9. **¿La autenticación es propia o institucional?** Cambia qué campos necesita Usuario.

---

## 8. Declaracion de uso de IA generativa

### Herramienta utilizada

**Claude Opus 5** (Anthropic), ejecutado mediante **Claude Code** en el entorno local de un integrante del equipo, con acceso de **solo lectura** a las notas de clase de la materia guardadas en su vault personal de Obsidian.

### Contexto proporcionado

El modelo trabajó exclusivamente sobre material generado por el equipo y por la clase, sin acceso a fuentes externas ni búsquedas en internet:

- **Historia de usuario y criterios** (Sesión 5) — caso de estudio, HU1–HU6 y sus criterios Gherkin.
- **Actividad 1 — Backlog priorizado (MoSCoW y RNF)** — historias depuradas con INVEST, priorización y los cuatro RNF con métrica.
- **Modelado de entidades y normalización** (Sesión 5 y revisiones) — modelo E-R, cardinalidades y la revisión que sacó Categoría a tabla propia.
- **Bases de datos no relacionales** (Sesión 6) — traducción a colecciones y documentos.
- **Del ejemplo al documento** (Sesión 7) — estructura esperada de `docs/` y la regla de enlaces en Markdown estándar.

### Alcance del uso

**Lo que hizo la IA:**

- Redactar los cinco documentos de `docs/` a partir de las notas, enlazándolos entre sí en Markdown estándar en vez de copiar la información repetida.
- **Cruzar** backlog, modelo de datos y modelo de documentos para construir la [matriz de trazabilidad](#4-matriz-de-trazabilidad) en las dos direcciones.
- Detectar y reportar los [huecos](#5-huecos-detectados-y-que-hicimos) que resultaron de ese cruce, con la evidencia de qué fila o campo los sustenta.
- Deducir el [inventario de pantallas](#6-inventario-de-pantallas) a partir de las historias.
- Marcar con callouts cada punto donde las notas no alcanzaban, en lugar de rellenarlo.

**Lo que NO hizo la IA:**

- La elicitación de requerimientos ni la entrevista al cliente.
- La redacción original de las historias de usuario ni su priorización MoSCoW.
- El modelo Entidad-Relación ni la decisión de sacar Categoría a su propia tabla.
- Elegir el motor de base de datos, que sigue **sin decidir** a propósito.
- Resolver ninguna de las [decisiones abiertas](#7-decisiones-abiertas).

### Validación humana

> [!question] Completar antes de entregar
> _(Axel: llena esta parte con el equipo.)_
>
> - Integrantes que revisaron el documento y fecha de la revisión:
> - Huecos de la sección 5 que el equipo confirmó, y cuáles descartó:
> - Decisiones abiertas de la sección 7 que ya se resolvieron en junta, y cómo:
> - Cambios que el equipo hizo al documento después de la generación:

### Postura del equipo

La IA se usó como **herramienta de estructuración y auditoría cruzada**, no como generadora de requerimientos ni de decisiones de diseño. La materia prima —el problema del cliente, las historias, la prioridad y el modelo de datos— es trabajo del equipo. El valor que aportó el modelo fue recorrer sistemáticamente el cruce entre backlog y modelo hasta encontrar dónde no coinciden. **El equipo asume la responsabilidad del contenido entregado.**
