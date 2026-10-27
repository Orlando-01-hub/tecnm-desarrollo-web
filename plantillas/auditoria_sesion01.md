# Bitácora de Requerimientos y Auditoría IA — Sesión 01
**Materia:** Desarrollo de Aplicaciones Web (DAB-2201)  
**Tema:** Encuadre, Diagnóstico HTTP y Servidor en Colab  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Orlando Tec ciau]  
**Matrícula:** [23531101]  
**Track:** [ Python/FastAPI | PHP 8+ ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Procesamiento manual de JSON de petición sin IA  
*Describe qué dificultad encontraste al procesar la petición HTTP o validar campos a mano:*
> Dificultad: No identificaba que las rutas debían empezar con /api/ y que las respuestas debían ser idénticas, sin agregar nada. Me costó igualar nombres y valores exactos. 

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal (Contrato OpenAPI / DTO)
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** |FastAPI, rutas /api/, respuestas JSON, servidor estable |
| **Rol (R)** | Servicio de estado y eco con códigos HTTP correctos |
| **Especificación (E)** | GET /api/estado → 200 · POST /api/eco → 200 · JSON malo → 422 · Método mal → 405 |
| **Output (O)** | Respuestas exactas, modelo Pydantic, sin rutas extra |

### 2.2 Prompt Estructurado
```text
[Crea un servidor FastAPI con estas rutas y respuestas exactas: /api/estado devuelve {"servidor":"ok","curso":"DAB-2201"}; /api/eco recibe mensaje y lo devuelve con recibido:true. Códigos 422 y 405 automáticos. Sin rutas extra.]
```

---

## 3. Código Devuelto por el Modelo
```python
# %%writefile app.py
from fastapi import FastAPI, Request
from fastapi.responses import JSONResponse
from pydantic import BaseModel
from typing import Optional

# Inicialización de la aplicación
app = FastAPI(title="Tu servidor responderá exactamente así")

# Modelo de datos para el eco
class MensajeEco(BaseModel):
    mensaje: str

# ✅ Ruta requerida: /api/estado
@app.get("/api/estado")
def estado():
    return {
        "servidor": "ok",
        "curso": "DAB-2201"
    }

# ✅ Ruta requerida: /api/eco - método POST
@app.post("/api/eco")
def eco(datos: MensajeEco):
    return {
        "mensaje": datos.mensaje,
        "recibido": True
    }

# 🛡️ Manejo explícito de errores de formato JSON inválido
@app.exception_handler(422)
async def error_json_invalido(request: Request, exc):
    return JSONResponse(
        status_code=422,
        content={"error": "Datos inválidos o formato incorrecto"}
    )
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness HTTP)
*Pruebas ejecutadas:* Pruebas de códigos de estado HTTP 200, 400 y 422

* [ x ] Respuestas semánticas correctas (`200 OK`, `201 Created`, `400 Bad Request`, `422 Unprocessable`).
* [ x ] No existen respuestas HTTP `500 Internal Server Error` no capturadas.
* [ x ] Encabezados `Content-Type: application/json` correctamente configurados.

### Reporte Crítico
*¿Qué código de estado HTTP o falla de seguridad omitió la IA y cómo lo corregiste?*
> R.- La IA omitió usar el prefijo /api/ en las rutas y devolvió claves con nombres distintos. No incluyó el código de curso esperado. Corregí las rutas y las respuestas para que coincidieran exactamente con lo solicitado. Los códigos 422 y 405 FastAPI los gestiona automáticamente, no requirieron ajuste manual.

---
**Firma digital:** [Orlando Tec Ciau]
