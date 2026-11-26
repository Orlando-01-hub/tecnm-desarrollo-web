# Preguntas frecuentes del Proyecto Integrador

## Alcance

**¿Puedo usar React, Vue o Angular?**  
No. La SPA se hace con HTML, CSS y JavaScript vanilla para evaluar los fundamentos del curso.

**¿Puedo usar PostgreSQL en lugar de SQLite?**  
Sí. SQLite es el valor por defecto para desarrollo; PostgreSQL es aceptable si el equipo lo documenta y lo hace funcionar en la demo.

**¿Puedo usar PHP en lugar de FastAPI?**  
Sí, con aprobación previa del docente. El contrato, la seguridad y las pruebas son los mismos; el docente no ofrece la misma batería de ejemplos que para FastAPI.

**¿Puedo agregar una tercera entidad?**  
No, salvo autorización escrita antes del Hito 1. El alcance es 2 entidades de negocio + usuario.

**¿Puedo agregar pagos, chat o notificaciones?**  
No. Están fuera del alcance del curso y no suman puntos.

**¿Puedo cambiar de dominio después del Hito 1?**  
Solo con una solicitud escrita y aprobación del docente. Cambiar de dominio implica rehacer el contrato y las pruebas.

## Equipo

**¿Qué pasa si mi compañero no trabaja?**  
Documenta la conversación, avisa al docente antes del checkpoint y continúa. El abandono no debe impedir que termines; el factor individual se ajusta.

**¿La nota es la misma para los dos?**  
La base es del equipo, pero se ajusta con el factor individual (0.7–1.1) según commits, defensa y evaluación de pares.

**¿Puedo trabajar solo?**  
Solo con autorización del docente. La modalidad base es equipos de 2.

## Entregas

**¿Qué significa “tag”?**  
Una etiqueta de Git que marca el commit exacto de la entrega:

```bash
git tag v0.1-hito1
git push origin --tags
```

**¿Puedo entregar tarde?**  
Sí, con −10% por día, hasta 3 días. Después de 3 días, el hito se califica sobre 70% máximo y debe recuperarse antes del siguiente. No se aceptan entregas después de la sesión 40.

**¿Qué pasa si no entrego un checkpoint?**  
El hito correspondiente se limita a 70% hasta que regularices. El checkpoint no suma nota, pero es un filtro de avance.

**¿Puedo reutilizar código de los laboratorios?**  
Puedes reutilizar **patrones** (por ejemplo, la estructura de un router o un DTO), pero no el dominio ni el código literal de la práctica. Cita lo que reutilices.

## Seguridad y IA

**¿Puedo usar IA para programar?**  
Sí, como copiloto. Debes entender el código, probarlo y documentar en `docs/decisiones.md` qué consultaste, qué aceptaste y qué corregiste.

**¿Puedo guardar el token en `localStorage`?**  
Puedes, pero debes explicar el riesgo de XSS y por qué elegiste esa opción. Para el curso, `sessionStorage` o memoria son alternativas más simples de justificar.

**¿Qué pasa si subo `.env` por accidente?**  
Rótalo de inmediato, elimínalo del historial si es necesario y avisa al docente. Un secreto expuesto es una vulnerabilidad crítica.

## Demo y evaluación

**¿La URL pública es obligatoria?**  
No. El Hito 4 exige una demo reproducible (Codespaces, Render o local) y un Dockerfile. La URL pública es un bonus.

**¿Qué pasa si la nube falla el día de la demo?**  
Puedes usar un video pregrabado de 5 minutos con el mismo guion. El docente puede pedir una demo en vivo después.

**¿Cómo se calcula mi nota individual?**  
Nota del equipo × factor individual (0.7–1.1). El factor se comunica antes de cerrar el hito y se basa en commits, defensa y evaluación de pares.

**¿Puedo apelar una calificación?**  
Sí, con evidencias, dentro de los 3 días hábiles posteriores a la retroalimentación.
