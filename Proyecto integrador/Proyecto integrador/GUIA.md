# Proyecto Integrador · Guía oficial

**Desarrollo de Aplicaciones Web (DAB-2201)**  
**Tecnológico Nacional de México — Campus Cancún**  
**Docente:** MCC. Iván Márquez Larios (`ijmarquezl`)

> **La idea en una frase:** una API pequeña, segura y persistente, con una SPA de una página, construida por un equipo de dos personas a lo largo de cuatro hitos.

---

## 1. Qué vas a construir

Un producto mínimo funcional con **dos entidades relacionadas** (1:N) y **un usuario**:

- Una persona se registra e inicia sesión.
- Crea registros de la entidad principal (por ejemplo, un tablero).
- Dentro de cada registro principal, crea registros de la entidad secundaria (por ejemplo, tareas).
- Consulta, filtra y elimina; solo ve lo suyo (o todo, si es administrador).
- Todo con validación, errores claros, pruebas automatizadas, documentación OpenAPI y una interfaz web mínima.

### Resultado final del Hito 4

```text
https://<tu-demo>/            -> SPA: login, lista, formulario y eliminar
https://<tu-demo>/docs        -> OpenAPI / Swagger
https://<tu-demo>/health      -> {"status":"ok"}

POST /api/registro            -> 201
POST /api/token               -> 200 + JWT
GET  /api/<padres>            -> 200 (solo los míos; admin ve todos)
POST /api/<padres>            -> 201
GET  /api/<padres>/{id}/<hijos> -> 200
POST /api/<padres>/{id}/<hijos> -> 201
DELETE /api/<hijos>/{id}      -> 204 (dueño o admin; si no, 403)
```

La SPA debe permitir **iniciar sesión, listar, crear y eliminar**. No necesitas más pantallas.

---

## 2. Alcance

### Lo que sí incluye

| Elemento | Límite |
| :--- | :--- |
| Entidades de negocio | **2** (una principal y una secundaria, relación 1:N) |
| Entidad de usuario | 1 (`id`, `usuario`, `password_hash`, `rol`) |
| Endpoints de negocio | **8–10** en total, incluidos auth y health |
| Interfaz | **1 página** con login, lista, formulario y eliminar |
| Roles | `user` y `admin` |
| Base de datos | SQLite en desarrollo; PostgreSQL opcional |
| Pruebas | 8 en Hito 1, 10 en Hito 2, 12 en Hito 3 y flujo E2E en Hito 4 |
| Documentación | README, OpenAPI y una página de decisiones por hito |

### Lo que no incluye

No se aceptan, salvo autorización escrita del docente:

- Una tercera entidad de negocio.
- Pagos, suscripciones o facturación.
- Chat, notificaciones en tiempo real, websockets o correo real.
- Aplicaciones móviles nativas.
- Integraciones con APIs externas.
- Recomendadores, IA generativa dentro del producto o ciencia de datos.
- Frameworks de frontend (React, Vue, Angular). La SPA se hace con HTML, CSS y JavaScript vanilla.
- Subida de archivos, imágenes o multimedia.
- Paneles administrativos elaborados. El rol `admin` solo demuestra autorización.

> **Regla de oro del alcance:** si una funcionalidad no aparece en esta guía, no entra al proyecto sin una solicitud escrita y aprobada antes del Hito 1.

---

## 3. Catálogo de dominios

Cada equipo elige **un dominio** del catálogo. No se puede usar el dominio de los laboratorios (biblioteca/libros) ni copiar el proyecto de otro equipo.

### A. Tablero → Tarea

- **Entidad principal:** `Tablero` (`id`, `nombre`, `descripcion`, `usuario_id`).
- **Entidad secundaria:** `Tarea` (`id`, `tablero_id`, `titulo`, `estado`, `prioridad`, `fecha_limite`).
- **Reglas:** `estado` ∈ {`pendiente`, `en_progreso`, `hecha`}; `prioridad` ∈ {`baja`, `media`, `alta`}; el tablero solo lo ve su dueño.
- **Filtro:** `GET /api/tableros/{id}/tareas?estado=pendiente`.
- **Valor demostrable:** organización personal y seguimiento de pendientes.

