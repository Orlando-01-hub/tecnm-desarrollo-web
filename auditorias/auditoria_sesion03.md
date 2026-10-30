# Bitácora de Requerimientos y Auditoría IA — Sesión 03
**Materia:** Desarrollo de Aplicaciones Web (DAB-2201)  
**Tema:** Headers, Content-Type y Negociación de Contenido  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Orlando Tec Ciau]  
**Matrícula:** [23531101]  
**Track:** [ Python/FastAPI | PHP 8+ ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Lectura y emisión manual de encabezados HTTP  
*Describe qué dificultad encontraste al procesar la petición HTTP o validar campos a mano:*
> R.- La principal dificultad fue comprender la diferencia entre Content-Type y Accept, porque ambos son encabezados HTTP, pero cumplen funciones distintas. También tuve que identificar cómo rechazar formatos no compatibles y devolver el código de estado correcto sin afectar el funcionamiento de la API.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal (Contrato OpenAPI / DTO)
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | Python 3.10+ y FastAPI para desarrollar y probar una API web. |
| **Rol (R)** | Desarrollador especializado en APIs REST y protocolos HTTP. |
| **Especificación (E)** | Crear las rutas POST /api/libros y GET /api/libros. Aceptar JSON, responder 201 al crear un libro, 415 ante formatos de entrada no compatibles, 406 cuando el cliente solo acepte XML y 200 al consultar libros en JSON. |
| **Output (O)** | Código funcional para app.py, con respuestas JSON, códigos HTTP correctos e instrucciones para probar los cuatro casos. |

### 2.2 Prompt Estructurado
```text
[Implementa una API con las rutas POST /api/libros y
GET /api/libros.

El POST solo debe aceptar Content-Type: application/json.
Si el formato no es compatible, debe responder 415.
Si el libro se crea correctamente, debe responder 201.

El GET debe responder en JSON y devolver 406 si el cliente
solicita únicamente application/xml. Si la solicitud es
válida, debe responder 200.

Las respuestas deben incluir Content-Type: application/json.
Entrega el código completo de app.py, organizado y comentado,
e indica cómo probar los cuatro casos con solicitudes HTTP.]
```

---

## 3. Código Devuelto por el Modelo
```python
# %%writefile app.py
from fastapi import FastAPI, Request
from fastapi.responses import JSONResponse

app = FastAPI(title="Tu API negociará el formato exactamente así")

libros = []
siguiente_id = 1


@app.post("/api/libros")
async def crear_libro(request: Request):
    global siguiente_id

    # Validar el formato de entrada
    content_type = request.headers.get("content-type", "")
    content_type = content_type.split(";")[0].strip().lower()

    if content_type != "application/json":
        return JSONResponse(
            status_code=415,
            content={"error": "Formato no soportado"}
        )

    # Leer y validar el JSON
    try:
        datos = await request.json()
    except Exception:
        return JSONResponse(
            status_code=400,
            content={"error": "JSON inválido"}
        )

    if not isinstance(datos, dict):
        return JSONResponse(
            status_code=400,
            content={"error": "Se esperaba un objeto JSON"}
        )

    if "titulo" not in datos or "anio" not in datos:
        return JSONResponse(
            status_code=400,
            content={"error": "Faltan titulo o anio"}
        )

    # Crear y guardar el libro
    libro = {
        "id": siguiente_id,
        "titulo": datos["titulo"],
        "anio": datos["anio"]
    }

    libros.append(libro)
    siguiente_id += 1

    # Responder con 201 Created
    return JSONResponse(
        status_code=201,
        content=libro,
        media_type="application/json"
    )


@app.get("/api/libros")
async def listar_libros(request: Request):
    # Validar el formato que acepta el cliente
    accept = request.headers.get("accept", "")
    formatos = [
        formato.split(";")[0].strip().lower()
        for formato in accept.split(",")
    ]

    acepta_json = any(
        formato in (
            "application/json",
            "application/*",
            "*/*"
        )
        for formato in formatos
    )

    if not acepta_json:
        return JSONResponse(
            status_code=406,
            content={"error": "Solo se responde en JSON"}
        )

    # Responder con 200 OK
    return JSONResponse(
        status_code=200,
        content=libros,
        media_type="application/json"
    )
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness HTTP)
*Pruebas ejecutadas:* Pruebas con tipos de contenido no soportados (415)
Prueba realizadas:
POST con JSON válido ->> 201 Created
POST con texto plano ->> 415 Unsupported Media Type
GET solicitando XML ->> 406 Not Acceptable
GET aceptando JSON - >> 200 OK

* [ X ] Respuestas semánticas correctas (`200 OK`, `201 Created`, `400 Bad Request`, `422 Unprocessable`).
* [ X ] No existen respuestas HTTP `500 Internal Server Error` no capturadas.
* [ X ] Encabezados `Content-Type: application/json` correctamente configurados.

### Reporte Crítico
*¿Qué código de estado HTTP o falla de seguridad omitió la IA y cómo lo corregiste?*
> R.- Durante la revisión del código identifiqué la importancia de comprobar que cada operación devuelva el código HTTP establecido en el contrato. En una primera versión, el POST devolvía 200 OK cuando debía devolver 201 Created. Corregí ese detalle y revisé la validación de los encabezados para que la API rechazara los formatos no compatibles.
También consideré necesario manejar los errores de JSON inválido y los campos obligatorios para evitar fallos inesperados.

---Conclusión

Esta actividad me permitió comprender mejor cómo funcionan los encabezados HTTP y por qué es importante validar tanto el formato de los datos enviados como el formato de la respuesta. También aprendí que no basta con que el código funcione: es necesario probarlo y verificar que cumpla con los requisitos establecidos.

**Firma digital:** [Orlando Tec Ciau]
