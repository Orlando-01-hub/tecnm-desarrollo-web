# Acuerdo de equipo

**Proyecto:** [Nombre del proyecto]  
**Curso:** Desarrollo de Aplicaciones Web (DAB-2201)  
**Dominio:** [Tablero → Tarea | Espacio → Reserva | Categoría → Gasto]  
**Repositorio:** [enlace]  
**Fecha:** [DD/MM/AAAA]

> Este acuerdo se firma antes del Hito 1 y se conserva en `docs/equipo.md`.

---

## 1. Integrantes y roles

| Integrante | Rol principal | Rol secundario | Correo / Teams |
| :--- | :--- | :--- | :--- |
| [Nombre 1] | Backend y datos | Pruebas / documentación | [ ] |
| [Nombre 2] | Frontend y calidad | Backend / integración | [ ] |

**Regla:** ambos deben poder explicar todo el proyecto. El rol secundario existe para evitar dependencias de una sola persona.

## 2. Comunicación

- **Canal principal:** [Teams / WhatsApp / Discord]
- **Tiempo de respuesta esperado:** [por ejemplo, menos de 24 horas en días hábiles]
- **Reuniones:** [día y hora, duración]
- **Registro de acuerdos:** [dónde se anotan: canal, issues, `docs/decisiones.md`]

## 3. Trabajo en Git

- **Rama principal:** `main` (siempre ejecutable).
- **Ramas de trabajo:** `feat/[nombre]`, `fix/[nombre]`.
- **Commits:** mensajes claros, en imperativo y con alcance. Ejemplo: `feat: agrega endpoint de tareas`.
- **Revisión:** ningún cambio se integra a `main` sin que el otro integrante lo haya probado.
- **Tags:** `v0.1-hito1`, `v0.2-hito2`, `v0.3-hito3`, `v1.0-final`.
- **Prohibido:** subir `.env`, contraseñas, bases de datos locales o archivos de más de 10 MB.

## 4. Definición de “terminado”

Un cambio está terminado cuando:

- [ ] Funciona en local y las pruebas pasan.
- [ ] El contrato HTTP coincide con el README/OpenAPI.
- [ ] No hay secretos ni datos personales en el repositorio.
- [ ] El otro integrante lo revisó.
- [ ] El commit está en GitHub.

## 5. Reparto de trabajo por hito

| Hito | Responsable A | Responsable B | Apoyo mutuo |
| :--- | :--- | :--- | :--- |
| 1. Mock API | [ ] | [ ] | [ ] |
| 2. Auth y capas | [ ] | [ ] | [ ] |
| 3. Persistencia y OWASP | [ ] | [ ] | [ ] |
| 4. SPA y demo | [ ] | [ ] | [ ] |

Cada persona debe tener al menos **un commit significativo por hito**, aunque el reparto no sea 50/50.

## 6. Conflictos

1. Hablar directamente y documentar el acuerdo en el canal.
2. Si no se resuelve en 48 horas, avisar al docente antes del checkpoint.
3. El docente puede pedir una reunión, ajustar roles o aplicar un factor individual.
4. El abandono de un integrante no debe impedir que el otro termine el proyecto.

## 7. Evaluación de pares

Se completa en el Hito 2 y en el Hito 4. Escala de 1 a 5:

| Criterio | [Nombre 1] | [Nombre 2] |
| :--- | :---: | :---: |
| Cumplió sus tareas a tiempo | [ ] | [ ] |
| Comunicación y disponibilidad | [ ] | [ ] |
| Calidad del trabajo | [ ] | [ ] |
| Comprensión del proyecto completo | [ ] | [ ] |
| Apoyo al compañero | [ ] | [ ] |

**Comentario confidencial:** [opcional]

La evaluación de pares es insumo para el factor individual; no se publica.

## 8. Compromisos

- [ ] Leímos la guía oficial y aceptamos el alcance.
- [ ] Conocemos las fechas de los hitos y checkpoints.
- [ ] Aceptamos la política de IA y autoría.
- [ ] Aceptamos el proceso de conflictos y la evaluación individual.

**Firmas:** [Nombre 1] · [Nombre 2]