### B. Espacio → Reserva

- **Entidad principal:** `Espacio` (`id`, `nombre`, `capacidad`, `usuario_id`).
- **Entidad secundaria:** `Reserva` (`id`, `espacio_id`, `usuario_id`, `inicio`, `fin`, `estado`).
- **Reglas:** `inicio < fin`; no se permiten reservas solapadas para el mismo espacio; `estado` ∈ {`activa`, `cancelada`}; solo el dueño o un admin puede cancelar.
- **Filtro:** `GET /api/espacios/{id}/reservas?desde=2026-11-01`.
- **Valor demostrable:** validación de reglas de negocio y choques de horario.

### C. Categoría → Gasto

- **Entidad principal:** `Categoria` (`id`, `nombre`, `usuario_id`).
- **Entidad secundaria:** `Gasto` (`id`, `categoria_id`, `monto`, `fecha`, `descripcion`).
- **Reglas:** `monto > 0`; la fecha no puede ser futura; solo el dueño ve sus gastos; el total del mes se calcula en la consulta.
- **Filtro:** `GET /api/categorias/{id}/gastos?mes=2026-10`.
- **Valor demostrable:** agregaciones simples y reporte mensual.

### Cómo elegir

1. Revisa los tres dominios con tu compañero.
2. Elige el que puedan explicar en una frase y defender sin depender de datos externos.
3. Regístralo en la propuesta de la sesión 4.
4. No cambies de dominio después del Hito 1; si es indispensable, solicítalo por escrito antes.

---

## 4. Stack y estructura

### Stack permitido

- **Backend:** Python 3.10+ con FastAPI, Pydantic v2, SQLAlchemy 2.x y Alembic.  
  **Alternativa:** PHP 8+ con un framework minimalista, solo con aprobación previa del docente.
- **Base de datos:** SQLite para desarrollo; PostgreSQL opcional para el Hito 4.
- **Frontend:** HTML, CSS y JavaScript vanilla con `fetch`.
- **Pruebas:** `pytest` + `httpx`, o una colección de Postman/Newman.
- **Contenedor:** Dockerfile en el Hito 4.
- **Control de versiones:** Git y GitHub; una etiqueta por hito.

No se permite cambiar de stack a mitad del proyecto.

### Estructura recomendada del repositorio

```text
proyecto_integrador/
├── README.md              # presentación del equipo y del producto
├── requirements.txt
├── .env.example
├── Dockerfile             # obligatorio en el Hito 4
├── src/
│   ├── main.py
│   ├── core/              # configuración, seguridad, dependencias
│   ├── routers/           # endpoints
│   ├── services/          # reglas de negocio
│   ├── repositories/      # acceso a datos
│   ├── models/            # modelos ORM
│   └── schemas/           # DTOs de entrada y salida
├── migrations/            # Alembic
├── tests/
│   ├── test_hito1.py
│   ├── test_hito2.py
│   └── test_hito3.py
├── frontend/
│   ├── index.html
│   └── app.js
└── docs/
    ├── propuesta.md
    ├── equipo.md
    ├── decisiones.md
    ├── checkpoint-01.md
    ├── checkpoint-02.md
    ├── checkpoint-03.md
    └── evidencias/
```

Puedes ajustar nombres, pero **mantén separadas** las capas de router, servicio, repositorio, modelos y esquemas a partir del Hito 2.

---

## 5. Equipos y roles

- **Equipos de 2.** Si el número de estudiantes es impar, el docente puede autorizar un equipo de 3 o un proyecto individual.
- **Roles sugeridos:**
  - **A — Backend y datos:** endpoints, modelos, migraciones, seguridad.
  - **B — Frontend y calidad:** SPA, pruebas, documentación, demo.
