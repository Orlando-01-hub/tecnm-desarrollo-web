# Hito 1 · Contrato y mock API

**Sesión 10** · Peso: **7.5%** · Etiqueta: `v0.1-hito1`

> **Resultado:** una API en memoria con dos entidades relacionadas, contrato OpenAPI, validación y ocho pruebas HTTP en verde. Sin autenticación real ni base de datos todavía.

---

## 1. Qué debe existir al final

```text
GET  /docs                    -> Swagger con las rutas y modelos
GET  /openapi.json            -> contrato legible por máquina
GET  /health                  -> {"status":"ok"}

GET  /api/[padres]            -> 200 {"[padres]":[...]}
POST /api/[padres]            -> 201 {"id":1,...}
GET  /api/[padres]/{id}/[hijos] -> 200 {"[hijos]":[...]}
POST /api/[padres]/{id}/[hijos] -> 201 {"id":1,...}
DELETE /api/[hijos]/{id}      -> 204
GET  /api/[hijos]/999         -> 404
POST /api/[padres] {}         -> 422
PATCH /api/[hijos]/1          -> 405
```

En este hito se usa un **usuario fijo** (`usuario_id = 1`) para concentrarse en el contrato. La autenticación real llega en el Hito 2.

## 2. Qué construir

- Dos entidades con relación 1:N, definidas con DTOs de Pydantic.
- Almacenamiento **en memoria** (lista o diccionario); sin SQLite todavía.
- Validación de campos obligatorios, longitudes y rangos.
- Errores con un sobre uniforme: `{"error":{"codigo":"...","mensaje":"..."}}`.
- OpenAPI con título, versión, descripción y modelos visibles.
- Ocho pruebas HTTP que cualquiera pueda ejecutar.

### Ejemplo con el dominio Tablero → Tarea

| Método | Ruta | Qué hace |
| :--- | :--- | :--- |
| GET | `/api/tableros` | Lista los tableros del usuario fijo |
| POST | `/api/tableros` | Crea un tablero |
| GET | `/api/tableros/{id}/tareas` | Lista las tareas del tablero |
| POST | `/api/tableros/{id}/tareas` | Crea una tarea |
| GET | `/api/tareas/{id}` | Consulta una tarea |
| DELETE | `/api/tareas/{id}` | Elimina una tarea |
| GET | `/health` | Estado del servicio |

Adapta los nombres a tu dominio (espacios/reservas o categorías/gastos).

## 3. Criterios de aceptación

- [ ] La API arranca con `uvicorn` y responde en `/health`.
- [ ] `/docs` y `/openapi.json` muestran las dos entidades y sus modelos.
- [ ] Crear un registro principal válido responde **201** con `id`.
- [ ] Crear un registro inválido responde **422** con el sobre de error.
- [ ] Crear un hijo bajo un padre inexistente responde **404**.
- [ ] Eliminar un hijo existente responde **204**; consultarlo después da **404**.
- [ ] Un verbo no permitido responde **405**, no **404**.
- [ ] Las **ocho pruebas** se ejecutan con un solo comando y quedan en verde.
- [ ] El README explica cómo arrancar y probar.
- [ ] El tag `v0.1-hito1` existe en GitHub.

## 4. Las ocho pruebas

| # | Caso | Resultado esperado |
| :---: | :--- | :--- |
| 1 | Crear principal válido | 201 con `id` |
| 2 | Crear principal con campo inválido | 422 |
| 3 | Listar principales | 200 con lista |
| 4 | Crear hijo válido | 201 |
| 5 | Crear hijo en padre inexistente | 404 |
| 6 | Listar hijos del padre | 200 con lista |
| 7 | Eliminar hijo y consultarlo | 204 y luego 404 |
| 8 | Verbo no permitido | 405 |

Las pruebas pueden ser `pytest` + `httpx` o una colección Postman/Newman. Deben vivir en el repositorio y ejecutarse sin pasos manuales.

## 5. Entregables

- [ ] Código en `proyecto_integrador/src/`.
- [ ] Pruebas en `proyecto_integrador/tests/`.
- [ ] `README.md` del proyecto actualizado.
- [ ] `docs/decisiones.md` con la página del Hito 1:
  - Qué construí.
  - Qué decisión de diseño tomé y por qué.
  - Qué trade-off acepté.
  - Qué falta para el Hito 2.
- [ ] Tag `v0.1-hito1` empujado a GitHub.
- [ ] Checkpoint de la sesión 15 preparado.

## 6. Demo de 3 minutos

1. Abrir `/docs`.
2. Crear un registro principal.
3. Crear un hijo.
4. Listar.
5. Eliminar el hijo.
6. Provocar un 422 y mostrar el sobre de error.
7. Cerrar con la salida de las ocho pruebas.

## 7. Errores comunes que bajan la nota

- Devolver siempre 200, incluso en errores.
- Aceptar campos vacíos o tipos incorrectos.
- No documentar los modelos en OpenAPI.
- Usar el dominio de los laboratorios (biblioteca) o copiar su código.
- Pruebas que solo verifican el código HTTP y no el cuerpo.
- Un `README` que no permite ejecutar el proyecto desde cero.

## 8. Autoevaluación rápida

| Pregunta | Sí / No |
| :--- | :---: |
| ¿Puedo explicar la relación 1:N sin leer el código? | [ ] |
| ¿Mis ocho pruebas fallan si rompo una validación? | [ ] |
| ¿El contrato OpenAPI coincide con lo que responde la API? | [ ] |
| ¿Otro equipo podría ejecutar mi proyecto siguiendo el README? | [ ] |
| ¿El tag apunta al commit correcto? | [ ] |

Si alguna respuesta es “no”, aún no está listo para el Hito 1.
