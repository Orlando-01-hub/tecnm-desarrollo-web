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
```python
# La IA propuso separar el proyecto en cuatro archivos:
 app.py: crea la aplicación FastAPI e incorpora el router.

router.py: define las rutas GET y POST y valida los datos recibidos mediante Pydantic.

servicio.py: contiene las funciones para crear y consultar libros.

repositorio.py: guarda los libros en una lista en memoria y asigna un ID automáticamente.
```

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
