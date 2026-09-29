# Wireframes

<<<<<<< HEAD
Inventario de pantallas dibujado: qué ve el usuario, qué puede hacer y a dónde lleva cada acción. Cruza el [backlog](backlog.md) con las pantallas de la [sección 6 del análisis](analisis-diseno.md#6-inventario-de-pantallas).

Documentos relacionados: [Análisis y diseño](analisis-diseno.md) · [Backlog](backlog.md) · [Modelo de datos](modelo-datos.md) · [Prompt usado para generarlo](wireframes/prompt-wireframe.md)

---

## Dónde verlo

| Versión | Qué es | Dónde |
|---|---|---|
| LoFi 1 | Primer boceto en Excalidraw: Login, Pantalla principal, Catálogo, Préstamo, Historial | [wireframes/Wireframe LoFi 1.md](wireframes/Wireframe%20LoFi%201.md) (abrir en Obsidian con el plugin de Excalidraw) |
| LoFi 2 | Nueve pantallas navegables, el flujo y la tabla pantalla-historia | Lienzo en línea: <https://claude.ai/artifact/188Rn9nRVPHJDvVzwazyXc> · fuentes en [wireframes/lofi-2/](wireframes/lofi-2/) |

> [!note] Sobre las fuentes de LoFi 2
> Los archivos `.dc.html` de [wireframes/lofi-2/](wireframes/lofi-2/) son el respaldo del lienzo, no páginas sueltas: dependen del runtime del editor (`support.js`) y no se ven bien abiertos directo en el navegador. Para verlos, usar el enlace del lienzo. [`canvas.json`](wireframes/lofi-2/canvas.json) guarda la posición de cada tablero y las notas.
=======
Wireframes lo-fi del sistema de préstamo de material: qué ve cada rol en cada pantalla, qué puede hacer y a dónde lo lleva cada acción. No definen colores ni estilo visual.

Documentos relacionados: [Backlog](backlog.md) · [Análisis y diseño](analisis-diseno.md) · [Modelo de datos](modelo-datos.md)

| Versión | Qué cubre | Dónde |
|---|---|---|
| Wireframe LoFi 1 | Primer boceto del equipo en Excalidraw: Login, Principal, Catálogo, Préstamo, Historial | [wireframes/Wireframe LoFi 1.md](wireframes/Wireframe%20LoFi%201.md) |
| AI v1 | Rol Usuario, HU1–HU6, 7 pantallas + flujo | [wireframes/ai-v1/](wireframes/ai-v1/README.md) |
| **AI v2 (vigente)** | **Backlog completo (HU-01…HU-21, RNF-01…05), dos roles, 15 pantallas + flujo + matriz** | [wireframes/ai-v2/](wireframes/ai-v2/) |

Cada versión trae un PNG por pantalla y una carpeta `html/` con las pantallas navegables (abrir `html/Login.html` en el navegador). El original editable vive en un canvas de Claude Design; los PNG se renderizaron sin conexión, así que usan fuentes locales de respaldo.

> [!note] Numeración
> Aquí se usan los IDs **HU-01…HU-21** y **RNF-01…05** de la Actividad 1 (backlog priorizado con MoSCoW), porque es la lista completa. [backlog.md](backlog.md) solo tiene HU1–HU6 y seis RNF; la equivalencia está en [Equivalencia con backlog.md](#equivalencia-con-backlogmd).
>>>>>>> da10dd5 (docs: wireframe AI v2 con backlog completo y trazabilidad)

---

## Flujo de pantallas

<<<<<<< HEAD
```mermaid
flowchart LR
    P1[P1 · Inicio de sesión] -->|entrar · usuario| P2[P2 · Catálogo]
    P1 -->|entrar · responsable| P5[P5 · Devoluciones]
    P2 -->|revisar solicitud| P3[P3 · Solicitud]
    P3 -->|confirmar| P4[P4 · Préstamo registrado]
    P3 -->|confirmar · ya no hay unidades| E["P3 · «Ya no está disponible»"]
    E -.->|volver al catálogo| P2
    E -.->|quitar y confirmar el resto| P4
    P5 -->|inventario| P2
    P5 -->|último devuelto| V[P5 · Sin préstamos activos]
    V -->|ver historial| P6[P6 · Historial]
    P6 -.->|menú| P7[P7 · Alta de material]

    style E stroke:#9f1f17,color:#9f1f17
    style P7 stroke:#c2610f,stroke-dasharray: 5 5
```

Cada flecha lleva la acción que la dispara. El camino de error (rojo, punteado) regresa al catálogo. P7 va punteada porque ninguna historia de HU1–HU6 la pide ([hueco 6](analisis-diseno.md#5-huecos-detectados-y-que-hicimos)).

---

## Tabla pantalla-historia

| Pantalla | Historias / RNF | Qué muestra o captura | Marcas WCAG | Hueco o decisión |
|---|---|---|---|---|
| **P1** Inicio de sesión | [RNF1](backlog.md#rnf1-seguridad-y-aislamiento-de-datos) | Matrícula y contraseña; el rol decide a qué vista entra | 3.3.2 · 2.5.8 | Hueco 3 · decisión 9 |
| **P2** Catálogo | [HU1](backlog.md#hu1-ver-el-inventario), [HU2](backlog.md#hu2-seleccionar-el-material-del-prestamo), [HU4](backlog.md#hu4-descontar-la-cantidad-disponible), [RNF3](backlog.md#rnf3-rendimiento-bajo-carga) | Material, registro, categoría y disponibles; agrega a la solicitud | 3.3.2 · 2.5.8 · agotado no solo por color | Hueco 1 · decisión 1 |
| **P3** Solicitud | [HU2](backlog.md#hu2-seleccionar-el-material-del-prestamo), [RNF2](backlog.md#rnf2-accesibilidad-wcag-21-aa), [RNF6](backlog.md#rnf6-facilidad-de-uso) | Materiales elegidos, resumen y confirmación; orden de Tab 1 → 4 | 2.5.8 · 1.4.3 · teclado | Decisión 2 (1:N / N:M) |
| **P3** Error de la regla | [HU2](backlog.md#hu2-seleccionar-el-material-del-prestamo), [HU4](backlog.md#hu4-descontar-la-cantidad-disponible), [RN7](modelo-datos.md#reglas-de-negocio) | «Ya no está disponible» con dos salidas: quitar y confirmar, o volver | 1.4.3 · `role="alert"` | La regla vive en el backend |
| **P4** Préstamo registrado | [HU3](backlog.md#hu3-registrar-el-prestamo-con-usuario-y-fecha), [HU4](backlog.md#hu4-descontar-la-cantidad-disponible) | Quién, qué, cuándo y estado; disponibles antes → después | `role="status"` · 2.5.8 | Hueco 2 · hueco 7 |
| **P5** Devoluciones | [HU5](backlog.md#hu5-registrar-la-devolucion), [RNF2](backlog.md#rnf2-accesibilidad-wcag-21-aa) | Préstamos activos; confirma devolución (devuelto, disponible + 1) | 3.3.2 · 2.5.8 · Esc / Enter | — |
| **P5** Estado vacío | [HU5](backlog.md#hu5-registrar-la-devolucion) | Cero préstamos activos y a dónde ir después | `role="status"` · 2.5.8 | — |
| **P6** Historial | [HU6](backlog.md#hu6-consultar-el-historial-de-prestamos), [RNF1](backlog.md#rnf1-seguridad-y-aislamiento-de-datos), [RNF4](backlog.md#rnf4-respaldo-y-recuperacion) | Busca por persona o material; muestra fechas y estado | 3.3.2 · estado con texto e ícono | Hueco 2 · decisión 5 |
| **P7** Alta de material | **Ninguna** (propuesta: HU-03) | Nombre, registro, categoría, unidades y materia | 3.3.2 · 2.5.8 | Huecos 4 y 6 · decisión 1 |

Los números de hueco y de decisión remiten a las secciones [5](analisis-diseno.md#5-huecos-detectados-y-que-hicimos) y [7](analisis-diseno.md#7-decisiones-abiertas) del análisis.

---

## Contra lo que pide la actividad (Clase 8)

- [x] **Historias Must con su número en cada pantalla.** HU1–HU6 son todas Must; cada tablero las lleva en la barra superior.
- [x] **Una pantalla con el error de la regla.** P3 «Ya no está disponible»: otra persona confirmó la última unidad. La pantalla solo muestra el mensaje; quien decide es el backend, con una transacción que lee y descuenta en la misma operación.
- [x] **Una pantalla con estado vacío.** P5 «Sin préstamos activos»: dice qué pasa y a dónde ir, en vez de una tabla en blanco.
- [x] **Las tres marcas de accesibilidad.** 3.3.2 (etiqueta visible fuera de la caja), 2.5.8 (botones de 44 px) y 1.4.3 (contraste ≥ 4.5 : 1), marcadas en punteado azul en cada tablero.
- [ ] **Leídas al revés.** P7 no tiene historia. El equipo tiene que decidir: se justifica, se quita o falta HU-03.

### Cómo leer las marcas en el lienzo

| Marca | Significa |
|---|---|
| Pestaña negra en la barra superior | Historia o RNF que cubre la pantalla |
| Punteado azul | Marca de accesibilidad WCAG |
| Punteado naranja | Hueco detectado en el análisis |
| Contorno negro | Regla de negocio |

Los datos de las tablas (materiales, registros, fechas) son de ejemplo; las personas van como `[Alumno A]`, `[Nombre del usuario]`.
=======
![Flujo de pantallas](wireframes/ai-v2/Main.png)

Dos carriles: **alumno / profesor** arriba y **responsable del laboratorio** abajo. Cada flecha lleva la acción que la dispara. Las flechas punteadas en naranja son el camino de error. Las de puntos marcan el dato que cruza de un rol al otro: una solicitud que el responsable tiene que aprobar (HU-04) o una devolución que tiene que confirmar (HU-05).

Las cuatro condiciones de la actividad:

| Pide la actividad | Dónde está |
|---|---|
| Historias Must con su número en cada pantalla | Chips `HU-nn · M/S/C` arriba a la derecha de cada pantalla |
| Pantalla con el error de la regla | [A-06 · Sin existencias](wireframes/ai-v2/ErrorExistencias.png): RN7 / RNF-05, alguien pidió la última unidad |
| Pantalla con estado vacío | [A-03 · Inicio sin préstamos](wireframes/ai-v2/InicioVacio.png) |
| Tres marcas de accesibilidad | 3.3.2 (etiqueta visible), 2.5.8 (objetivo ≥ 24 px) y 1.4.3 (contraste, sin depender del color), anotadas en naranja junto al elemento |

---

## Pantalla → historias

| Pantalla | Imagen | Historias | RNF | Equivale a (inventario §6) |
|---|---|---|---|---|
| **A-01** Inicio de sesión | [Login.png](wireframes/ai-v2/Login.png) | — | RNF-01, RNF-02 | P1 |
| **A-02** Inicio del alumno | [Inicio.png](wireframes/ai-v2/Inicio.png) | HU-08, HU-10, HU-14, HU-20 | RNF-02 | *nueva* |
| **A-03** Inicio vacío | [InicioVacio.png](wireframes/ai-v2/InicioVacio.png) | HU-08 | RNF-02 | *nueva* |
| **A-04** Catálogo | [Catalogo.png](wireframes/ai-v2/Catalogo.png) | HU-06, HU-07, HU-12, HU-16 | RNF-02, RNF-05 | P2 |
| **A-05** Mi solicitud | [Solicitud.png](wireframes/ai-v2/Solicitud.png) | HU-07, HU-08, HU-09, HU-16 | RNF-02 | P3 |
| **A-06** Error: sin existencias | [ErrorExistencias.png](wireframes/ai-v2/ErrorExistencias.png) | HU-07, HU-09 | RNF-02, RNF-05 | P3 (error) |
| **A-07** Solicitud enviada | [SolicitudEnviada.png](wireframes/ai-v2/SolicitudEnviada.png) | HU-04, HU-09, HU-14 | — | P4 |
| **A-08** Registrar devolución | [Devolucion.png](wireframes/ai-v2/Devolucion.png) | HU-10, HU-14 | RNF-02 | *nueva* |
| **A-09** Mi historial | [Historial.png](wireframes/ai-v2/Historial.png) | HU-13 | RNF-01, RNF-02 | P6 (vista del alumno) |
| **R-01** Panel del responsable | [AdminPanel.png](wireframes/ai-v2/AdminPanel.png) | HU-04, HU-05, HU-11, HU-21 | — | P4, P5 |
| **R-02** Préstamos activos | [AdminPrestamos.png](wireframes/ai-v2/AdminPrestamos.png) | HU-05, HU-08, HU-11 | — | P5 |
| **R-03** Inventario | [AdminInventario.png](wireframes/ai-v2/AdminInventario.png) | HU-02, HU-03, HU-07, HU-18, HU-19 | — | P2 (vista del responsable) |
| **R-04** Alta / edición de material | [AdminMaterial.png](wireframes/ai-v2/AdminMaterial.png) | HU-03, HU-18, HU-19 | RNF-02 | P7 |
| **R-05** Historial general | [AdminHistorial.png](wireframes/ai-v2/AdminHistorial.png) | HU-01 | RNF-01 | P6 |
| **R-06** Reportes mensuales | [AdminReportes.png](wireframes/ai-v2/AdminReportes.png) | HU-15 | — | *nueva* |

La misma relación en forma de matriz: [Trazabilidad.png](wireframes/ai-v2/Trazabilidad.png).

## Historia → pantallas

Leída al revés, para comprobar que ninguna historia se quedó sin pantalla.

| Historia | Prioridad | Pantallas |
|---|---|---|
| HU-01 · Historial de préstamos (resp.) | M | R-05 |
| HU-02 · Inventario (resp.) | M | R-03 |
| HU-03 · Ingresar material | M | R-03, R-04 |
| HU-04 · Aprobar solicitudes | M | R-01, A-07 |
| HU-05 · Confirmar devolución | M | R-01, R-02 |
| HU-06 · Consultar materiales | M | A-04 |
| HU-07 · Cantidad disponible | M | A-04, A-05, A-06, R-03 |
| HU-08 · Fecha límite de devolución | M | A-02, A-03, A-05, R-02 |
| HU-09 · Solicitar préstamo | M | A-05, A-06, A-07 |
| HU-10 · Registrar devolución (usuario) | M | A-02, A-08 |
| HU-11 · Préstamos activos (resp.) | S | R-01, R-02 |
| HU-12 · Filtrar materiales | S | A-04 |
| HU-13 · Historial propio | S | A-09 |
| HU-14 · Notificaciones de confirmación | S | A-02, A-07, A-08 |
| HU-15 · Reportes mensuales | C | R-06 |
| HU-16 · Carrito / solicitud agrupada | C | A-04, A-05 |
| HU-17 · Cobro de material | **W** | **ninguna, a propósito**: fuera de alcance; R-06 solo lo menciona |
| HU-18 · Modificar material | M | R-03, R-04 |
| HU-19 · Estado de conservación | S | R-03, R-04 |
| HU-20 · Aviso antes de la fecha límite | C | A-02 |
| HU-21 · Aviso de devolución registrada | C | R-01 |
| RNF-01 · Acceso y aislamiento | M | A-01, A-09, R-05 |
| RNF-02 · Accesibilidad WCAG 2.1 AA | S | 9 pantallas con marcas (ver matriz) |
| RNF-03 · Rendimiento | S | sin pantalla: se verifica con la prueba de carga |
| RNF-04 · Respaldo y recuperación | M | sin pantalla: se verifica con el simulacro de restauración |
| RNF-05 · Disponibilidad actualizada | M | A-04, A-06 |

---

## Lo que el wireframe le pide al modelo de datos

Dibujar las pantallas sacó campos y estados que el [modelo de datos](modelo-datos.md) todavía no tiene. No se cambió el modelo; queda para que el equipo decida.

| Lo pide | Pantallas | Qué falta en el modelo |
|---|---|---|
| Barra de existencias (HU-07) | A-04, A-05, R-03 | Cantidad total y disponible por material: es el **hueco 1** de [análisis y diseño](analisis-diseno.md#5-huecos-detectados-y-que-hicimos) y la decisión abierta #1 |
| Descripción breve del material | A-04, A-05, R-04 | `Material.descripcion`: hoy solo existe `Categoria.descripcion` |
| Fecha límite de devolución (HU-08) | A-02, A-05, R-02 | `Prestamo.fecha_limite`; `fecha_devolucion` es cuándo se devolvió de verdad. Tampoco está definido **quién fija** la fecha límite |
| Aprobación (HU-04) y devolución en dos pasos (HU-10 → HU-05) | A-02, A-07, R-01 | Estados del préstamo en lugar del booleano `activo`: pendiente de aprobación, rechazada, activo, devolución por confirmar, cerrado |
| Solicitud con varios materiales (HU-16, HU-09) | A-05, A-07 | Material—Préstamo N:M: resuelve hacia N:M la decisión abierta #2 |
| Estado de conservación (HU-19) | R-03, R-04 | Un campo de conservación separado de `Material.estado` (disponible / prestado) |
| Buscar por persona, contactar | R-02, R-05 | Nombre y correo del usuario: **hueco 2** y decisión abierta #5 |
| Avisos (HU-14, HU-20, HU-21) | A-02, A-07, R-01 | Dónde se guardan las notificaciones y por qué canal salen |

## Léanlas al revés: ¿qué sobra?

Elementos dibujados que ninguna historia pide de forma literal. El equipo decide si **se justifica, se quita o falta una historia**:

- **«Rechazar…» solicitud** (R-01): HU-04 solo dice *aprobar*. Si se puede aprobar, alguien tiene que poder decir que no. Posiblemente falte una historia.
- **«Contactar»** a un alumno con préstamo vencido (R-02): ninguna historia lo pide y depende del correo del usuario, que tampoco existe.
- **«Reportar problema»** al confirmar una devolución (R-01): se apoya en HU-19, pero ninguna historia une conservación con devolución.
- **Exportar CSV** (R-06): ninguna historia lo pide. Se parece a la alternativa futura que menciona la justificación de HU-17 («registrar el adeudo y exportar el reporte»), pero esa alternativa no está en el alcance.

---

## Equivalencia con backlog.md

| backlog.md | Actividad 1 |
|---|---|
| HU1 · Ver el inventario | HU-02 |
| HU2 · Seleccionar material | HU-09 |
| HU3 · Registrar préstamo con usuario y fecha | HU-01, HU-04 |
| HU4 · Descontar cantidad disponible | HU-07 |
| HU5 · Registrar la devolución | HU-05, HU-10 |
| HU6 · Consultar historial | HU-01 |
| RNF1–RNF4 | RNF-01–RNF-04 |
| RNF5 · Disponibilidad en horario de laboratorio | *no existe en la Actividad 1* |
| RNF6 · Facilidad de uso | *no existe en la Actividad 1* |
| *no existe en backlog.md* | RNF-05 · Disponibilidad actualizada del inventario |

> [!warning] Las dos listas de RNF no coinciden
> RNF5 significa cosas distintas en cada documento: *disponibilidad en horario* en backlog.md y *disponibilidad actualizada del inventario* en la Actividad 1. Hay que unificar antes de entregar.

---

## Declaración de uso de IA

**Herramienta:** Claude Opus 5.5 (Anthropic), ejecutado mediante Claude Code con la herramienta de diseño Claude Design, en el entorno local de un integrante del equipo.

**Contexto proporcionado:** los documentos de esta carpeta `docs/` ([backlog](backlog.md), [análisis y diseño](analisis-diseno.md), [modelo de datos](modelo-datos.md)), el boceto del equipo [Wireframe LoFi 1](wireframes/Wireframe%20LoFi%201.md), el diagrama de flujo de clase («El flujo de pantallas») y las notas de clase del integrante: *Actividad 1 — Backlog priorizado y RNF* y *Diseño de pantallas*. Sin búsquedas en internet.

**Lo que hizo la IA:**

- Dibujar las pantallas lo-fi a partir del boceto del equipo, conservando su estructura (barra superior con pestañas, catálogo en cuadrícula, historial en lista).
- Extender el wireframe del rol Usuario (v1) al backlog completo con los dos roles (v2).
- Agregar, a pedido del equipo, la barra de existencias y la descripción breve de cada material.
- Construir la matriz de trazabilidad y las dos tablas de este documento.
- Señalar los huecos del modelo de datos y los elementos que ninguna historia pide.

**Lo que NO hizo la IA:**

- Las historias de usuario, su prioridad MoSCoW ni los RNF: vienen de la Actividad 1 del equipo.
- El boceto original ni la estructura de navegación.
- Decidir los huecos ni lo que sobra: quedan abiertos para el equipo.

**Validación humana:**

> [!question] Completar antes de entregar
> - Integrantes que revisaron los wireframes y fecha:
> - Qué se cambió después de la generación:
> - Decisiones tomadas sobre «¿qué sobra?»:

**El equipo asume la responsabilidad del contenido entregado.**
>>>>>>> da10dd5 (docs: wireframe AI v2 con backlog completo y trazabilidad)
