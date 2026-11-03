# Bitácora de Requerimientos y Auditoría IA — Sesión 06
**Materia:** Desarrollo de Aplicaciones Web (DAB-2201)  
**Tema:** Validación Estricta, Constraints y Params Query/Body  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Orlando Tec Ciau]  
**Matrícula:** [23531101]  
**Track:** [ Python/FastAPI | PHP 8+ ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Filtro manual de rangos y longitudes de texto  
*Describe qué dificultad encontraste al procesar la petición HTTP o validar campos a mano:*
> R.- La dificultad que encontré fue validar correctamente los datos que recibía el servidor, porque tenía que revisar que el título tuviera la longitud adecuada, que el año estuviera dentro del rango permitido y que el precio fuera mayor que cero. También tenía que evitar que se aceptaran valores incorrectos en las consultas.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal (Contrato OpenAPI / DTO)
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | FastAPI con Python 3.13 y Pydantic v2, ejecutado en Google Colab. |
| **Rol (R)** | Desarrollador backend especializado en APIs RESTful y validación de datos. |
| **Especificación (E)** | 	Crear rutas POST y GET para libros. Validar título entre 3 y 80 caracteres, año entre 1450 y 2100, precio mayor que cero y límite entre 1 y 100 utilizando Field y Query. |
| **Output (O)** | Archivo app.py funcional, con modelos Pydantic, restricciones automáticas y respuestas HTTP 201, 200 y 422. |

### 2.2 Prompt Estructurado
```text
Especificación:
Crea una API de libros con las rutas POST /api/libros
y GET /api/libros.

El título debe tener entre 3 y 80 caracteres.
El año debe estar entre 1450 y 2100.
El precio debe ser mayor que cero.
El parámetro limite debe aceptar valores entre 1 y 100.

Los datos válidos deben responder con HTTP 201 al crear
un libro y HTTP 200 al consultar.
Los datos inválidos deben generar HTTP 422.

Utiliza Pydantic Field y Query para aplicar las
restricciones automáticamente.

Output:
Entrega el código completo de app.py, organizado,
funcional y listo para ejecutar en Google Colab.]
```

---

## 3. Código Devuelto por el Modelo
```python
# from fastapi import FastAPI, Query
from pydantic import BaseModel, Field, ConfigDict

app = FastAPI(title="Validación Estricta de Libros")

class LibroCreate(BaseModel):
    model_config = ConfigDict(extra="forbid")

    titulo: str = Field(min_length=3, max_length=80)
    anio: int = Field(ge=1450, le=2100)
    precio: float = Field(gt=0)

libros = []
siguiente_id = 1

@app.post("/api/libros", status_code=201)
def crear_libro(libro: LibroCreate):
    global siguiente_id

    nuevo_libro = {
        "id": siguiente_id,
        "titulo": libro.titulo,
        "anio": libro.anio,
        "precio": libro.precio
    }

    libros.append(nuevo_libro)
    siguiente_id += 1

    return nuevo_libro

@app.get("/api/libros")
def listar_libros(
    limite: int = Query(default=10, ge=1, le=100)
):
    return {
        "libros": libros[:limite],
        "limite": limite
    }
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness HTTP)
*Pruebas ejecutadas:* Pruebas de desbordamiento de cadenas y números negativos

* [ x ] Respuestas semánticas correctas: HTTP 200, 201 y 422.
* [ x ] Se establecieron restricciones para evitar datos fuera de los rangos permitidos.
* [ x ] Se configuró la respuesta de los datos en formato JSON mediante FastAPI.

### Reporte Crítico
*¿Qué código de estado HTTP o falla de seguridad omitió la IA y cómo lo corregiste?*
> R.- En el primer código se omitió establecer explícitamente el código HTTP 201 al registrar un libro. También se permitían datos incorrectos, como títulos demasiado cortos, años fuera del rango y precios iguales a cero. Lo corregí utilizando Field y Query de Pydantic y FastAPI, además de establecer el código 201 para los registros exitosos y mantener el 422 para los datos inválidos. 

---
**Firma digital:** [Orlando Tec Ciau]
