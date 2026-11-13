# Bitácora de Requerimientos y Auditoría IA — Sesión 16
**Materia:** Desarrollo de Aplicaciones Web (DAB-2201)  
**Tema:** OAuth2 Password Flow y Header Authorization Bearer  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Orlando Tec Ciau]  
**Matrícula:** [23531101]  
**Track:** [ Python/FastAPI | PHP 8+ ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Extracción manual del encabezado Authorization  
*Describe qué dificultad encontraste al procesar la petición HTTP o validar campos a mano:*
> Al intentar procesar y validar el encabezado `Authorization` manualmente, me di cuenta de que es propenso a errores: hay que verificar que exista, que tenga el formato correcto (`Bearer <token>`), separar el esquema del token mismo, manejar espacios en blanco y diferenciar entre "sin encabezado" y "esquema distinto". Además, al usar un formulario en lugar de JSON para el login, tuve que adaptar cómo se envían los datos, ya que inicialmente intentaba leerlos como JSON y recibía respuestas `422 Unprocessable Entity`. OAuth2 usa `application/x-www-form-urlencoded` porque es compatible de forma nativa con navegadores y no requiere bibliotecas adicionales, pero al principio no me resultaba intuitivo.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal (Contrato OpenAPI / DTO)
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | FastAPI 0.110+ (Python 3.10+), Google Colab, python-multipart para formularios, python-jose/PyJWT para tokens JWT |
| **Rol (R)** | Arquitecto de software especializado en APIs RESTful, autenticación OAuth2 y seguridad web |
| **Especificación (E)** | Endpoint `POST /token` con `OAuth2PasswordRequestForm` que valida usuario `ana` / contraseña `secreta` y devuelve JWT. Ruta protegida `GET /api/perfil` con `OAuth2PasswordBearer`. Sin token o esquema incorrecto → HTTP 401 + encabezado `WWW-Authenticate: Bearer` |
| **Output (O)** | Archivo `app.py` modular con instancia FastAPI, generación/validación JWT, códigos HTTP semánticos (200, 401) y respuestas JSON correctas |

### 2.2 Prompt Estructurado
```text
[Implementa el flujo OAuth2 Password Flow en FastAPI. Crea un endpoint POST /token que reciba username y password mediante OAuth2PasswordRequestForm, valide contra usuario "ana" y contraseña "secreta", y devuelva un access_token firmado con JWT y token_type "bearer". Protege la ruta GET /api/perfil usando OAuth2PasswordBearer. Si no hay token o es inválido, responde con HTTP 401 e incluye el encabezado WWW-Authenticate: Bearer. Usa HS256 como algoritmo JWT y una clave secreta obtenida desde variables de entorno. Devuelve solo el código completo en un archivo app.py.]
```

---

## 3. Código Devuelto por el Modelo
```python
# import os
from datetime import datetime, timedelta, timezone
from fastapi import FastAPI, Depends, HTTPException
from fastapi.security import (
    OAuth2PasswordBearer,
    OAuth2PasswordRequestForm
)
from jose import jwt, JWTError

app = FastAPI(
    title="API OAuth2 Password Flow",
    version="1.0.0"
)

SECRET_KEY = os.getenv("JWT_SECRET_KEY")
if not SECRET_KEY:
    raise RuntimeError("Falta configurar JWT_SECRET_KEY")

ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30

USUARIO_PRUEBA = "ana"
PASSWORD_PRUEBA = "secreta"

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

def crear_token(usuario: str):
    ahora = datetime.now(timezone.utc)
    expiracion = ahora + timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    datos_token = {
        "usuario": usuario,
        "exp": expiracion
    }
    return jwt.encode(datos_token, SECRET_KEY, algorithm=ALGORITHM)

@app.post("/token")
def login(formulario: OAuth2PasswordRequestForm = Depends()):
    if formulario.username != USUARIO_PRUEBA or formulario.password != PASSWORD_PRUEBA:
        raise HTTPException(
            status_code=401,
            detail="Credenciales incorrectas",
            headers={"WWW-Authenticate": "Bearer"}
        )
    return {
        "access_token": crear_token(formulario.username),
        "token_type": "bearer"
    }

@app.get("/api/perfil")
def obtener_perfil(token: str = Depends(oauth2_scheme)):
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        usuario = payload.get("usuario")
        if not usuario or not isinstance(usuario, str):
            raise HTTPException(
                status_code=401,
                detail="Token inválido",
                headers={"WWW-Authenticate": "Bearer"}
            )
    except JWTError:
        raise HTTPException(
            status_code=401,
            detail="Token inválido o expirado",
            headers={"WWW-Authenticate": "Bearer"}
        )
    return {"usuario": usuario}

```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness HTTP)
*Pruebas ejecutadas:* Pruebas de acceso no autorizado sin token (HTTP 401)

* [ x ] Respuestas semánticas correctas (200 OK, 401 Unauthorized)
* [ x ] Sin errores 500 Internal Server Error
* [ x ] Content-Type: application/json` presente en respuestas
* [ x ] Encabezado `WWW-Authenticate: Bearer` incluido en respuestas 401
### Reporte Crítico
*¿Qué código de estado HTTP o falla de seguridad omitió la IA y cómo lo corregiste?*
> La primera versión que obtuve de la IA devolvía `400 Bad Request` en lugar de `401 Unauthorized` cuando las credenciales eran incorrectas, y olvidaba incluir el encabezado `WWW-Authenticate: Bearer` en las respuestas de error. Además, en la decodificación del JWT faltaba el parámetro `algorithms=[ALGORITHM]`, lo cual representa una vulnerabilidad de seguridad. Lo corregí cambiando el código de estado a **401**, agregando el diccionario `headers={"WWW-Authenticate": "Bearer"}` en cada excepción HTTPException, y asegurando que `algorithms=[ALGORITHM]` estuviera presente en la llamada a `jwt.decode()`.

---
**Firma digital:** [Orlando Tec Ciau]
