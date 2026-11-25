# Bitácora de Requerimientos y Auditoría IA — Sesión 12
**Materia:** Desarrollo de Aplicaciones Web (DAB-2201)  
**Tema:** Inyección de Dependencias (FastAPI Depends / PHP Container)  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Orlando Tec Ciau]  
**Matrícula:** [23531101]  
**Track:** [ Python/FastAPI | PHP 8+ ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Inyector de dependencias manual por funciones  
*Describe qué dificultad encontraste al procesar la petición HTTP o validar campos a mano:*
> La dificultad fue mantener los datos de los libros entre diferentes peticiones y evitar crear un repositorio nuevo cada vez. También fue necesario validar que los datos enviados fueran correctos para evitar errores en la API.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal (Contrato OpenAPI / DTO)
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | FastAPI con Python 3.13, utilizando Google Colab y las herramientas instaladas para el desarrollo de la API.|
| **Rol (R)** | Arquitecto de software especializado en APIs REST e inyección de dependencias. |
| **Especificación (E)** | Implementar un repositorio en memoria, una función get_repositorio() y utilizar Depends() en los endpoints para recibir la dependencia. |
| **Output (O)** | Archivo app.py funcional con modelos Pydantic, diagnóstico del repositorio y endpoints GET y POST con códigos HTTP correctos. |

### 2.2 Prompt Estructurado
```text
[Estoy trabajando con Python y FastAPI en Google Colab.

Requisitos:
1. Crear una clase llamada RepositorioMemoria.
2. Crear una función get_repositorio() que devuelva una instancia del repositorio.
3. Utilizar Depends(get_repositorio) en los endpoints.
4. Crear GET /api/diagnostico que devuelva el nombre del repositorio.
5. Crear POST /api/libros que registre libros y responda HTTP 201.
6. Crear GET /api/libros que muestre los libros registrados.
7. Validar los datos utilizando Pydantic y responder HTTP 422 cuando sean inválidos.
8. Mantener los datos utilizando una instancia compartida del repositorio.
Output:
Entregar el código completo de app.py, organizado y funcional,
respetando los códigos HTTP establecidos en el contrato.]
```

---

## 3. Código Devuelto por el Modelo
```
%%writefile app.py

from fastapi import FastAPI, Depends
from pydantic import BaseModel, Field
from typing import List


app = FastAPI(
    title="API de Libros con Inyección de Dependencias",
    version="1.0.0"
)


# 1. Modelo de entrada
class LibroEntrada(BaseModel):
    titulo: str = Field(min_length=1)
    anio: int


# 2. Modelo de salida
class LibroSalida(BaseModel):
    id: int
    titulo: str
    anio: int


# 3. Repositorio en memoria
class RepositorioMemoria:

    def __init__(self):
        self.libros = []
        self.siguiente_id = 1

    def crear(self, libro: LibroEntrada):
        nuevo_libro = LibroSalida(
            id=self.siguiente_id,
            titulo=libro.titulo,
            anio=libro.anio
        )

        self.libros.append(nuevo_libro)
        self.siguiente_id += 1

        return nuevo_libro

    def obtener_todos(self):
        return self.libros


# 4. Instancia compartida del repositorio
repositorio_memoria = RepositorioMemoria()


# 5. Función de inyección de dependencias
def get_repositorio() -> RepositorioMemoria:
    return repositorio_memoria


# 6. Diagnóstico
@app.get("/api/diagnostico")
def diagnostico(
    repositorio: RepositorioMemoria = Depends(get_repositorio)
):
    return {
        "repositorio": type(repositorio).__name__
    }


# 7. Crear un libro
@app.post(
    "/api/libros",
    response_model=LibroSalida,
    status_code=201
)
def crear_libro(
    libro: LibroEntrada,
    repositorio: RepositorioMemoria = Depends(get_repositorio)
):
    return repositorio.crear(libro)


# 8. Consultar todos los libros
@app.get("/api/libros")
def listar_libros(
    repositorio: RepositorioMemoria = Depends(get_repositorio)
):
    return {
        "libros": repositorio.obtener_todos()
    }

```
## Evidencia Verificada

==============================================================================

VERIFICACIÓN DEL RESULTADO · 4 casos · app.py

==============================================================================

✅ 01. El diagnóstico revela el repositorio inyectado

✅ 02. El endpoint inyectado crea un libro

✅ 03. El repositorio conserva el estado

✅ 04. La validación sigue en el borde

------------------------------------------------------------------------------

RESULTADO: 4/4 casos correctos. ¡Resultado logrado!

from verificar import revisar_archivo, resumen

resultados = []
resultados.append(revisar_archivo("app.py", exigir=["Depends"], descripcion="El endpoint usa Depends"))
resultados.append(revisar_archivo("app.py", exigir=["get_repositorio"], descripcion="Existe la función de inyección"))
resumen(resultados)

     
✅ El endpoint usa Depends

✅ Existe la función de inyección

------------------------------------------------------------------------------

RESULTADO: 2/2 comprobaciones correctas. ¡Resultado logrado!

---

## 4. Fase 3: El Auditor (Resultados del Test Harness HTTP)
*Pruebas ejecutadas:* Pruebas de ciclo de vida de dependencias por request 

* [ x ] Respuestas HTTP correctas: 200 OK, 201 Created y 422 Unprocessable Entity.
* [ x ] Se verificó que el repositorio conserva los libros registrados.
* [ x ] Se comprobó que los endpoints utilizan Depends().
* [ x ] Se comprobó que existe la función get_repositorio().
* [ x ] Se verificó que los datos inválidos sean rechazados mediante Pydantic.

### Reporte Crítico
*¿Qué código de estado HTTP o falla de seguridad omitió la IA y cómo lo corregiste?*
> Un error que podía presentarse era que el repositorio no conservara los libros registrados si se creaba una instancia nueva en cada petición. Lo corregí utilizando una instancia compartida mediante get_repositorio(). También comprobé que los datos incorrectos fueran rechazados con el código HTTP 422.

---
**Firma digital:** [Orlando Tec Ciau]
