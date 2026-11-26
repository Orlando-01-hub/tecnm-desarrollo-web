# Propuesta del Proyecto Integrador

**Curso:** Desarrollo de Aplicaciones Web (DAB-2201)  
**Equipo:** [Nombre 1] y [Nombre 2]  
**Dominio elegido:** [Tablero → Tarea | Espacio → Reserva | Categoría → Gasto]  
**Repositorio:** [enlace]  
**Fecha:** [DD/MM/AAAA]

> Entrega: **sesión 4**, antes de la clase. Extensión máxima: 2 páginas.

---

## 1. Problema y usuario

[¿Qué problema resuelve? ¿Quién lo usaría? ¿Por qué es útil? 3–5 líneas.]

## 2. Entidades

### Entidad principal: [nombre]

| Campo | Tipo | Obligatorio | Regla |
| :--- | :--- | :---: | :--- |
| id | entero | sí | generado por el servidor |
| [campo] | [tipo] | [sí/no] | [longitud, rango, etc.] |
| usuario_id | entero | sí | dueño del registro |

### Entidad secundaria: [nombre]

| Campo | Tipo | Obligatorio | Regla |
| :--- | :--- | :---: | :--- |
| id | entero | sí | generado por el servidor |
| [padre]_id | entero | sí | llave foránea |
| [campo] | [tipo] | [sí/no] | [regla] |

### Usuario

| Campo | Tipo | Regla |
| :--- | :--- | :--- |
| id | entero | generado por el servidor |
| usuario | texto | único, 3–30 caracteres |
| password_hash | texto | nunca se devuelve |
| rol | texto | `user` o `admin` |

**Relación:** [Entidad principal] 1 → N [Entidad secundaria].

## 3. Endpoints

Máximo 10. Marca con `*` los que existen desde el Hito 1.

| # | Método | Ruta | Qué hace | Auth | Hito |
| :---: | :--- | :--- | :--- | :---: | :---: |
| 1 | POST | `/api/registro` | Crea usuario | No | 2 |
| 2 | POST | `/api/token` | Login y JWT | No | 2 |
| 3 | GET | `/api/[padres]` | Lista los míos | Sí | 1* |
| 4 | POST | `/api/[padres]` | Crea principal | Sí | 1* |
| 5 | GET | `/api/[padres]/{id}/[hijos]` | Lista hijos | Sí | 1* |
| 6 | POST | `/api/[padres]/{id}/[hijos]` | Crea hijo | Sí | 1* |
| 7 | GET | `/api/[hijos]/{id}` | Consulta hijo | Sí | 1* |
| 8 | DELETE | `/api/[hijos]/{id}` | Elimina hijo | Sí | 1* |
| 9 | GET | `/health` | Estado | No | 1* |
| 10 | [opcional] | [ruta] | [filtro o total] | Sí | 3 |

> En el Hito 1 se usa un usuario fijo; la autenticación real llega en el Hito 2.

## 4. Reglas de negocio

1. [Regla 1: por ejemplo, el estado solo puede ser pendiente, en_progreso o hecha.]
2. [Regla 2: por ejemplo, no se permiten reservas solapadas.]
3. [Regla 3: por ejemplo, el monto debe ser mayor que cero.]
4. [Regla 4: solo el dueño o un admin puede modificar/eliminar.]
5. [Regla 5: filtro o total calculado.]

## 5. Casos de prueba del Hito 1

| # | Caso | Entrada | Resultado esperado |
| :---: | :--- | :--- | :--- |
| 1 | Crear principal válido | [JSON] | 201 |
| 2 | Crear principal inválido | [JSON] | 422 |
| 3 | Listar principales | — | 200 |
| 4 | Crear hijo válido | [JSON] | 201 |
| 5 | Hijo de un padre inexistente | [id] | 404 |
| 6 | Eliminar hijo | [id] | 204 |
| 7 | Obtener hijo eliminado | [id] | 404 |
| 8 | Método no permitido | [ruta] | 405 |

## 6. Riesgos y mitigación

| Riesgo | Probabilidad | Impacto | Mitigación |
| :--- | :---: | :---: | :--- |
| [Riesgo 1] | [alta/media/baja] | [alto/medio/bajo] | [acción] |
| [Riesgo 2] | [ ] | [ ] | [acción] |
| [Riesgo 3] | [ ] | [ ] | [acción] |

## 7. Plan por hito

| Hito | Qué tendremos listo | Responsable principal |
| :--- | :--- | :--- |
| 1 | [Mock API, OpenAPI, pruebas] | [ ] |
| 2 | [Capas, auth, RBAC] | [ ] |
| 3 | [Persistencia, migración, OWASP] | [ ] |
| 4 | [SPA, Dockerfile, demo] | [ ] |

## 8. Roles y compromiso

| Integrante | Rol principal | Horas estimadas por semana | Commits por hito |
| :--- | :--- | :---: | :---: |
| [Nombre 1] | [Backend/datos] | [ ] | [ ] |
| [Nombre 2] | [Frontend/calidad] | [ ] | [ ] |

- [ ] Ambos leyeron la guía oficial.
- [ ] Ambos aceptan el alcance y las reglas.
- [ ] Ambos se comprometen a tener al menos un commit por hito.

## 9. Declaración de alcance

Confirmamos que el proyecto:

- [ ] Usa **2 entidades de negocio + usuario**.
- [ ] Tiene **8–10 endpoints**.
- [ ] No incluye pagos, chat, tiempo real, IA, apps móviles ni APIs externas.
- [ ] Usa el stack permitido.
- [ ] No copia el dominio ni el código de los laboratorios.

**Firmas:** [Nombre 1] · [Nombre 2]
