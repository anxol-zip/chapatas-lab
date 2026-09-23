# Modelo de documentos (NoSQL)

Traducción del [modelo relacional](modelo-datos.md) a una base de documentos (MongoDB / Firestore). No repetimos los campos aquí: cada colección enlaza a su entidad en [modelo-datos.md](modelo-datos.md).

Documentos relacionados: [Backlog](backlog.md) · [Modelo de datos](modelo-datos.md) · [Decisión de motor](decision-motor.md) · [Análisis y diseño](analisis-diseno.md)

---

## Que se embebe y que se referencia

Una base de documentos no tiene `join`. La decisión ya no es "qué tan separadas deben estar las tablas para evitar anomalías", sino:

- **Se embebe** lo que se lee siempre junto y hay que mostrar sin una segunda lectura.
- **Se referencia** lo que cambia por su cuenta, lo que se comparte entre muchos documentos, o lo que puede crecer sin límite.

Duplicar a propósito deja de ser la excepción y se vuelve el mecanismo por defecto — pero la regla para decidir no cambió: **no está en la forma, está en si existe una razón escrita.**

| Dato | Decisión | Por qué |
|---|---|---|
| `material.categoriaId` | **Referencia** | Ya sabemos que los nombres de categoría se corrigen y se renombran. Embeberlo reintroduce exactamente la anomalía de actualización que la observación del profesor nos pidió eliminar. Precio: mostrar el nombre cuesta dos lecturas. |
| `material.prestamoAbierto` | **Embebido** (redundante a propósito) | Responder "¿está disponible?" sin recorrer toda la colección `prestamos`. Es el mismo papel que juega el booleano `activo` en el modelo relacional. **Precio:** al cerrar el préstamo alguien tiene que ponerlo en `null`, o el material queda marcado como prestado para siempre. |
| `prestamo.usuario` y `prestamo.administrador` | **Embebidos** (copia al momento del préstamo) | Mismo principio que una factura, que guarda el precio de la venta y no una liga al precio actual: si el usuario corrige su nombre después, el préstamo viejo no se entera — y para un historial eso es lo correcto. |
| `prestamo.material` | **Embebido** con `id` + `registro` | Para listar el historial sin leer la colección `materiales`. El `id` sigue siendo referencia al documento completo. |

> [!info] camelCase, no snake_case
> Las tablas SQL usan `snake_case` (`fecha_inicio`, `categoria_id`); los documentos usan `camelCase` (`fechaInicio`, `categoriaId`), que es la convención cuando el backend de una base de documentos se escribe en JS/TS.

---

## Coleccion usuarios

Campos y significado: ver [Usuario](modelo-datos.md#usuario).

```json
{
  "_id": "usr_77c",
  "rol": "alumno"
}
```

> [!question] Documento casi vacío
> Con solo `rol`, el documento no justifica una colección. Los campos que faltan (`nombre`, `identificador`, `correo`) son los mismos que señala el [modelo de datos](modelo-datos.md#usuario).

## Coleccion administradores

Campos y significado: ver [Administrador](modelo-datos.md#administrador).

```json
{
  "_id": "adm_02b"
}
```

> [!warning] Un documento sin atributos propios
> Como documento independiente es literalmente `{ _id }`. Es la misma señal que llevó a la diapositiva de clase a fusionar Usuario y Administrador en una sola colección `personas` con `rol`. Queda como [decisión abierta](analisis-diseno.md#7-decisiones-abiertas).

## Coleccion categorias

Campos y significado: ver [Categoria](modelo-datos.md#categoria).

```json
{
  "_id": "cat_04f",
  "nombre": "Redes",
  "descripcion": null
}
```

## Coleccion materiales

Campos y significado: ver [Material](modelo-datos.md#material).

```json
{
  "_id": "mat_31a",
  "categoriaId": "cat_04f",
  "activo": true,
  "registro": "REG-2026-0031",
  "materia": "Redes 1",
  "estado": "prestado",
  "prestamoAbierto": {
    "prestamoId": "p_9a1",
    "fechaInicio": "2026-09-14T15:10:00Z"
  }
}
```

`prestamoAbierto` es el único campo que no existe en el [modelo relacional](modelo-datos.md#material): ahí esa pregunta la respondía el índice único parcial de [RN1](modelo-datos.md#reglas-de-negocio). Sin `join` ni índices que crucen colecciones, embeberlo es la forma razonable de responder "¿está disponible?" rápido. Cuando el material está libre, vale `null`.

## Coleccion prestamos

Campos y significado: ver [Prestamo](modelo-datos.md#prestamo).

```json
{
  "_id": "p_9a1",
  "usuario": { "id": "usr_77c", "rol": "alumno" },
  "administrador": { "id": "adm_02b" },
  "material": { "id": "mat_31a", "registro": "REG-2026-0031" },
  "fechaInicio": "2026-09-14T15:10:00Z",
  "fechaDevolucion": null,
  "activo": true
}
```

> [!warning] Si Préstamo—Material resulta ser N:M
> Como está anotado en el [modelo de datos](modelo-datos.md#relaciones-y-cardinalidades), [HU2](backlog.md#hu2-seleccionar-el-material-del-prestamo) ("elige uno o varios") apunta a N:M. En documentos eso no exige una tercera colección: se resuelve con un arreglo embebido, que reemplaza a la tabla puente del modelo relacional.
>
> ```json
> "materiales": [
>   { "materialId": "mat_31a", "registro": "REG-2026-0031" },
>   { "materialId": "mat_09f", "registro": "REG-2026-0044" }
> ]
> ```

---

## Lo que se pierde al pasar a documentos

| Garantía del modelo relacional | En documentos |
|---|---|
| [RN1](modelo-datos.md#reglas-de-negocio) (un material en un solo préstamo abierto), vía índice único parcial | No hay índice que cruce colecciones. Queda del lado de la aplicación. |
| [RN2](modelo-datos.md#reglas-de-negocio) (integridad referencial), vía llave foránea | No existe la llave foránea. Un `materialId` puede quedar apuntando a nada. |
| [RN5](modelo-datos.md#reglas-de-negocio) (nombre de categoría único), vía `unique` | Se aproxima con un índice único de colección, pero no en todos los motores igual. |

Esto es lo que pesa en la [decisión de motor](decision-motor.md).
