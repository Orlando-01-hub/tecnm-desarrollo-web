# Bitácora de Requerimientos y Auditoría IA — Sesión 05
**Materia:** Desarrollo de Aplicaciones Web (DAB-2201)  
**Tema:** Pydantic v2 / DTOs en PHP y Tipado Estático  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Orlando Tec Ciau]  
**Matrícula:** [23531101]  
**Track:** [ Python/FastAPI | PHP 8+ ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* DTO manual con validación de tipos primitivos  
*Describe qué dificultad encontraste al procesar la petición HTTP o validar campos a mano:*
> R.- La principal dificultad fue validar que los datos recibidos fueran del tipo correcto, ya que un usuario puede enviar información incorrecta o campos que no están definidos. También fue necesario controlar que los datos se guardaran correctamente y que el servidor respondiera con el código HTTP adecuado. Con Pydantic fue más sencillo realizar estas validaciones.
---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal (Contrato OpenAPI / DTO)
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | Python 3.13, FastAPI y Pydantic v2 ejecutados en Google Colab. |
| **Rol (R)** | Arquitecto de software especializado en APIs RESTful, validación de datos y seguridad web. |
| **Especificación (E)** | Crear DTO de entrada y salida para libros, validar título, año, precio y disponibilidad. Rechazar campos adicionales con extra="forbid" y permitir la conversión de cadenas numéricas. |
| **Output (O)** | Archivo app.py funcional, con endpoints POST y GET, respuestas JSON y códigos HTTP 201, 200 y 422. |

### 2.2 Prompt Estructurado
```text

Especificación:
Crea una API para registrar y consultar libros.
Utiliza un DTO de entrada llamado LibroEntrada con los campos:
titulo (str), anio (int), precio (float) y disponible (bool).

Utiliza ConfigDict(extra="forbid") para rechazar campos
adicionales. Permite convertir cadenas numéricas como
"1963" a enteros y rechaza valores no numéricos con HTTP 422.

Crea un DTO de salida llamado LibroSalida que devuelva
el id y los datos del libro.

Implementa POST /api/libros con respuesta HTTP 201
y GET /api/libros con respuesta HTTP 200.
El listado debe devolver un objeto JSON con la propiedad
"libros" que contenga los registros creados.

Output:
Entrega el código completo y funcional de app.py,
con modelos Pydantic v2, rutas y validaciones.]
```

---

## 3. Código Devuelto por el Modelo
```python
# %%writefile app.py

from fastapi import FastAPI
from pydantic import BaseModel, ConfigDict

app = FastAPI(title="API de libros con Pydantic v2")

# Almacenamiento temporal
libros = []
siguiente_id = 1


# DTO de entrada
class LibroEntrada(BaseModel):
    model_config = ConfigDict(extra="forbid")

    titulo: str
    anio: int
    precio: float
    disponible: bool


# DTO de salida
class LibroSalida(BaseModel):
    id: int
    titulo: str
    anio: int
    precio: float
    disponible: bool


# Crear un libro
@app.post(
    "/api/libros",
    response_model=LibroSalida,
    status_code=201
)
async def crear_libro(libro: LibroEntrada):
    global siguiente_id

    nuevo_libro = LibroSalida(
        id=siguiente_id,
        titulo=libro.titulo,
        anio=libro.anio,
        precio=libro.precio,
        disponible=libro.disponible
    )

    libros.append(nuevo_libro)
    siguiente_id += 1

    return nuevo_libro


# Listar libros
@app.get("/api/libros", status_code=200)
async def listar_libros():

    return {
        "libros": libros
    }
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness HTTP)
*Pruebas ejecutadas:* Pruebas de tipos incompatibles y coerción

* [ X ] Respuestas semánticas correctas: HTTP 200, 201 y 422.
* [ X ] Se rechazaron los años no numéricos.
* [ X ] Se permitió convertir la cadena numérica "1963" a entero.
* [ X ] Se rechazaron campos adicionales mediante extra="forbid".
* [ X ] El listado devolvió los libros dentro de la propiedad libros.
* [ X ] Las respuestas se entregaron en formato JSON.

### Reporte Crítico
*¿Qué código de estado HTTP o falla de seguridad omitió la IA y cómo lo corregiste?*
> R.- Durante la verificación detecté que el listado de libros devolvía directamente una lista, cuando el contrato esperaba un objeto JSON con la propiedad "libros". Corregí el endpoint GET para que devolviera la estructura solicitada. También comprobé que los datos incorrectos y los campos adicionales fueran rechazados con HTTP 422.

---
**Firma digital:** [Orlando Tec Ciau]
