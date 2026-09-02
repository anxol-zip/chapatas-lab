---
fecha: 2026-09-02
curso: Diseño de Software
tema: "Descripción del sistema y requisitos (funcionales y no funcionales) - Préstamo de material de laboratorio"
---

# Sistema de Préstamo de Material de Laboratorio

## Descripción del sistema

La idea es hacer un sistema para controlar el préstamo de material del laboratorio. Ahorita todo se lleva en una libreta: cuando alguien (un alumno o un profesor) se lleva algo, el responsable del laboratorio lo anota a mano. El problema es que así no se sabe con certeza qué está prestado, quién lo tiene, ni desde cuándo, y cada semestre se pierde material porque no hay forma clara de rastrear quién se llevó qué.

Lo que quiero hacer es un sistema donde:

- Se pueda ver qué materiales hay disponibles y cuántos de cada uno.
- El usuario (alumno o profesor) pueda elegir los artículos que se va a llevar.
- El sistema deje registrado quién se llevó cada cosa y cuándo, para que si algo no regresa se pueda saber quién lo tenía.
- Cuando alguien devuelve el material, se pueda marcar como devuelto y que la cantidad disponible se actualice sola.

En pocas palabras: no es tanto "evitar que roben" en el sentido de impedirlo físicamente, sino que el sistema siempre sepa quién tiene cada cosa, para que haya una responsabilidad clara y se pueda dar seguimiento si algo se pierde.

---

## Requisitos Funcionales (RF)

Estos son las cosas que el sistema **hace**, se pueden comprobar dando clic o usando el sistema directamente.

### RF1 — Ver el inventario
Como responsable del laboratorio, quiero ver el listado de materiales con su cantidad disponible, para saber qué hay antes de prestar algo.

- Se comprueba así: el responsable abre el sistema, ve el inventario, y aparece cada material con su cantidad actualizada.

### RF2 — Seleccionar los artículos que me llevo
Como usuario (alumno o profesor), quiero seleccionar los artículos que me estoy llevando, para registrar mi préstamo sin que alguien lo tenga que anotar a mano.

- Se comprueba así: el usuario ve la lista de materiales, elige uno o varios, confirma, y el sistema crea el registro del préstamo.

### RF3 — Registrar quién se lleva cada cosa
Como responsable del laboratorio, quiero que el sistema guarde quién se llevó cada artículo y en qué momento, para saber en todo momento quién tiene qué material.

- Se comprueba así: al confirmar un préstamo, queda guardado el artículo, el nombre del usuario y la fecha/hora.

### RF4 — Descontar la cantidad disponible
Como responsable del laboratorio, quiero que la cantidad disponible de un material baje automáticamente cuando se presta, para no prestar más de lo que realmente hay.

- Se comprueba así: si un material tiene, por ejemplo, 5 disponibles y se presta uno, el sistema muestra 4 disponibles.

### RF5 — Registrar la devolución
Como responsable del laboratorio, quiero poder registrar cuando alguien devuelve un artículo, para cerrar ese préstamo y que la cantidad disponible se recupere.

- Se comprueba así: el responsable marca el artículo como devuelto, el préstamo cambia de estado a "devuelto" y la cantidad disponible sube en uno.

### RF6 — Consultar el historial de préstamos
Como responsable del laboratorio, quiero poder buscar el historial de préstamos por persona o por artículo, para saber quién se llevó algo si llega a faltar.

- Se comprueba así: se busca por nombre o por artículo y el sistema muestra los préstamos relacionados, con fecha y estado (prestado/devuelto).

---

## Requisitos No Funcionales (RNF)

Estos no son acciones que el sistema hace, sino **cualidades** de cómo se comporta. No se comprueban dando un clic, sino midiendo o poniendo a prueba el sistema.

### RNF1 — Privacidad entre usuarios
Un alumno no debe poder ver los préstamos de otro alumno, solo los suyos. El responsable del laboratorio sí puede ver todo.

- Se comprueba así: intentando entrar con la cuenta de un alumno y checando que no pueda ver información de préstamos de otra persona.

### RNF2 — Rapidez al consultar el inventario
El listado de materiales debe cargar rápido, para que el responsable no pierda tiempo cada vez que alguien va a pedir algo prestado.

- Se comprueba así: midiendo cuánto tarda en aparecer el inventario al abrir el sistema (debería ser casi inmediato, un par de segundos como máximo).

### RNF3 — Facilidad de uso
El sistema debe ser fácil de usar sin necesitar explicación previa, porque lo va a usar el responsable del laboratorio en el día a día, no un experto en tecnología.

- Se comprueba así: dándoselo a alguien que no lo conoce y viendo si logra registrar un préstamo sin ayuda.

### RNF4 — Que no se pierda información
Los registros de préstamos y devoluciones no se deben perder ni corromper, porque son la prueba de quién tiene cada cosa.

- Se comprueba así: revisando que después de varios préstamos y devoluciones seguidas, el historial siga completo y correcto.

### RNF5 — Disponibilidad durante el horario del laboratorio
El sistema debe estar funcionando mientras el laboratorio está abierto, para que no se tenga que volver a usar la libreta si se cae el sistema.

- Se comprueba así: revisando que el sistema esté accesible durante todo el horario de atención del laboratorio, sin caídas frecuentes.

---

## Resumen en una línea
El sistema deja ver qué material hay, permite que el usuario elija lo que se lleva, y siempre sabe quién tiene qué y desde cuándo — eso es lo funcional. Que sea rápido, fácil de usar, privado entre usuarios y que no pierda información — eso es lo no funcional.
