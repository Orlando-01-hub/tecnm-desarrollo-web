# Bitácora de Requerimientos y Auditoría IA — Sesión 19
**Materia:** Desarrollo de Aplicaciones Web (DAB-2201)  
**Tema:** Sanitización de Entradas, Prevención de XSS e Inyecciones  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Orlando Tec Ciau]  
**Matrícula:** [23531101]  
**Track:** [ Python/FastAPI | PHP 8+ ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Limpieza manual de HTML y caracteres especiales  
*Describe qué dificultad encontraste al procesar la petición HTTP o validar campos a mano:*
> La dificultad fue identificar y convertir correctamente las etiquetas HTML peligrosas sin modificar los comentarios normales. También fue necesario evitar que el contenido pudiera ejecutarse como código.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal (Contrato OpenAPI / DTO)
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | Python con FastAPI en Google Colab. |
| **Rol (R)** | Desarrollador backend especializado en APIs y seguridad web. |
| **Especificación (E)** | Crear y consultar comentarios, validar el texto y escapar HTML para prevenir ataques XSS. |
| **Output (O)** | Archivo app.py funcional, modelos Pydantic y respuestas JSON con códigos HTTP correctos. |

### 2.2 Prompt Estructurado
```text
[Implementa una API de comentarios con POST /api/comentarios y GET /api/comentarios. Utiliza Pydantic para validar las entradas y html.escape() para convertir las etiquetas HTML peligrosas en texto seguro antes de guardarlas. Los comentarios normales deben conservarse sin cambios. POST debe responder HTTP 201 y GET HTTP 200. Entrega el código completo en app.py.]
```

---

## 3. Código Devuelto por el Modelo
```python
#  la función html.escape() para neutralizar las etiquetas HTML. Se implementaron dos rutas: una para registrar comentarios y otra para consultarlos. Los comentarios se almacenan escapados para evitar contenido HTML ejecutable.
```
---

## 4. Fase 3: El Auditor (Resultados del Test Harness HTTP)
*Pruebas ejecutadas:* Pruebas de inyección de scripts en payloads

* [ x ] Comentario normal guardado correctamente: HTTP 201.
* [ x ] Script neutralizado correctamente: HTTP 201.
* [ x ] Listado de comentarios seguro: HTTP 200.
* [ x ] Respuestas en formato JSON.
* [ x ] Se evita devolver etiquetas HTML ejecutables en el contenido almacenado.

### Reporte Crítico
*¿Qué código de estado HTTP o falla de seguridad omitió la IA y cómo lo corregiste?*
> La principal falla de seguridad que debía prevenirse era permitir que las etiquetas HTML se interpretaran como código. Se corrigió utilizando html.escape() antes de guardar los comentarios. También se utilizó Pydantic para validar los datos recibidos y evitar comentarios vacíos.

---
**Firma digital:** [Orlando Tec Ciau]