- Ambos deben poder explicar **todo** el proyecto. Los roles no son una excusa para no entender la otra mitad.
- Cada miembro debe tener **al menos un commit significativo por hito**.
- El acuerdo de equipo se firma en `docs/equipo.md` antes del Hito 1.

### Si un miembro no cumple

1. Conversación documentada en el canal del equipo.
2. Aviso por escrito al docente antes del checkpoint.
3. Si persiste, el docente puede aplicar un factor individual menor y asignar una recuperación.
4. El abandono no castiga al compañero: el equipo puede continuar y el estudiante que no participó recibe la evaluación individual que corresponda.

---

## 6. Los cuatro hitos

| Hito | Sesión | Foco | Entregable | Etiqueta | Peso |
| :--- | :---: | :--- | :--- | :---: | :---: |
| **1** | 10 | Contrato y mock | API en memoria, 2 entidades, OpenAPI, validación, errores, 8 pruebas | `v0.1-hito1` | 7.5% |
| **2** | 20 | Capas y seguridad | Router/servicio/repositorio, DI, registro/login, JWT, RBAC, 10 pruebas | `v0.2-hito2` | 7.5% |
| **3** | 30 | Persistencia y OWASP | SQLite/PostgreSQL, ORM, migración, transacciones, SQLi/CORS, 12 pruebas | `v0.3-hito3` | 7.5% |
| **4** | 40 | Producto | SPA, Dockerfile, variables de entorno, E2E, demo de 5 minutos | `v1.0-final` | 7.5% |

Cada hito es **acumulativo**: se construye sobre el anterior. No se reescribe desde cero.

### Hito 1 — Contrato y mock

- API en memoria con las dos entidades y un usuario fijo.
- DTOs con validación, errores semánticos y OpenAPI en `/docs`.
- Ocho pruebas HTTP documentadas.
- Sin autenticación real ni base de datos todavía.
- **Detalle:** [`docs/hito-1.md`](docs/hito-1.md).

### Hito 2 — Capas y seguridad

- Separación router → servicio → repositorio e inyección de dependencias.
- Registro con hash, login con JWT, roles `user`/`admin`.
- Rutas protegidas, 401/403 correctos y diez pruebas.
- **Detalle:** [`docs/hito-2.md`](docs/hito-2.md).

### Hito 3 — Persistencia y OWASP

- SQLite o PostgreSQL con SQLAlchemy y migración Alembic.
- CRUD persistente, transacción y rollback en una operación compuesta.
- Consultas parametrizadas, CORS con lista blanca y doce pruebas.
- **Detalle:** [`docs/hito-3.md`](docs/hito-3.md).

### Hito 4 — Producto

- SPA de una página con login, lista, formulario y eliminar.
- Dockerfile, `.env.example`, variables de entorno y demo reproducible.
- Flujo E2E y demo de 5 minutos.
- **Detalle:** [`docs/hito-4.md`](docs/hito-4.md).

---

## 7. Cómo pido avances

El proyecto no se evalúa solo al final. Hay **cuatro momentos**:

| Momento | Sesión | Qué se entrega |
| :--- | :---: | :--- |
| **Propuesta** | 4 | Equipo, dominio, entidades, endpoints, riesgos y plan de hitos |
| **Checkpoint 1** | 15 | Estado del Hito 1 + qué sigue + bloqueo + evidencia |
| **Checkpoint 2** | 25 | Estado del Hito 2 + qué sigue + bloqueo + evidencia |
| **Checkpoint 3** | 35 | Ensayo del Hito 4 + riesgos de cierre + evidencia |

### Formato del checkpoint

Copia [`docs/checkpoint.md`](docs/checkpoint.md) a `docs/checkpoint-0X.md` y completa:

