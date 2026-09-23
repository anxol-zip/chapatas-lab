# Backlog priorizado

Historias de usuario, criterios de aceptación y requerimientos no funcionales del sistema de control de préstamos de material de laboratorio.

> [!note] De dónde sale esto
> Las historias HU1–HU6 y sus criterios vienen del taller de la Sesión 5 (formato Connextra + Gherkin). La prioridad MoSCoW y los RNF con métrica vienen de la Actividad 1, donde el equipo depuró el backlog con INVEST.

Documentos relacionados: [Análisis y diseño](analisis-diseno.md) · [Modelo de datos](modelo-datos.md) · [Modelo de documentos](modelo-documentos.md) · [Decisión de motor](decision-motor.md)

---

## Historias de usuario

### HU1: Ver el inventario

**Como** responsable del laboratorio
**quiero** ver el listado de materiales con su cantidad disponible
**para** saber qué hay antes de prestar algo

**Prioridad MoSCoW:** Must

**Criterios de aceptación**

- **Dado** que el responsable abre el sistema
- **Cuando** consulta el inventario
- **Entonces** aparece cada material con su cantidad disponible actualizada

---

### HU2: Seleccionar el material del prestamo

**Como** usuario (alumno o profesor)
**quiero** seleccionar los artículos que me estoy llevando
**para** registrar mi préstamo sin que alguien lo tenga que anotar a mano

**Prioridad MoSCoW:** Must

**Criterios de aceptación**

- **Dado** que el usuario ve la lista de materiales disponibles
- **Cuando** elige uno o varios y confirma
- **Entonces** el sistema crea el registro del préstamo

