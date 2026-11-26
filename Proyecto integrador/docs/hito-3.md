# Hito 3 · Persistencia y OWASP

**Sesión 30** · Peso: **7.5%** · Etiqueta: `v0.3-hito3`

> **Resultado:** la API del Hito 2 ahora guarda en una base de datos relacional, aplica una migración, respeta una transacción y resiste SQL Injection y CORS mal configurado. Doce pruebas en verde.

---

## 1. Qué debe existir al final

```text
GET  /api/[padres]                 -> lee de la base de datos
POST /api/[padres]                 -> inserta y devuelve el id
GET  /api/[padres]/{id}/[hijos]    -> lee la relación desde la base
DELETE /api/[hijos]/{id}           -> elimina con transacción

alembic upgrade head               -> crea el esquema
alembic downgrade base             -> lo revierte

GET /api/[padres]/buscar?texto=' OR '1'='1
                                     -> 200 {"[padres]":[]} (no toda la tabla)

OPTIONS /api/[padres]  Origin: https://app.ejemplo.com
                                     -> 200 con CORS permitido
```

## 2. Qué construir

### Base de datos y ORM

- SQLite para desarrollo (`sqlite:///proyecto.db`) o PostgreSQL si el equipo lo decide.
- Modelos SQLAlchemy con llave primaria, llave foránea y relación 1:N.
- Una migración Alembic que cree las tablas y pueda revertirse.
- Un `seed` o script que cree el admin y 5 registros de ejemplo.

### Operación transaccional

Elige una operación compuesta de tu dominio y hazla atómica:

- Crear una reserva y actualizar el estado del espacio.
- Crear un gasto y recalcular un total.
- Crear una tarea y actualizar el contador del tablero.

Si la segunda parte falla, la primera no debe quedar aplicada. Demuéstralo con una prueba de rollback.

### Seguridad OWASP

- Consultas **parametrizadas**; prohibido concatenar texto del usuario en SQL.
- CORS con lista blanca (`https://app.ejemplo.com` o el origen de tu SPA).
- Validación de entrada y salida; errores sin trazas internas.
- `.env` fuera del repositorio; `.env.example` documenta las variables.
- Sin secretos en el código ni en la base de datos.

### Esquema de ejemplo (Tablero → Tarea)

```text
usuarios
  id INTEGER PK
  usuario TEXT UNIQUE
  password_hash TEXT
  rol TEXT

tableros
  id INTEGER PK
  nombre TEXT
  usuario_id INTEGER FK -> usuarios.id

tareas
  id INTEGER PK
  tablero_id INTEGER FK -> tableros.id
  titulo TEXT
  estado TEXT
  prioridad TEXT
  fecha_limite DATE
```

## 3. Criterios de aceptación

- [ ] Los datos sobreviven al reinicio del servidor.
- [ ] `alembic upgrade head` crea el esquema desde una base vacía.
- [ ] `alembic downgrade base` revierte sin dejar tablas huérfanas.
- [ ] La relación 1:N se lee desde la base, no desde una lista en memoria.
- [ ] La operación compuesta usa transacción y hace rollback si falla.
- [ ] Una búsqueda con `' OR '1'='1` no devuelve toda la tabla.
- [ ] CORS permite el origen de la SPA y rechaza otro.
- [ ] Las **doce pruebas** quedan en verde.
- [ ] Tag `v0.3-hito3` creado.

## 4. Las doce pruebas

| # | Caso | Resultado esperado |
| :---: | :--- | :--- |
| 1 | Crear principal | 201 y fila en la base |
| 2 | Listar principales | 200 |
| 3 | Crear hijo bajo el padre | 201 |
| 4 | Padre inexistente | 404 |
| 5 | Hijo inválido | 422 |
| 6 | Eliminar hijo | 204 |
| 7 | Consultar eliminado | 404 |
| 8 | Persistencia tras reinicio | 200 con el dato |
| 9 | Búsqueda normal | 200 con resultados |
| 10 | Payload de SQLi | 200 sin toda la tabla |
| 11 | Rollback de la operación compuesta | sin cambios parciales |
| 12 | CORS permitido y rechazado | cabecera solo en el origen válido |

## 5. Entregables

- [ ] Modelos ORM y migración en `src/` y `migrations/`.
- [ ] `seed` o script de datos de ejemplo.
- [ ] Consultas parametrizadas y CORS con lista blanca.
- [ ] `docs/decisiones.md` con la página del Hito 3:
  - Esquema elegido.
  - Operación transaccional y por qué.
  - Cómo se protege la búsqueda.
- [ ] Doce pruebas en verde.
- [ ] Tag `v0.3-hito3`.
- [ ] Checkpoint de la sesión 35 preparado.

## 6. Demo de 3 minutos

1. Mostrar `alembic upgrade head`.
2. Crear un registro y reiniciar el servidor.
3. Listar y comprobar que el dato sigue.
4. Ejecutar la búsqueda normal y el payload de SQLi.
5. Mostrar el rollback de la operación compuesta.
6. Mostrar el preflight CORS permitido y el rechazado.
7. Mostrar la salida de las doce pruebas.

## 7. Errores comunes que bajan la nota

- Usar `create_all` en lugar de una migración versionada.
- Concatenar SQL con la entrada del usuario.
- Mantener los datos en una lista y “simular” la base.
- No probar el rollback.
- Configurar CORS con `*` y llamarlo “seguro”.
- Subir `.env` o la base de datos al repositorio.
- No documentar el esquema ni la migración.

## 8. Autoevaluación rápida

| Pregunta | Sí / No |
| :--- | :---: |
| ¿Puedo crear la base desde cero con un comando? | [ ] |
| ¿Puedo revertir la migración? | [ ] |
| ¿La operación compuesta deja todo o nada? | [ ] |
| ¿El payload de SQLi devuelve cero resultados? | [ ] |
| ¿CORS solo autoriza el origen de mi SPA? | [ ] |

Si alguna respuesta es “no”, aún no está listo para el Hito 3.
