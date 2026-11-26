# Rúbrica del Proyecto Integrador

**Curso:** Desarrollo de Aplicaciones Web (DAB-2201)  
**Peso total:** 30% de la calificación final  
**Distribución:** 4 hitos × 7.5% = 30%

---

## 1. Criterios de cada hito

Cada hito se califica sobre 100 puntos y luego se convierte a 7.5% de la nota final.

| Criterio | Peso | Qué se evalúa |
| :--- | :---: | :--- |
| **Funcionalidad** | 40% | El contrato del hito funciona: casos felices, errores y límites. |
| **Seguridad y calidad** | 20% | Validación, auth, roles, consultas seguras y código legible. |
| **Pruebas** | 20% | Suite ejecutable, casos significativos y resultados en verde. |
| **Documentación y OpenAPI** | 10% | README, OpenAPI, decisiones y evidencias. |
| **Demo y defensa** | 10% | Explicación clara, demo reproducible y respuestas individuales. |

## 2. Niveles de desempeño

| Nivel | Puntos | Descripción general |
| :--- | :---: | :--- |
| **Excelente** | 90–100 | Cumple todo el contrato, pruebas sólidas, seguridad explícita y demo impecable. |
| **Bien** | 80–89 | Cumple el contrato con detalles menores; pruebas y documentación suficientes. |
| **Suficiente** | 70–79 | Cumple lo esencial; faltan casos límite, pruebas o claridad en la demo. |
| **Insuficiente** | 0–69 | No cumple el contrato, hay fallas de seguridad o no puede demostrarse. |

## 3. Rúbrica por criterio

### Funcionalidad (40%)

| Nivel | Descriptor |
| :--- | :--- |
| **90–100** | Todos los casos del hito funcionan, incluidos errores y límites; el contrato coincide con OpenAPI. |
| **80–89** | Todos los casos felices funcionan; uno o dos errores menores. |
| **70–79** | El flujo principal funciona; faltan casos límite o algún código HTTP es incorrecto. |
| **0–69** | El flujo principal falla, hay 500 no controlados o el contrato no se cumple. |

### Seguridad y calidad (20%)

| Nivel | Descriptor |
| :--- | :--- |
| **90–100** | Hash, JWT, roles, consultas parametrizadas y CORS correctos; código separado y legible. |
| **80–89** | Controles presentes con un detalle menor (mensaje, validación o separación). |
| **70–79** | Controles básicos, pero con deuda clara (validación débil, capas mezcladas). |
| **0–69** | Secreto en el repositorio, contraseña en texto plano, SQL concatenado o autorización ausente. |

### Pruebas (20%)

| Nivel | Descriptor |
| :--- | :--- |
| **90–100** | Suite ejecutable con un comando, casos felices y de error, resultados reproducibles. |
| **80–89** | Suite completa con algún caso poco significativo. |
| **70–79** | Pocas pruebas o solo casos felices; requieren pasos manuales. |
| **0–69** | No hay pruebas ejecutables o no pasan. |

### Documentación y OpenAPI (10%)

| Nivel | Descriptor |
| :--- | :--- |
| **90–100** | README reproducible, OpenAPI completo, decisiones y evidencias claras. |
| **80–89** | README y OpenAPI suficientes; faltan detalles menores. |
| **70–79** | Documentación mínima; cuesta ejecutar el proyecto desde cero. |
| **0–69** | Sin README útil, sin OpenAPI o documentación contradictoria. |

### Demo y defensa (10%)

| Nivel | Descriptor |
| :--- | :--- |
| **90–100** | Demo de 5 minutos, reproducible, ambos explican y responden con seguridad. |
| **80–89** | Demo clara con una explicación individual débil. |
| **70–79** | Demo incompleta o un integrante no puede explicar su parte. |
| **0–69** | No hay demo, no corresponde al tag o no pueden explicar el proyecto. |

## 4. Resumen por hito

| Hito | Funcionalidad | Seguridad | Pruebas | Docs | Demo | Total | Nota final |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 1. Mock API | 40 | 20 | 20 | 10 | 10 | 100 | 7.5% |
| 2. Auth y capas | 40 | 20 | 20 | 10 | 10 | 100 | 7.5% |
| 3. Persistencia y OWASP | 40 | 20 | 20 | 10 | 10 | 100 | 7.5% |
| 4. Producto | 40 | 20 | 20 | 10 | 10 | 100 | 7.5% |

## 5. Factor individual

La nota del equipo se multiplica por un factor de **0.7 a 1.1**:

| Factor | Situación |
| :---: | :--- |
| **1.1** | Liderazgo técnico excepcional, evidencia clara y apoyo verificable al compañero. |
| **1.0** | Participación equilibrada, commits en todos los hitos y explica todo el proyecto. |
| **0.9** | Participa, pero con menos evidencia o dificultad para explicar alguna parte. |
| **0.7–0.8** | Aporte insuficiente, ausencia en un hito o falta de comprensión; requiere recuperación. |

La nota individual no puede superar 100. El factor se comunica antes de cerrar el hito.

## 6. Regla de checkpoints

- Todos los equipos entregan los checkpoints de las sesiones 4, 15, 25 y 35.
- Si falta un checkpoint, el hito correspondiente se **limita a 70%** hasta que se regularice.
- El checkpoint no suma nota extra; es un filtro de avance.
- Un checkpoint entregado tarde no extiende la fecha del hito.

## 7. Mínimos para acreditar el proyecto

- Hito 4 entregado y aprobado.
- Autenticación y base de datos funcionando.
- Suite de pruebas en verde.
- Ninguna vulnerabilidad crítica: contraseña en texto plano, SQL concatenado o secreto en el repositorio.
- Hitos 1–3 entregados y con retroalimentación atendida.

## 8. Ejemplo de cálculo

Equipo con estas notas:

| Hito | Puntos | Aporte |
| :--- | :---: | :---: |
| 1 | 85 | 6.375% |
| 2 | 90 | 6.750% |
| 3 | 80 | 6.000% |
| 4 | 95 | 7.125% |
| **Total** | | **26.25%** |

Si el factor individual de un integrante es 1.0, recibe 26.25% del 30%. Si es 0.9, recibe 23.625%. Si es 1.1, recibe 28.875% (sin superar 30%).

## 9. Revisión y apelación

- El docente comparte la rúbrica antes de cada hito.
- El equipo puede solicitar una revisión con evidencias dentro de los 3 días hábiles posteriores a la retroalimentación.
- La revisión no cambia el factor individual por evaluación de pares, salvo error documentado.
