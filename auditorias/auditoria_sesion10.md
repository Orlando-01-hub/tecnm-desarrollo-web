# Bitácora de Requerimientos y Auditoría IA — Sesión 10
**Materia:** Desarrollo de Aplicaciones Web (DAB-2201)  
**Tema:** Hito 1: Mock API RESTful con OpenAPI y Validation Harness  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Orlando Tec Ciau]  
**Matrícula:** [23531101]  
**Track:** [ Python/FastAPI | PHP 8+ ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Integración de Router y esquemas de validación  
*Describe qué dificultad encontraste al procesar la petición HTTP o validar campos a mano:*
> La mayor dificultad fue asegurar que cada ruta respondiera correctamente según lo que pedía
> el contrato. Al principio me enfoqué en que las operaciones funcionaran, pero después
> comprendí que también era importante devolver los códigos de respuesta adecuados en cada
> situación. Además, tuve que configurar correctamente las validaciones para evitar que se
> aceptaran datos incompletos o incorrectos.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal (Contrato OpenAPI / DTO)
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | FastAPI y Pydantic ejecutados en Google Colab, con validación de datos y documentación OpenAPI automática. |
| **Rol (R)** | Desarrollador backend responsable de crear una API RESTful para administrar libros. |
| **Especificación (E)** | CRUD completo sobre /api/libros, generación automática de IDs, validación de título y año, respuestas HTTP 200, 201, 204, 404, 405 y 422, y documentación disponible en /openapi.json. |
| **Output (O)** | Archivo app.py funcional que cumple el contrato del laboratorio y obtiene 9/9 casos correctos en la verificación. |

### 2.2 Prompt Estructurado
```text
[Actúa como desarrollador backend especializado en FastAPI. Implementa una Mock API RESTful para la gestión de libros mediante un archivo app.py, incluyendo CRUD completo sobre /api/libros, generación automática de identificadores, validación de título y año con Pydantic, respuestas HTTP correctas (200, 201, 204, 404, 405 y 422), manejo de errores semánticos, documentación OpenAPI disponible en /openapi.json y cumplimiento de todos los casos de la suite de verificación del Hito 1.]
```

---

## 3. Código Devuelto por el Modelo
```python
%%writefile app.py
from fastapi import FastAPI, HTTPException, Response
from pydantic import BaseModel, Field

app = FastAPI(
    title="Mock API de Libros",
    version="0.1.0"
    )

# Implementa aquí el contrato declarado en el reto.
# Tú decides los modelos, las rutas, las validaciones y el manejo de errores.

# Modelo documentado en OpenAPI
class Libro(BaseModel):
    id: int
    titulo: str = Field(min_length=1, max_length=200)
    anio: int = Field(ge=1500, le=2026)

# Modelo para entrada de datos
class LibroEntrada(BaseModel):
    titulo: str = Field(min_length=1, max_length=200)
    anio: int = Field(ge=1500, le=2026)

libros = {}
siguiente_id = 1


@app.get("/api/libros")
def listar_libros():
    return {"libros": list(libros.values())}


@app.post(
    "/api/libros",
    response_model=Libro,
    status_code=201
)
def crear_libro(datos: LibroEntrada):
    global siguiente_id

    libro = {
        "id": siguiente_id,
        "titulo": datos.titulo,
        "anio": datos.anio
    }

    libros[siguiente_id] = libro
    siguiente_id += 1

    return libro


@app.get(
    "/api/libros/{libro_id}",
    response_model=Libro
)
def consultar_libro(libro_id: int):
    if libro_id not in libros:
        raise HTTPException(
            status_code=404,
            detail="Libro no encontrado"
        )

    return libros[libro_id]


@app.put(
    "/api/libros/{libro_id}",
    response_model=Libro
)
def actualizar_libro(libro_id: int, datos: LibroEntrada):
    if libro_id not in libros:
        raise HTTPException(
            status_code=404,
            detail="Libro no encontrado"
        )

    libro = {
        "id": libro_id,
        "titulo": datos.titulo,
        "anio": datos.anio
    }

    libros[libro_id] = libro

    return libro


@app.delete(
    "/api/libros/{libro_id}",
    status_code=204
)
def eliminar_libro(libro_id: int):
    if libro_id not in libros:
        raise HTTPException(
            status_code=404,
            detail="Libro no encontrado"
        )

    del libros[libro_id]

    return Response(status_code=204)
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness HTTP)
*Pruebas ejecutadas:* Suite integral de 10 pruebas HTTP destructivas

* [x] Respuestas semánticas correctas (`200 OK`, `201 Created`, `400 Bad Request`, `422 Unprocessable`).
* [x] No existen respuestas HTTP `500 Internal Server Error` no capturadas.
* [x] Encabezados `Content-Type: application/json` correctamente configurados.

### Reporte Crítico
*¿Qué código de estado HTTP o falla de seguridad omitió la IA y cómo lo corregiste?*
> La verificación me ayudó a identificar detalles que inicialmente no había considerado, como la importancia de que cada respuesta utilizara el código HTTP correcto. Al revisar los resultados pude confirmar que la validación de datos, el manejo de errores y la documentación de la API cumplían con los requisitos establecidos en el reto.

---
**Firma digital:** [Orlando Tec Ciau]
