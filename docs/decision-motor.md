# Decisión de motor de base de datos

Documentos relacionados: [Backlog](backlog.md) · [Modelo de datos](modelo-datos.md) · [Modelo de documentos](modelo-documentos.md) · [Análisis y diseño](analisis-diseno.md)

---

## Motor elegido

> [!question] Pendiente: confirmar motor con el equipo
> Nuestras notas de clase **no registran una decisión de motor**. Lo único que aparece es la lista de motores mencionados en el pizarrón de la clase de bases no relacionales — **MongoDB** y **Firestore** — y los ejemplos en SQL de la diapositiva, que usan sintaxis de PostgreSQL (`uuid`, `gen_random_uuid()`, índice único parcial con `where`). Nada de eso es una elección del equipo, así que no la damos por tomada.
>
> Lo que sí está decidido y documentado es **la forma del modelo en las dos familias**: el [modelo relacional](modelo-datos.md) y su [traducción a documentos](modelo-documentos.md). Falta que el equipo escriba cuál se implementa.

---

## Comparacion

La comparación de abajo es el insumo para esa decisión, apoyada en los [RNF](backlog.md#requerimientos-no-funcionales). No es la decisión.

| Criterio | Relacional (tipo PostgreSQL) | Documentos (Mongo / Firestore) |
|---|---|---|
| [RN1](modelo-datos.md#reglas-de-negocio) — un material en un solo préstamo abierto | La garantiza el motor con un índice único parcial | No hay índice que cruce colecciones: queda en la aplicación |
| [RN2](modelo-datos.md#reglas-de-negocio) — integridad referencial | La garantiza la llave foránea | No existe; un `materialId` puede apuntar a nada |
| [RN5](modelo-datos.md#reglas-de-negocio) — nombre de categoría único | `unique` nativo | Índice único de colección, desigual según el motor |
| [RNF1](backlog.md#rnf1-seguridad-y-aislamiento-de-datos) — aislamiento de datos | Se resuelve en el backend en ambos casos; Firestore además ofrece reglas de seguridad declarativas del lado del servidor | Igual, más reglas declarativas en Firestore |
| [RNF3](backlog.md#rnf3-rendimiento-bajo-carga) — catálogo p95 ≤ 2 s con 50 concurrentes | El catálogo exige un `join` con [Categoria](modelo-datos.md#categoria); a esta escala es irrelevante | El catálogo exige dos lecturas (o embeber la categoría, con su costo de actualización) |
| [RNF4](backlog.md#rnf4-respaldo-y-recuperacion) — respaldo diario, RPO ≤ 24 h, RTO ≤ 4 h | `pg_dump` / snapshots; restauración verificable y cronometrable | Respaldos gestionados en los servicios administrados |
| Cardinalidad en duda Préstamo—Material | Si es N:M hace falta una tabla puente (`prestamo_material`) | Si es N:M se resuelve con un arreglo embebido, sin tercera colección |

> [!note] Lo que sí podemos decir hoy
> Las reglas que hoy nos cuestan trabajo ([RN1](modelo-datos.md#reglas-de-negocio), [RN2](modelo-datos.md#reglas-de-negocio), [RN5](modelo-datos.md#reglas-de-negocio)) son exactamente las que un motor relacional garantiza solo y uno de documentos deja en manos de la aplicación. Eso inclina la balanza hacia el relacional para esta primera versión, pero **inclinar no es decidir**: lo escribimos como argumento, no como conclusión.

---

## La regla no garantizada

Hay una regla de negocio que **ningún motor garantiza por sí solo**, elijamos el que elijamos:

> **[RN7](modelo-datos.md#reglas-de-negocio) — No se puede prestar más material del que hay disponible.**

Es la regla de [HU4](backlog.md#hu4-descontar-la-cantidad-disponible) ("de 5 disponibles, se presta una, quedan 4") y de [HU1](backlog.md#hu1-ver-el-inventario).

**Por qué no la garantiza el motor:**

1. **Hoy ni siquiera se puede expresar.** [Material](modelo-datos.md#material) no tiene ningún campo de cantidad: solo `estado`, que es un texto. No hay nada que un `check` pueda comparar.
2. **Aunque existiera `cantidad_disponible`, un `check (cantidad_disponible >= 0)` no basta.** Es una regla que cruza dos tablas (contar los préstamos abiertos de ese material contra su existencia), y eso no cabe en una restricción de columna.
3. **Es una condición de carrera.** Dos usuarios que piden la última unidad al mismo tiempo leen "1 disponible" a la vez, y los dos creen que pueden. Esto es especialmente probable en el pico de inicio de práctica que describe [RNF3](backlog.md#rnf3-rendimiento-bajo-carga) (≥ 50 usuarios concurrentes).
4. **En documentos el problema es peor**, porque además [RN1](modelo-datos.md#reglas-de-negocio) y [RN2](modelo-datos.md#reglas-de-negocio) también se caen al lado de la aplicación, y `material.prestamoAbierto` es una copia que alguien tiene que mantener sincronizada.

**Cómo la resolvería la aplicación:**

- **Si el material se modela por unidad física** (una fila por aparato), la regla se vuelve [RN1](modelo-datos.md#reglas-de-negocio) y el índice único parcial sobre `material_id where activo` **sí la garantiza**: el segundo préstamo simplemente falla al insertar. Esta es la opción que nos gusta, porque mueve la regla del código al motor.
- **Si el material se modela con un contador**, el préstamo tiene que registrarse dentro de una **transacción** que lea el disponible y lo descuente en la misma operación, bloqueando la fila (`select ... for update`), y el backend debe rechazar la solicitud con un error claro cuando ya no alcanza. En una base de documentos sería una actualización condicional atómica (`findOneAndUpdate` con la condición sobre el contador) o una transacción del motor.
- **En los dos casos, la validación de pantalla no cuenta.** Esconder el botón de "solicitar" cuando marca 0 disponibles es decoración: un `if` del frontend se salta desde la consola del navegador en diez segundos. La regla vive en el backend.

> [!warning] Lo que hay que decidir primero
> Cuál de los dos modelados de Material se adopta **cambia la respuesta**. Está en [decisiones abiertas](analisis-diseno.md#7-decisiones-abiertas).