- **Hecho:** qué funciona hoy, con commit o tag.
- **Siguiente:** qué harás antes del próximo hito.
- **Bloqueo:** qué no puedes resolver solo.
- **Evidencia:** enlace al commit, tag, prueba o captura.
- **Demo de 3 minutos:** qué mostrarías si te tocara hoy.

### Regla de avance

- Todos los equipos entregan el checkpoint **antes de la clase**.
- En clase se eligen 2–3 equipos al azar para demostrar durante 3 minutos.
- Si no hay checkpoint, el hito correspondiente se **limita a 70%** hasta que se regularice.
- El checkpoint no suma nota extra: es un filtro para detectar problemas a tiempo.

---

## 8. Reglas del juego

### Repositorio y commits

- Un repositorio por equipo, en el fork del curso o en un repositorio propio enlazado desde Moodle.
- El proyecto vive en `proyecto_integrador/`.
- Cada hito termina con un **tag** y un **commit** con mensaje claro.
- No se aceptan archivos comprimidos, capturas gigantes ni binarios innecesarios.

### Autoría e IA

- El código debe ser comprendido por ambos integrantes.
- La IA se permite como copiloto, pero cada hito incluye en `docs/decisiones.md` qué se consultó, qué se aceptó y qué se corrigió.
- Copiar código de otro equipo, del docente o de un repositorio sin citar es falta de integridad académica.
- Se puede reutilizar **patrones** vistos en los laboratorios; no se puede copiar el dominio ni el código literal de la práctica.

### Seguridad mínima

- Contraseñas con `bcrypt` o `argon2`; nunca en texto plano.
- JWT firmado, con expiración.
- Consultas parametrizadas; nunca concatenar SQL con entrada del usuario.
- CORS con lista blanca; no `*` en producción.
- `.env` fuera del repositorio; `.env.example` documenta las variables.
- Errores sin trazas internas ni datos sensibles.

### Datos

- Usa datos de prueba ficticios. No cargues datos personales reales.
- Incluye un `seed` o script para crear el administrador y 5 registros de ejemplo.

### Entregas y retardos

- Entrega en la fecha del hito, con el tag correspondiente.
- Retardo: **−10% por día**, hasta 3 días. Después de 3 días, el hito se califica sobre 70% máximo y debe recuperarse antes del siguiente.
- No se aceptan entregas después de la sesión 40.
- Los checkpoints no tienen calificación directa, pero su ausencia activa el límite del 70%.

### Demostración

- 5 minutos por equipo; hablan ambos.
- Guion: salud, login, crear, listar, eliminar, error controlado y cierre.
- Si la nube falla, se acepta un video pregrabado de 5 minutos con el mismo guion; el docente puede pedir una demo en vivo después.

---

## 9. Evaluación

El proyecto vale **30% de la calificación final**, distribuido en cuatro hitos de **7.5%** cada uno.

### Rúbrica de cada hito

| Criterio | Peso | Qué se evalúa |
| :--- | :---: | :--- |
| **Funcionalidad** | 40% | El contrato del hito funciona; casos felices y errores. |
| **Seguridad y calidad** | 20% | Validación, auth, roles, consultas seguras, código legible. |
| **Pruebas** | 20% | Suite ejecutable, casos significativos, resultados en verde. |
| **Documentación y OpenAPI** | 10% | README, OpenAPI, decisiones y evidencias. |
| **Demo y defensa** | 10% | Explicación clara, demo reproducible, respuestas individuales. |

La rúbrica detallada con niveles está en [`docs/rubrica.md`](docs/rubrica.md).

### Factor individual

La nota del equipo se ajusta con un factor de **0.7 a 1.1**:

- **1.0:** participó de forma equilibrada, tiene commits y explica el proyecto.
- **0.9:** participó, pero con menor evidencia o dificultad para explicar alguna parte.
- **0.7–0.8:** aporte insuficiente o ausencia en un hito; requiere recuperación.
- **1.1:** liderazgo técnico excepcional, evidencia clara y apoyo verificable al compañero.

