# Bitácora de Requerimientos y Auditoría IA — Sesión 18
**Materia:** Desarrollo de Aplicaciones Web (DAB-2201)  
**Tema:** Rate Limiting, Throttling y Prevención de Abuso  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Orlando Tec Ciau]  
**Matrícula:** [23531101]  
**Track:** [ Python/FastAPI | PHP 8+ ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Contador manual de peticiones por IP en memoria  
*Describe qué dificultad encontraste al procesar la petición HTTP o validar campos a mano:*
> La dificultad fue controlar las peticiones de cada cliente y calcular correctamente el tiempo de espera. También fue necesario evitar que un cliente afectara el límite de los demás.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal (Contrato OpenAPI / DTO)
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | Python con FastAPI en Google Colab. |
| **Rol (R)** | Desarrollador backend especializado en APIs y seguridad web. |
| **Especificación (E)** | Permitir tres peticiones por IP cada 60 segundos y responder 429 en la cuarta, incluyendo Retry-After. |
| **Output (O)** | Archivo app.py funcional, respuestas JSON y pruebas HTTP correctas. |

### 2.2 Prompt Estructurado
```text
[Actúa como desarrollador backend especializado en FastAPI y seguridad web. Implementa un límite de tres peticiones por cliente en una ventana de 60 segundos para el endpoint GET /api/recurso. Las primeras tres peticiones deben responder HTTP 200 y la cuarta HTTP 429, incluyendo el encabezado Retry-After. Utiliza contadores independientes por IP, almacenamiento en memoria y respuestas JSON. El código debe cumplir los criterios de verificación.

]
```

---

## 3. Código Devuelto por el Modelo
```python
# Se utilizó Python con FastAPI para implementar el control de peticiones mediante un contador independiente por dirección IP. Se agregó una ventana de 60 segundos, control de concurrencia y una respuesta HTTP 429 con el encabezado Retry-After cuando se supera el límite.
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness HTTP)
*Pruebas ejecutadas:* Pruebas de ráfaga masiva y bloqueo temporal (HTTP 429)

* [ x ] Primera petición: HTTP 200 OK.
* [ x ] Segunda petición: HTTP 200 OK.
* [ x ] Tercera petición: HTTP 200 OK.
* [ x ] Cuarta petición: HTTP 429 Too Many Requests.
* [ x ] Se incluye el encabezado Retry-After.
* Las respuestas utilizan formato JSON.

### Reporte Crítico
*¿Qué código de estado HTTP o falla de seguridad omitió la IA y cómo lo corregiste?*
> La verificación permitió comprobar que era necesario controlar correctamente la cuarta petición y agregar el encabezado Retry-After. Se corrigió mediante un contador por IP y el cálculo del tiempo restante de la ventana de 60 segundos.

---
**Firma digital:** [Orlando Tec Ciau]
