# Bitácora de Requerimientos y Auditoría IA — Sesión 04
**Materia:** Desarrollo de Aplicaciones Web (DAB-2201)  
**Tema:** Anatomía RESTful, URIs Nombres y Recursos  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Orlado Tec Ciau]  
**Matrícula:** [23531101]  
**Track:** [ Python/FastAPI | PHP 8+ ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Diseño de URIs jerárquicas sin verbos en la ruta  
*Describe qué dificultad encontraste al procesar la petición HTTP o validar campos a mano:*
> R.- La principal dificultad fue organizar correctamente las rutas para consultar los libros de cada autor sin utilizar verbos. También tuve que diferenciar los parámetros de ruta de los parámetros de consulta para filtrar por género y limitar los resultados.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal (Contrato OpenAPI / DTO)
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | Python con FastAPI en Google Colab. |
| **Rol (R)** | Asistente técnico especializado en diseño de API RESTful. |
| **Especificación (E)** | Crear rutas para consultar autores y libros, filtrar por género, limitar resultados y responder 404 si el autor no existe. |
| **Output (O)** | Código completo de app.py, con respuestas JSON y rutas RESTful. |

### 2.2 Prompt Estructurado
```text 
[Desarrolla una API RESTful utilizando Python y FastAPI para administrar consultas de autores y libros. Utiliza rutas con nombres de recursos, permite filtrar los libros por género y limitar los resultados. Si el autor no existe, devuelve un error HTTP 404 con el mensaje correspondiente. Las respuestas deben estar en formato JSON y cumplir con los criterios de verificación de la actividad.]
```

---

## 3. Código Devuelto por el Modelo
```python
# %%writefile app.py
from fastapi import FastAPI, HTTPException, Query

app = FastAPI(
    title="API RESTful de Autores y Libros",
    description="API para consultar autores y sus libros",
    version="1.0.0"
)

# Datos iniciales de los autores
autores = {
    1: {
        "id": 1,
        "nombre": "Gabriel García Márquez"
    },
    2: {
        "id": 2,
        "nombre": "Julio Cortázar"
    }
}

# Colección de libros
libros = [
    {
        "id": 1,
        "titulo": "Cien años de soledad",
        "genero": "novela",
        "autor_id": 1
    },
    {
        "id": 2,
        "titulo": "Doce cuentos peregrinos",
        "genero": "cuento",
        "autor_id": 1
    },
    {
        "id": 3,
        "titulo": "Rayuela",
        "genero": "novela",
        "autor_id": 2
    }
]


# Consultar todos los autores
@app.get("/api/autores")
def obtener_autores():
    return {
        "autores": list(autores.values())
    }


# Consultar la colección completa de libros
@app.get("/api/libros")
def obtener_libros():
    return {
        "libros": [
            {
                "id": libro["id"],
                "titulo": libro["titulo"],
                "genero": libro["genero"]
            }
            for libro in libros
        ]
    }


# Consultar los libros de un autor específico
@app.get("/api/autores/{autor_id}/libros")
def obtener_libros_autor(
    autor_id: int,
    genero: str | None = None,
    limite: int | None = Query(default=None, ge=1)
):
    # Verificar si el autor existe
    if autor_id not in autores:
        raise HTTPException(
            status_code=404,
            detail="Autor no encontrado"
        )

    # Obtener los libros correspondientes al autor
    resultado = [
        {
            "id": libro["id"],
            "titulo": libro["titulo"],
            "genero": libro["genero"]
        }
        for libro in libros
        if libro["autor_id"] == autor_id
    ]

    # Filtrar por género si se especifica
    if genero is not None:
        resultado = [
            libro
            for libro in resultado
            if libro["genero"].lower() == genero.lower()
        ]

    # Limitar la cantidad de resultados si se especifica
    if limite is not None:
        resultado = resultado[:limite]

    # Devolver la respuesta en formato JSON
    return {
        "autor_id": autor_id,
        "libros": resultado
    }
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness HTTP)
*Pruebas ejecutadas:* Pruebas de peticiones a recursos inexistentes (404)

* [ X ] Consulta de los libros del autor 1: HTTP 200.
* [ X ] Filtro por género y límite: HTTP 200.
* [ X ] Consulta de un autor inexistente: HTTP 404.
* [ X ] Consulta de la colección completa de libros: HTTP 200.
### Reporte Crítico
*¿Qué código de estado HTTP o falla de seguridad omitió la IA y cómo lo corregiste?*
> R.- El error detectado fue que la ruta /api/libros devolvía una lista directamente, cuando el verificador esperaba un objeto JSON con la propiedad libros. Lo corregí modificando la estructura de la respuesta. Después ejecuté nuevamente las pruebas y logré que los cuatro casos fueran correctos.

---
**Firma digital:** [Orlando Tec Ciau]