El factor se comunica antes de cerrar el hito. La nota individual no puede superar 100.

### Mínimos para acreditar el proyecto

- Hito 4 entregado y aprobado.
- Autenticación y base de datos funcionando.
- Suite de pruebas en verde.
- Ninguna vulnerabilidad crítica (contraseñas en texto plano, SQL concatenado, secreto en el repositorio).
- Los hitos 1–3 entregados y con retroalimentación atendida.

---

## 10. Entregables y etiquetas

Cada hito debe incluir:

- [ ] Código en `proyecto_integrador/`.
- [ ] README actualizado con instrucciones de ejecución.
- [ ] OpenAPI accesible en `/docs` o `openapi.json`.
- [ ] Suite de pruebas ejecutable y en verde.
- [ ] `docs/decisiones.md` con una página del hito.
- [ ] Tag `v0.X-hitoX` en GitHub.
- [ ] Checkpoint o demo cuando corresponda.

### Convención de tags

```bash
git tag v0.1-hito1
git tag v0.2-hito2
git tag v0.3-hito3
git tag v1.0-final
git push origin --tags
```

No se califica un hito sin tag y sin pruebas.

---

## 11. Calendario

| Sesión | Entregable | Qué debe estar listo |
| :---: | :--- | :--- |
| 4 | Propuesta | Equipo, dominio, entidades, endpoints, riesgos |
| 10 | **Hito 1** | Mock API, OpenAPI, validación, 8 pruebas |
| 15 | Checkpoint 1 | Estado real del Hito 1 y plan del Hito 2 |
| 20 | **Hito 2** | Capas, auth, JWT, RBAC, 10 pruebas |
| 25 | Checkpoint 2 | Estado del Hito 2 y plan del Hito 3 |
| 30 | **Hito 3** | Persistencia, migración, OWASP, 12 pruebas |
| 35 | Checkpoint 3 | Ensayo del Hito 4 y riesgos de cierre |
| 40 | **Hito 4** | SPA, Dockerfile, E2E y demo de 5 minutos |

Si el calendario del curso se recorre, el docente comunicará las nuevas fechas con al menos una semana de anticipación.

---

## 12. Plantillas

- [`docs/00-propuesta.md`](docs/00-propuesta.md) — propuesta inicial.
- [`docs/checkpoint.md`](docs/checkpoint.md) — avance de una página.
- [`docs/decisiones.md`](docs/decisiones.md) — registro de decisiones por hito.
- [`docs/equipo.md`](docs/equipo.md) — acuerdo de equipo y roles.
- [`docs/hito-1.md`](docs/hito-1.md) — checklist del Hito 1.
- [`docs/hito-2.md`](docs/hito-2.md) — checklist del Hito 2.
- [`docs/hito-3.md`](docs/hito-3.md) — checklist del Hito 3.
- [`docs/hito-4.md`](docs/hito-4.md) — checklist del Hito 4.
- [`docs/rubrica.md`](docs/rubrica.md) — rúbrica detallada.
- [`docs/faq.md`](docs/faq.md) — dudas frecuentes.
- [`README.md`](README.md) — plantilla del README del equipo.

---

## 13. Checklist de arranque

Antes de la sesión 4:

- [ ] Tengo equipo de 2 y acordamos roles.
- [ ] Elegimos un dominio del catálogo.
- [ ] Creamos el repositorio y la carpeta `proyecto_integrador/`.
- [ ] Firmamos `docs/equipo.md`.
- [ ] Completamos `docs/00-propuesta.md`.
- [ ] Definimos los 8–10 endpoints y las 8 pruebas del Hito 1.
- [ ] Revisamos el calendario y los criterios de evaluación.
- [ ] Identificamos los tres riesgos principales y su mitigación.

---

**Siguiente paso:** abre [`docs/00-propuesta.md`](docs/00-propuesta.md) y completa la propuesta con tu compañero.
