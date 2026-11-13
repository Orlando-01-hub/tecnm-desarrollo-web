# Bitácora de Requerimientos y Auditoría IA — Sesión 15
**Materia:** Desarrollo de Aplicaciones Web (DAB-2201)  
**Tema:** Autenticación Stateless con JSON Web Tokens (JWT)  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Orlando Tec Ciau]  
**Matrícula:** [23531101]  
**Track:** [ Python/FastAPI | PHP 8+ ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Codificación Base64Url y firma HMAC-SHA256  
*Describe qué dificultad encontraste al procesar la petición HTTP o validar campos a mano:*
> Al principio tuve dificultad para entender cómo se genera y valida un token JWT, especialmente porque contiene información codificada y una firma de seguridad. También fue complicado comprobar manualmente que el token fuera correcto y que el servidor rechazara uno alterado o sin autorización. Con la práctica comprendí que el servidor utiliza una clave secreta para verificar la firma y que el token también puede tener una fecha de expiración.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal (Contrato OpenAPI / DTO)
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | Python 3.13, FastAPI, Pydantic y Python-JOSE en Google Colab. |
| **Rol (R)** | Desarrollador backend especializado en autenticación, seguridad web y APIs RESTful. |
| **Especificación (E)** | Crear JWT firmado con HS256 y expiración. Implementar /api/token y /api/perfil, validar credenciales y rechazar tokens inválidos, alterados o vencidos con HTTP 401. |
| **Output (O)** | Archivo app.py con modelos Pydantic, generación de JWT, variable de entorno para la clave secreta y rutas protegidas. |

### 2.2 Prompt Estructurado
```text
[Crear el endpoint POST /api/token, que reciba usuario y contraseña.
Validar las credenciales de prueba: usuario "ana" y contraseña "secreta".
Si las credenciales son correctas, devolver un token JWT con el usuario y la fecha de expiración.
Crear el endpoint GET /api/perfil, que solicite el encabezado Authorization con formato Bearer.
Verificar la firma, validez y expiración del token.
Si el token es inválido, alterado, expirado o no se proporciona, responder con HTTP 401.
Utilizar una variable de entorno para almacenar la clave secreta.
Utilizar modelos Pydantic para validar los datos de entrada y salida.
Evitar errores HTTP 500 no controlados.]
```

---

## 3. Código Devuelto por el Modelo
```python
# %%writefile app.py

import os
from datetime import datetime, timedelta, timezone

from fastapi import FastAPI, HTTPException, Header
from pydantic import BaseModel, Field, ConfigDict
from jose import jwt, JWTError


# 1. Configuración de la aplicación
app = FastAPI(
    title="API de Autenticación JWT",
    version="1.0.0"
)


# 2. Obtener la clave secreta desde una variable de entorno
SECRET_KEY = os.getenv("JWT_SECRET_KEY")

if not SECRET_KEY:
    raise RuntimeError(
        "Falta configurar la variable de entorno JWT_SECRET_KEY"
    )


# 3. Configuración del algoritmo
ALGORITHM = "HS256"

# Duración del token: 30 minutos
ACCESS_TOKEN_EXPIRE_MINUTES = 30


# 4. Usuario de prueba de la actividad
USUARIO_PRUEBA = "ana"
PASSWORD_PRUEBA = "secreta"


# 5. Modelo para recibir las credenciales
class Credenciales(BaseModel):
    model_config = ConfigDict(extra="forbid")

    usuario: str = Field(min_length=1)
    password: str = Field(min_length=1)


# 6. Modelo de respuesta del token
class TokenRespuesta(BaseModel):
    access_token: str
    token_type: str


# 7. Modelo de respuesta del perfil
class PerfilRespuesta(BaseModel):
    usuario: str


# 8. Función para crear el token JWT
def crear_token(usuario: str):

    # Obtener la fecha y hora actual
    ahora = datetime.now(timezone.utc)

    # Establecer la fecha de expiración
    expiracion = ahora + timedelta(
        minutes=ACCESS_TOKEN_EXPIRE_MINUTES
    )

    # Información que contendrá el token
    datos_token = {
        "usuario": usuario,
        "exp": expiracion
    }

    # Firmar el token con la clave secreta
    token = jwt.encode(
        datos_token,
        SECRET_KEY,
        algorithm=ALGORITHM
    )

    return token


# 9. Ruta para generar el token
@app.post(
    "/api/token",
    response_model=TokenRespuesta
)
def obtener_token(datos: Credenciales):

    # Validar las credenciales del usuario
    if (
        datos.usuario != USUARIO_PRUEBA
        or datos.password != PASSWORD_PRUEBA
    ):
        raise HTTPException(
            status_code=401,
            detail="Credenciales incorrectas"
        )

    # Generar el token JWT
    access_token = crear_token(datos.usuario)

    # Devolver el token al cliente
    return {
        "access_token": access_token,
        "token_type": "bearer"
    }


# 10. Ruta protegida del perfil
@app.get(
    "/api/perfil",
    response_model=PerfilRespuesta
)
def obtener_perfil(
    authorization: str | None = Header(default=None)
):

    # Comprobar que exista el encabezado Authorization
    if not authorization:
        raise HTTPException(
            status_code=401,
            detail="Token no proporcionado"
        )

    # Comprobar el formato Bearer
    partes = authorization.split()

    if len(partes) != 2 or partes[0].lower() != "bearer":
        raise HTTPException(
            status_code=401,
            detail="Formato de autorización inválido"
        )

    token = partes[1]

    try:
        # Verificar firma y expiración del token
        payload = jwt.decode(
            token,
            SECRET_KEY,
            algorithms=[ALGORITHM]
        )

        # Recuperar el usuario del token
        usuario = payload.get("usuario")

        if not usuario or not isinstance(usuario, str):
            raise HTTPException(
                status_code=401,
                detail="Token inválido"
            )

    except JWTError:
        # Rechazar tokens alterados o vencidos
        raise HTTPException(
            status_code=401,
            detail="Token inválido o expirado"
        )

    # Responder con el perfil del usuario
    return {
        "usuario": usuario
    }
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness HTTP)
*Pruebas ejecutadas:* Pruebas con tokens alterados o firma inválida

* [ x ] Respuestas semánticas correctas (`200 OK`, `201 Created`, `400 Bad Request`, `422 Unprocessable`).
* [ x ] No existen respuestas HTTP `500 Internal Server Error` no capturadas.
* [ x ] Encabezados `Content-Type: application/json` correctamente configurados.

### Reporte Crítico
*¿Qué código de estado HTTP o falla de seguridad omitió la IA y cómo lo corregiste?*
> Una de las fallas importantes que debía controlar era que la API no aceptara tokens alterados, inválidos o sin proporcionar. Para corregirlo, agregué la validación del encabezado Authorization con formato Bearer y la verificación del token mediante la clave secreta. También se controla la expiración del JWT y se devuelve el código HTTP 401 cuando el token no es válido o no está presente. De esta manera se evita que la aplicación acepte accesos no autorizados o genere errores 500 por estas situaciones.

---
**Firma digital:** [Orlando Tec Ciau]