> [!warning] Choca con la cardinalidad del modelo
> "Elige uno o varios" implica N:M entre Préstamo y Material, pero el [modelo de datos](modelo-datos.md#relaciones-y-cardinalidades) tiene 1:N (un solo `material_id` por préstamo). Está anotado como decisión abierta.

---

### HU3: Registrar el prestamo con usuario y fecha

**Como** responsable del laboratorio
**quiero** que el sistema guarde quién se llevó cada artículo y en qué momento
**para** saber en todo momento quién tiene qué material

**Prioridad MoSCoW:** Must

**Criterios de aceptación**

- **Dado** que se confirma un préstamo
- **Cuando** el sistema lo registra
- **Entonces** queda guardado el artículo, el nombre del usuario y la fecha/hora

---

### HU4: Descontar la cantidad disponible

**Como** responsable del laboratorio
**quiero** que la cantidad disponible de un material baje automáticamente cuando se presta
**para** no prestar más de lo que realmente hay

**Prioridad MoSCoW:** Must

**Criterios de aceptación**

- **Dado** un material con, por ejemplo, 5 unidades disponibles
- **Cuando** se presta una unidad
- **Entonces** el sistema muestra 4 disponibles

---

### HU5: Registrar la devolucion

**Como** responsable del laboratorio
**quiero** poder registrar cuando alguien devuelve un artículo
**para** cerrar ese préstamo y que la cantidad disponible se recupere

**Prioridad MoSCoW:** Must

**Criterios de aceptación**

- **Dado** un préstamo activo
- **Cuando** el responsable marca el artículo como devuelto
- **Entonces** el préstamo cambia a estado "devuelto" y la cantidad disponible sube en uno

---

### HU6: Consultar el historial de prestamos

**Como** responsable del laboratorio
**quiero** poder buscar el historial de préstamos por persona o por artículo
**para** saber quién se llevó algo si llega a faltar

**Prioridad MoSCoW:** Must

**Criterios de aceptación**

- **Dado** que existen préstamos registrados
- **Cuando** se busca por nombre o por artículo
- **Entonces** el sistema muestra los préstamos relacionados, con fecha y estado (prestado/devuelto)

---

> [!question] Cómo asignamos el MoSCoW de HU1–HU6
> Nuestras notas priorizan con MoSCoW la lista **depurada** de la Actividad 1 (HU-01 … HU-17), no la numeración HU1–HU6 de la Sesión 5. Las seis de arriba corresponden a historias que en esa lista quedaron todas en **Must** (HU1↔HU-02, HU2↔HU-09, HU3↔HU-01/HU-04, HU4↔HU-07, HU5↔HU-05/HU-10, HU6↔HU-01), por eso todas dicen Must. Hay que confirmar la correspondencia con el equipo antes de entregar.

---

## Requerimientos no funcionales

### RNF1: Seguridad y aislamiento de datos

**Prioridad:** Must · **ISO/IEC 25010:** Seguridad

Toda operación requiere sesión autenticada, y ninguna cuenta puede leer ni modificar préstamos que no le pertenezcan, salvo el rol Administrador.

| Métrica | Umbral |
|---|---|
| Intentos de acceso a un préstamo ajeno rechazados | 100 %, respuesta `403 Forbidden` |
| Endpoints accesibles sin sesión válida | 0 |
| Contraseñas almacenadas en texto plano | 0 (hash con bcrypt, costo ≥ 12) |
| Sesión inactiva antes de cierre automático | ≤ 30 min |

**Cómo se verifica:** suite automatizada en CI que, autenticada como Usuario A, intenta leer y modificar los préstamos del Usuario B manipulando el identificador en la URL (prueba de IDOR); debe responder `403` en todos los casos. Se complementa con inspección de la tabla de usuarios (sin contraseñas legibles) y revisión manual de que ninguna ruta protegida responda sin token.

### RNF2: Accesibilidad WCAG 2.1 AA

**Prioridad:** Should · **ISO/IEC 25010:** Usabilidad (accesibilidad)

Las pantallas de catálogo, solicitud de préstamo y devolución deben ser operables por personas con discapacidad visual o motriz.

| Métrica | Umbral |
|---|---|
| Contraste de texto contra su fondo | ≥ 4.5:1 (normal) / ≥ 3:1 (grande) |
| Flujos completables solo con teclado | 100 % de los tres flujos críticos |
| Errores críticos de accesibilidad | 0 |
| Puntaje de accesibilidad en Lighthouse | ≥ 90 / 100 |
| Elementos interactivos sin nombre accesible | 0 |

**Cómo se verifica:** auditoría automatizada con axe DevTools y Lighthouse en el pipeline de CI sobre las tres pantallas críticas, más una prueba manual recorriendo el flujo de solicitud con `Tab`, `Enter` y `Esc` y un lector de pantalla activo (NVDA o VoiceOver).

### RNF3: Rendimiento bajo carga

**Prioridad:** Should · **ISO/IEC 25010:** Eficiencia de desempeño

El sistema debe sostener el pico de solicitudes concurrentes del inicio de una práctica sin degradar la experiencia.

| Métrica | Umbral |
|---|---|
| Consulta de catálogo (p95) | ≤ 2 s |
| Registro de una solicitud de préstamo (p95) | ≤ 3 s |
| Usuarios concurrentes sin degradación | ≥ 50 |
| Tasa de error bajo carga | ≤ 1 % |

**Cómo se verifica:** prueba de carga con k6 (o JMeter) simulando 50 usuarios virtuales durante 5 minutos contra los endpoints de catálogo y solicitud; el reporte debe mostrar `http_req_duration p(95)` bajo el umbral y `http_req_failed` menor a 1 %. Se corre antes de cada entrega mayor.

### RNF4: Respaldo y recuperacion

**Prioridad:** Must · **ISO/IEC 25010:** Fiabilidad (recuperabilidad)

La información de inventario, préstamos e historial debe poder restaurarse tras una falla, con pérdida acotada.

| Métrica | Umbral |
|---|---|
| Frecuencia del respaldo automático | diaria |
| RPO — pérdida máxima tolerada | ≤ 24 h |
| RTO — tiempo máximo de restauración | ≤ 4 h |
| Respaldos con restauración verificada | 100 % de los simulacros |
| Retención mínima | 30 días |

**Cómo se verifica:** simulacro mensual de recuperación: se restaura el respaldo más reciente en un entorno limpio, se cronometra y se comparan los conteos de inventario y préstamos contra producción. Se documenta con fecha, duración y resultado; un respaldo que nunca se ha restaurado no cuenta como respaldo válido.

### RNF5: Disponibilidad en horario de laboratorio

**Prioridad:** Should · **ISO/IEC 25010:** Fiabilidad (disponibilidad)

El sistema debe estar operando mientras el laboratorio está abierto, para no tener que volver a la libreta.

- **Dado** el horario de atención del laboratorio
- **Cuando** el sistema opera durante ese periodo
- **Entonces** se mantiene accesible, sin caídas frecuentes

> [!warning] Sin verificación
> Nuestras notas no definen ni el porcentaje de disponibilidad (¿99 %? ¿99.5 %?), ni el horario exacto del laboratorio, ni cómo se mide ("caídas frecuentes" no es una métrica). No inventamos el umbral: hay que fijarlo con el equipo.

### RNF6: Facilidad de uso

**Prioridad:** Should · **ISO/IEC 25010:** Usabilidad (aprendizaje)

El sistema debe poder operarse sin explicación previa.

- **Dado** alguien que nunca ha usado el sistema
- **Cuando** intenta registrar un préstamo por primera vez
- **Entonces** logra hacerlo sin ayuda externa

> [!warning] Sin verificación
> No hay métrica en las notas: no se dice con cuántas personas se prueba, en cuánto tiempo, ni qué tasa de éxito se considera aprobada. Tal como está, es un deseo, no un requerimiento verificable.

---

## Fuera de alcance (Won't have)

El cobro de material no repuesto (HU-17 de la Actividad 1) quedó explícitamente fuera de esta versión: es un proceso administrativo y financiero de la universidad, arrastra requerimientos que nunca se elicitaron (tabulador de precios, pasarela de pago, apelación) y maneja datos financieros de estudiantes. La alternativa contemplada para después es que el sistema **registre** el adeudo y exporte el reporte, no que lo cobre.
