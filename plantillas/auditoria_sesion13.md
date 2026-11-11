# Bitácora de Requerimientos y Auditoría IA — Sesión 13
**Materia:** Desarrollo de Aplicaciones Web (DAB-2201)  
**Tema:** Manejo de Sesiones Stateful vs Stateless Tokens  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Orlando Tec Ciau]  
**Matrícula:** [23531101]  
**Track:** [ Python/FastAPI | PHP 8+ ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Simulador de tabla de sesiones en memoria  
*Describe qué dificultad encontraste al procesar la petición HTTP o validar campos a mano:*
> La dificultad fue mantener la sesión del usuario entre diferentes peticiones HTTP y lograr que el servidor reconociera al usuario mediante una cookie. También fue necesario controlar que al cerrar sesión ya no se pudiera acceder al perfil.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal (Contrato OpenAPI / DTO)
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | Python 3.13, FastAPI y Pydantic trabajando en Google Colab. |
| **Rol (R)** | Desarrollador backend especializado en APIs REST, autenticación y seguridad web. |
| **Especificación (E)** | Implementar un sistema de sesiones Stateful en memoria, utilizando cookies HttpOnly para login, consulta del perfil y logout. |
| **Output (O)** | 
Archivo app.py funcional con modelos Pydantic, manejo de cookies y respuestas HTTP 200, 204 y 401. |

### 2.2 Prompt Estructurado
```text
[1. Crear un sistema de sesiones Stateful en memoria.
2. Utilizar el usuario de prueba ana y la contraseña secreta.
3. Implementar POST /api/login para validar las credenciales.
4. Generar un identificador aleatorio para cada sesión.
5. Enviar una cookie session_id con HttpOnly y SameSite=Lax.
6. Implementar GET /api/perfil para identificar al usuario
   mediante la cookie.
7. Implementar POST /api/logout para eliminar la sesión.
8. Después del logout, el perfil debe responder HTTP 401.
9. Utilizar códigos HTTP correctos y evitar errores 500.]
```

---

## 3. Código Devuelto por el Modelo
```python
#
El modelo proporcionó el código de app.py utilizando FastAPI y Pydantic.
Se implementaron los siguientes elementos:
Diccionario sesiones para guardar las sesiones activas.
Modelo LoginEntrada para validar usuario y contraseña.
Endpoint POST /api/login para iniciar sesión.
Generación de identificadores aleatorios con secrets.token_urlsafe(32).
Cookie session_id con protección HttpOnly.
Endpoint GET /api/perfil para consultar los datos del usuario.
Endpoint POST /api/logout para cerrar y eliminar la sesión.
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness HTTP)
*Pruebas ejecutadas:* Pruebas de expiración y revocación de sesión

* [ x ] Se verificó el inicio de sesión con respuesta HTTP 200.
* [ x ] Se comprobó la creación de la cookie HttpOnly.
* [ x ] Se verificó el acceso al perfil mediante la cookie.
* [ x ] Se comprobó el cierre de sesión con HTTP 204.
* [ x ] Se verificó que después del logout el perfil respondiera HTTP 401.
* [ x ] Se comprobó que las solicitudes inválidas fueran rechazadas correctamente.

### Reporte Crítico
*¿Qué código de estado HTTP o falla de seguridad omitió la IA y cómo lo corregiste?*
> Un error que podía presentarse era que la sesión siguiera activa después de cerrar sesión. Lo corregí eliminando el identificador del diccionario de sesiones y utilizando delete_cookie() para borrar la cookie del cliente. De esta manera, al intentar consultar nuevamente el perfil, el servidor responde con HTTP 401.

---
**Firma digital:** [Orlando Tec Ciau]
