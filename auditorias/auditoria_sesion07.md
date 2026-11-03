# Bitácora de Requerimientos y Auditoría IA — Sesión 07
**Materia:** Desarrollo de Aplicaciones Web (DAB-2201)  
**Tema:** Especificación OpenAPI (Swagger) y ReDoc  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Orlando Tec Ciau]  
**Matrícula:** [23531101]  
**Track:** [ Python/FastAPI | PHP 8+ ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Anotación manual de metadata de endpoints  
*Describe qué dificultad encontraste al procesar la petición HTTP o validar campos a mano:*
>La dificultad que encontré fue organizar la información de los endpoints y documentar correctamente las rutas de la API. También tuve problemas para hacer que las respuestas del servidor coincidieran con la estructura que esperaba la documentación. 

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal (Contrato OpenAPI / DTO)
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | Python 3.13, FastAPI y Pydantic v2, trabajando en Google Colab. |
| **Rol (R)** | Desarrollador backend especializado en APIs RESTful y documentación OpenAPI. |
| **Especificación (E)** | Crear una API llamada Biblioteca API, versión 1.0.0, con documentación OpenAPI 3.1, Swagger UI y ReDoc. Implementar GET y POST en /api/libros. |
| **Output (O)** | Código completo de app.py, modelos Pydantic, endpoints documentados y respuestas HTTP correctas. |

### 2.2 Prompt Estructurado
```text
[Contexto:
Estoy trabajando con Python 3.13, FastAPI y Pydantic v2
en Google Colab para desarrollar una API de libros.

Rol:
Actúa como desarrollador backend especializado en
APIs RESTful y documentación OpenAPI.

Especificación:
Crear una aplicación llamada Biblioteca API,
versión 1.0.0, con la descripción
"API de libros del curso DAB-2201".

Implementar los siguientes endpoints:
GET /api/libros para consultar los libros.
POST /api/libros para registrar nuevos libros.

Utilizar modelos Pydantic para documentar los datos.
El listado debe iniciar vacío y responder con un
objeto JSON que contenga la propiedad libros.

La documentación debe estar disponible en:
 /openapi.json
 /docs
 /redoc

El listado debe responder HTTP 200 y el registro
de libros HTTP 201.

Output:
Entregar el código completo de app.py, funcional,
organizado y con documentación automática Swagger
y ReDoc.]
```

---

## 3. Código Devuelto por el Modelo
```python
# 
%%writefile app.py

from fastapi import FastAPI
from pydantic import BaseModel, Field

# Configuración y metadatos de la API
app = FastAPI(
    title="Biblioteca API",
    version="1.0.0",
    description="API de libros del curso DAB-2201"
)

# Modelo de libro
class Libro(BaseModel):
    id: int
    titulo: str = Field(min_length=3, max_length=80)
    anio: int = Field(ge=1450, le=2100)
    precio: float = Field(gt=0)

# Modelo para crear libros
class LibroCreate(BaseModel):
    titulo: str = Field(min_length=3, max_length=80)
    anio: int = Field(ge=1450, le=2100)
    precio: float = Field(gt=0)

# Modelo de respuesta del listado
class LibrosResponse(BaseModel):
    libros: list[Libro]

# Lista vacía al iniciar la aplicación
libros = []

# Endpoint GET: consultar libros
@app.get(
    "/api/libros",
    summary="Listar libros",
    response_model=LibrosResponse,
    status_code=200
)
def listar_libros():
    return {"libros": libros}

# Endpoint POST: registrar un libro
@app.post(
    "/api/libros",
    summary="Crear un libro",
    response_model=Libro,
    status_code=201
)
def crear_libro(libro: LibroCreate):

    nuevo_libro = Libro(
        id=len(libros) + 1,
        titulo=libro.titulo,
        anio=libro.anio,
        precio=libro.precio
    )

    libros.append(nuevo_libro)

    return nuevo_libro

```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness HTTP)
*Pruebas ejecutadas:* Verificación de renderizado de interfaz Swagger UI

* [ x ] OpenAPI respondió correctamente con HTTP 200.
* [ x ] Swagger UI estuvo disponible con HTTP 200.
* [ x ] ReDoc estuvo disponible con HTTP 200.
* [ x ] El endpoint GET /api/libros respondió correctamente con HTTP 200.
* [ x ] Se obtuvo un resultado final de 4 de 4 pruebas aprobadas.

### Reporte Crítico
*¿Qué código de estado HTTP o falla de seguridad omitió la IA y cómo lo corregiste?*
> El principal error fue que el endpoint GET devolvía una lista directamente y no un objeto JSON con la propiedad libros. También tenía dos libros de ejemplo cuando el verificador esperaba una lista vacía. Lo corregí creando el modelo LibrosResponse y dejando la lista inicial vacía. Así conseguí que las cuatro pruebas fueran aprobadas.

---
**Firma digital:** [Orlando Tec Ciau]
