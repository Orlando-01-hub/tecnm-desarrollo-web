# Bitácora de Requerimientos y Auditoría IA — Sesión 09
**Materia:** Desarrollo de Aplicaciones Web (DAB-2201)  
**Tema:** Middleware Web, Intercepción y Logging de Transacciones  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Orlando Tec Ciau]  
**Matrícula:** [23531101]  
**Track:** [ Python/FastAPI | PHP 8+ ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Middleware manual de tiempo de respuesta (X-Process-Time)  
*Describe qué dificultad encontraste al procesar la petición HTTP o validar campos a mano:*
> Tuve dificultad para medir el tiempo de cada petición y agregar los encabezados sin repetir código en cada endpoint. También necesitaba asegurarme de que funcionara cuando una ruta no existiera.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal (Contrato OpenAPI / DTO)
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | Python 3.13, FastAPI y Google Colab. |
| **Rol (R)** | Desarrollador experto en middleware y APIs REST. |
| **Especificación (E)** | Crear un middleware que mida el tiempo y agregue los encabezados X-Process-Time y X-Request-ID, conservando el identificador enviado por el cliente. |
| **Output (O)** | Código completo de app.py, funcional y con respuestas HTTP correctas. |

### 2.2 Prompt Estructurado
```text
[Actúa como desarrollador experto en Python y FastAPI.
Crea un middleware que mida el tiempo de cada petición
y agregue los encabezados X-Process-Time y X-Request-ID.

Si el cliente envía un identificador, debe conservarse.
Si no lo envía, genera uno automáticamente.

Debe funcionar en todas las respuestas, incluyendo
los errores 404. Entrega el código completo y explicado.]
```

---

## 3. Código Devuelto por el Modelo
```python
# %%writefile app.py

from fastapi import FastAPI, Request
import time
import uuid

app = FastAPI(title="Biblioteca API con Middleware")


# Middleware para medir el tiempo y generar identificador
@app.middleware("http")
async def registrar_peticion(request: Request, call_next):

    inicio = time.perf_counter()

    # Conservar el ID enviado por el cliente o generar uno nuevo
    request_id = request.headers.get("X-Request-ID")

    if not request_id:
        request_id = str(uuid.uuid4())

    # Procesar la petición
    response = await call_next(request)

    # Calcular tiempo de procesamiento
    tiempo = time.perf_counter() - inicio

    # Agregar encabezados a todas las respuestas
    response.headers["X-Process-Time"] = f"{tiempo:.4f}"
    response.headers["X-Request-ID"] = request_id

    return response


# Endpoint principal
@app.get("/api/libros")
async def listar_libros():
    return {"libros": []}

```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness HTTP)
*Pruebas ejecutadas:* Pruebas de inyección de encabezados de rastreo

* [ x ] Respuesta HTTP 200 con los encabezados de tiempo y rastreo.
* [ x ] Conservación del identificador enviado por el cliente.
* [ x ] Middleware funcionando también en respuestas 404.

### Reporte Crítico
*¿Qué código de estado HTTP o falla de seguridad omitió la IA y cómo lo corregiste?*
> Inicialmente debía comprobar que el middleware también funcionara en las respuestas 404. Lo solucioné colocando el procesamiento de las peticiones en un middleware global para que los encabezados se agregaran automáticamente a todas las respuestas.

---
**Firma digital:** [Orlando Tec Ciau]
