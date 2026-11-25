# Bitácora de Requerimientos y Auditoría IA — Sesión 11
**Materia:** Desarrollo de Aplicaciones Web (DAB-2201)  
**Tema:** Arquitectura en Capas: Router, Servicio y Capa de Datos  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Orlando Tec Ciau]  
**Matrícula:** [23531101]  
**Track:** [ Python/FastAPI | PHP 8+ ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Separación manual de controladores y lógica  
*Describe qué dificultad encontraste al procesar la petición HTTP o validar campos a mano:*
> La mayor dificultad fue organizar el código en diferentes archivos, porque al principio toda la lógica podía estar dentro de un solo archivo. Tuve que identificar qué función correspondía a cada capa. También fue necesario separar las rutas, las reglas para crear libros y el almacenamiento de los datos, procurando que la API siguiera funcionando correctamente.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal (Contrato OpenAPI / DTO)
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | Python 3.13, FastAPI y Pydantic trabajando en Google Colab, con almacenamiento de libros en memoria.|
| **Rol (R)** | Arquitecto de software senior especialista en APIs RESTful y Seguridad Web |
| **Especificación (E)** | Separar la API en cuatro archivos: app.py, router.py, servicio.py y repositorio.py. Mantener las rutas GET y POST, con respuestas HTTP 200, 201 y validación 422. |
| **Output (O)** | Cuatro archivos Python funcionales, organizados por responsabilidades y respetando el contrato HTTP solicitado. |

### 2.2 Prompt Estructurado
```text
[Estoy trabajando en una API de biblioteca para el
curso de Desarrollo de Aplicaciones Web.

Rol:
Necesito que actúes como arquitecto de software
y me ayudes a organizar correctamente el proyecto.

Especificación:
Divide la aplicación en cuatro archivos:

1. app.py: crea la aplicación FastAPI e incluye el router.
2. router.py: define las rutas GET y POST de libros.
3. servicio.py: contiene la lógica para crear y listar libros.
4. repositorio.py: almacena y recupera los libros en memoria.

Mantén el contrato HTTP:
- GET /api/libros debe responder 200.
- POST /api/libros debe responder 201.
- Los datos inválidos deben responder 422.

No mezcles las responsabilidades de las capas.
El router no debe manejar directamente el almacenamiento
y el repositorio no debe importar FastAPI.

Output:
Entrega el código completo de los cuatro archivos,
explicando brevemente la responsabilidad de cada uno
y respetando el funcionamiento de la API.]
```

---

## 3. Código Devuelto por el Modelo
```
# app.py

%%writefile app.py

from fastapi import FastAPI
from router import router

app = FastAPI(title="Biblioteca por capas")

# Incluimos las rutas de libros
app.include_router(router)
---

## repositorio.py

```

%%writefile repositorio.p

# Capa de acceso a datos
# Guarda y recupera libros. No conoce HTTP.


libros = []
siguiente_id = 1


def listar_libros():
    return libros


def guardar_libro(titulo, anio):
    global siguiente_id

    libro = {
        "id": siguiente_id,
        "titulo": titulo,
        "anio": anio
    }

    libros.append(libro)
    siguiente_id += 1

    return libro
    
---

## servicio.py

```

%%writefile servicio.py

# Capa de lógica de negocio
# Reglas para crear y listar libros.

from repositorio import listar_libros, guardar_libro


def obtener_libros():
    return {
        "libros": listar_libros()
    }


def crear_libro(titulo, anio):
    return guardar_libro(titulo, anio)

---

---
## router.py
%%writefile router.py

from fastapi import APIRouter
from pydantic import BaseModel, Field

from servicio import obtener_libros, crear_libro

router = APIRouter()


# Modelo para recibir los datos del libro
class LibroEntrada(BaseModel):
    titulo: str = Field(min_length=1)
    anio: int = Field(ge=1)


# Ruta para listar libros
@router.get("/api/libros", status_code=200)
def listar():
    return obtener_libros()


# Ruta para crear libros
@router.post("/api/libros", status_code=201)
def crear(datos: LibroEntrada):
    return crear_libro(datos.titulo, datos.anio)

## verificacion

import os

archivos = [
    "app.py",
    "router.py",
    "servicio.py",
    "repositorio.py"
]

for archivo in archivos:
    if os.path.exists(archivo):
        print("✅", archivo, "existe")
    else:
        print("❌", archivo, "no existe")

✅ app.py existe

✅ router.py existe

✅ servicio.py existe

✅ repositorio.py existe


```
## Evidencia Verificada 

==============================================================================

VERIFICACIÓN DEL RESULTADO · 4 casos · app.py

==============================================================================

✅ 01. El listado inicia vacío

✅ 02. El servicio crea un libro

✅ 03. El listado conserva el contrato

✅ 04. La validación del router sigue funcionando

------------------------------------------------------------------------------

RESULTADO: 4/4 casos correctos. ¡Resultado logrado!

---

## 4. Fase 3: El Auditor (Resultados del Test Harness HTTP)
*Pruebas ejecutadas:* Pruebas de aislamiento de errores en lógica de negocio

* [ x ] El listado inicial de libros responde con HTTP 200.
* [ x ] El servicio permite crear un libro con HTTP 201.
* [ x ] El listado conserva la estructura esperada.
* [ x ] Los datos inválidos responden con HTTP 422.
* [ x ] Existe el archivo router.py.
* [ x ] app.py incluye correctamente el router.
* [ x ] Existe el archivo servicio.py.
* [ x ] Existe el archivo repositorio.py.
### Reporte Crítico
*¿Qué código de estado HTTP o falla de seguridad omitió la IA y cómo lo corregiste?*
> El principal aspecto que tuve que revisar fue que las validaciones de los datos se mantuvieran en el router. También comprobé que cada archivo tuviera su responsabilidad y que el repositorio no dependiera de FastAPI. De esta manera se conservó el código HTTP 422 para los datos inválidos y el funcionamiento esperado de la API.

La verificación permitió comprobar que la separación de archivos no modificara las respuestas HTTP originales.

---
**Firma digital:** [Orlando Tec Ciau]
