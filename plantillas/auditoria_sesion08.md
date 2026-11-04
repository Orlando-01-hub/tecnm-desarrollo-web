# Bitácora de Requerimientos y Auditoría IA — Sesión 08
**Materia:** Desarrollo de Aplicaciones Web (DAB-2201)  
**Tema:** Respuestas Semánticas y Excepciones HTTP Personalizadas  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Orlando Tec Ciau]  
**Matrícula:** [23531101]  
**Track:** [ Python/FastAPI | PHP 8+ ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Lanzamiento manual de excepciones estructuradas  
*Describe qué dificultad encontraste al procesar la petición HTTP o validar campos a mano:*
> Tuve dificultad para controlar los errores de validación y responder con códigos HTTP correctos. También me costó identificar por qué un libro válido respondía 422 cuando faltaban algunos campos.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal (Contrato OpenAPI / DTO)
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | FastAPI 0.110+ (Python 3.10+) o PHP 8.2 con servidor embebido |
| **Rol (R)** | Arquitecto de software senior especialista en APIs RESTful y Seguridad Web |
| **Especificación (E)** | Manejador global de HTTPException y JSON response |
| **Output (O)** | Solo código modular, esquemas Pydantic/DTO y códigos HTTP semánticos |

### 2.2 Prompt Estructurado
```text
[Actúa como desarrollador experto en Python y FastAPI.
Ayúdame a corregir una API de biblioteca que permita
consultar y registrar libros.

Implementa excepciones personalizadas para libros
inexistentes y un manejador global para errores
de validación.

Utiliza respuestas JSON y códigos HTTP correctos:
200 para consultas, 201 para registros, 404 para
libros inexistentes y 422 para datos inválidos.

Los campos precio y disponible deben tener valores
predeterminados si no se envían.

Entrega el código completo, modular y funcional.]
```

---

## 3. Código Devuelto por el Modelo
```python
# %%writefile app.py

from fastapi import FastAPI, Request
from fastapi.responses import JSONResponse
from fastapi.exceptions import RequestValidationError
from pydantic import BaseModel, Field

app = FastAPI(title="Biblioteca API")

# Datos iniciales
libros = {
    1: {
        "id": 1,
        "titulo": "Rayuela",
        "anio": 1963,
        "precio": 250.50,
        "disponible": True
    }
}

siguiente_id = 2


# Modelo de entrada
class LibroEntrada(BaseModel):
    titulo: str = Field(min_length=1)
    anio: int
    precio: float = 0.0
    disponible: bool = True


# Excepción personalizada
class LibroNoEncontrado(Exception):
    def __init__(self, libro_id: int):
        self.libro_id = libro_id


# Manejador para libros inexistentes
@app.exception_handler(LibroNoEncontrado)
async def manejar_libro_no_encontrado(
    request: Request, exc: LibroNoEncontrado
):
    return JSONResponse(
        status_code=404,
        content={
            "error": {
                "codigo": "NO_ENCONTRADO",
                "mensaje": f"El libro {exc.libro_id} no existe"
            }
        }
    )


# Manejador global de errores de validación
@app.exception_handler(RequestValidationError)
async def manejar_error_validacion(
    request: Request, exc: RequestValidationError
):
    return JSONResponse(
        status_code=422,
        content={
            "error": {
                "codigo": "VALIDACION",
                "mensaje": "Revisa los datos enviados"
            }
        }
    )


# Consultar un libro
@app.get("/api/libros/{libro_id}")
async def obtener_libro(libro_id: int):
    if libro_id not in libros:
        raise LibroNoEncontrado(libro_id)

    return libros[libro_id]


# Registrar un libro
@app.post("/api/libros", status_code=201)
async def crear_libro(libro: LibroEntrada):
    global siguiente_id

    nuevo_libro = {
        "id": siguiente_id,
        **libro.model_dump()
    }

    libros[siguiente_id] = nuevo_libro
    siguiente_id += 1

    return nuevo_libro
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness HTTP)
*Pruebas ejecutadas:* Pruebas de captura de errores sin crashing (HTTP 500)

* [ x ] [x] Respuestas correctas: 200, 201, 404 y 422.
* [ x ] No existen errores HTTP 500 en los cuatro casos probados.
* [ x ] Respuestas JSON correctamente configuradas.
### Reporte Crítico
*¿Qué código de estado HTTP o falla de seguridad omitió la IA y cómo lo corregiste?*
> La IA inicialmente no consideró que algunos campos podían faltar al registrar un libro. Esto provocaba un error 422. Lo corregí colocando valores predeterminados en precio y disponible para que el registro funcionara correctamente.

---
**Firma digital:** [Orlando Tec Ciau]
