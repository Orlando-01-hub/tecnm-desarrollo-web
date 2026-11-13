# Bitácora de Requerimientos y Auditoría IA — Sesión 17
**Materia:** Desarrollo de Aplicaciones Web (DAB-2201)  
**Tema:** Control de Acceso Basado en Roles (RBAC)  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Orlando Tec Ciau]  
**Matrícula:** [23531101]  
**Track:** [ Python/FastAPI | PHP 8+ ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Verificación manual de permisos de usuario (Admin/User)  
*Describe qué dificultad encontraste al procesar la petición HTTP o validar campos a mano:*
> Al intentar probar manualmente las rutas, me di cuenta de que no es solo "entrar o no entrar". Primero debía obtener el token para cada usuario, leer qué rol traía dentro y después decidir si podía acceder a cada ruta. Me costó distinguir cuándo debía devolver 401 y cuándo 403: si alguien no tiene token, no sé quién es → 401. Si ya sé quién es pero no tiene permiso → 403. Al principio confundía ambos códigos y eso hacía que las pruebas fallaran. También me costó entender que el rol viaja dentro del token, así que debo leerlo de ahí cada vez, no guardarlo en algún lado.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal (Contrato OpenAPI / DTO)
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | FastAPI 0.110+ con Python 3.10+, Google Colab. Usamos JWT para tokens y Pydantic para validar datos. Todo se ejecuta en el entorno de Colab sin instalaciones locales. |
| **Rol (R)** | Diseñador de seguridad de API: defino quién puede acceder a cada ruta, cómo se identifican los usuarios y qué respuesta reciben cuando no tienen permiso. Separo claramente autenticación de autorización. |
| **Especificación (E)** | Endpoint POST `/api/token` recibe usuario y contraseña, devuelve JWT con nombre y rol. Ruta `/api/perfil` accesible a cualquier usuario autenticado. Ruta `/api/admin/panel` solo accesible con rol `admin`: usuario normal recibe 403, sin token recibe 401. |
| **Output (O)** | Archivo `app.py` modular con modelos Pydantic, generación/validación de JWT, dependencia para obtener el usuario actual, códigos HTTP semánticos (200, 401, 403) y encabezados de seguridad correctos. |

### 2.2 Prompt Estructurado
```text
[Implementa Control de Acceso Basado en Roles (RBAC) en FastAPI. Crea dos usuarios: admin con contraseña admin123 y rol admin, y ana con contraseña secreta y rol user. El endpoint POST /api/token recibe usuario y contraseña en formato JSON y devuelve un JWT que incluye el nombre de usuario y su rol. Protege GET /api/perfil para que cualquier usuario autenticado pueda verlo. Protege GET /api/admin/panel para que SOLO el rol admin pueda entrar: si un usuario normal intenta entrar, devuelve HTTP 403 Forbidden con un mensaje claro. Si no hay token, devuelve HTTP 401 Unauthorized con el encabezado WWW-Authenticate: Bearer. Usa HS256 para firmar los tokens y obtén la clave secreta desde la variable de entorno JWT_SECRET_KEY. Devuelve solo el código completo en un archivo app.py.]
```

---

## 3. Código Devuelto por el Modelo
```python
# 
---

## 3. Código Devuelto por el Modelo

```python
import os
from datetime import datetime, timedelta, timezone
from fastapi import FastAPI, Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer
from pydantic import BaseModel
from jose import jwt, JWTError

app = FastAPI(title="API con Control de Acceso por Roles", version="1.0.0")

SECRET_KEY = os.getenv("JWT_SECRET_KEY")
if not SECRET_KEY:
    raise RuntimeError("Falta configurar JWT_SECRET_KEY")

ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30

USUARIOS = {
    "admin": {"password": "admin123", "rol": "admin"},
    "ana": {"password": "secreta", "rol": "user"}
}

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="/api/token")

class LoginData(BaseModel):
    usuario: str
    password: str

def crear_token(usuario: str, rol: str):
    ahora = datetime.now(timezone.utc)
    expiracion = ahora + timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    datos_token = {
        "usuario": usuario,
        "rol": rol,
        "exp": expiracion
    }
    return jwt.encode(datos_token, SECRET_KEY, algorithm=ALGORITHM)

def obtener_usuario_actual(token: str = Depends(oauth2_scheme)):
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        usuario = payload.get("usuario")
        rol = payload.get("rol")
        if not usuario or not rol:
            raise HTTPException(
                status_code=401,
                detail="Token inválido",
                headers={"WWW-Authenticate": "Bearer"}
            )
        return {"usuario": usuario, "rol": rol}
    except JWTError:
        raise HTTPException(
            status_code=401,
            detail="Token inválido o expirado",
            headers={"WWW-Authenticate": "Bearer"}
        )

@app.post("/api/token")
def login(datos: LoginData):
    if datos.usuario not in USUARIOS or USUARIOS[datos.usuario]["password"] != datos.password:
        raise HTTPException(
            status_code=401,
            detail="Credenciales incorrectas",
            headers={"WWW-Authenticate": "Bearer"}
        )
    rol = USUARIOS[datos.usuario]["rol"]
    access_token = crear_token(datos.usuario, rol)
    return {"access_token": access_token, "token_type": "bearer"}

@app.get("/api/perfil")
def ver_perfil(usuario_actual = Depends(obtener_usuario_actual)):
    return {
        "usuario": usuario_actual["usuario"],
        "rol": usuario_actual["rol"]
    }

@app.get("/api/admin/panel")
def panel_admin(usuario_actual = Depends(obtener_usuario_actual)):
    if usuario_actual["rol"] != "admin":
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="No tienes permiso para acceder a este recurso"
        )
    return {"panel": "admin"}

```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness HTTP)
*Pruebas ejecutadas:* Pruebas de acceso denegado por rol insuficiente (HTTP 403)

* [ x ] Pruebas de acceso denegado por rol insuficiente → HTTP 403
* [ x ] Respuestas semánticas correctas (200 OK, 401 Unauthorized, 403 Forbidden)
* [ x ] No existen respuestas HTTP 500 Internal Server Error no capturadas
* [ x ] Encabezados `Content-Type: application/json` correctamente configurados

### Reporte Crítico
*¿Qué código de estado HTTP o falla de seguridad omitió la IA y cómo lo corregiste?*
> La primera versión que recibí de la IA devolvía 403 Forbidden tanto cuando un usuario normal intentaba entrar al panel como cuando alguien no enviaba ningún token. Eso es incorrecto: sin token debe ser 401. También omitía incluir el encabezado `WWW-Authenticate: Bearer` en las respuestas 401, lo cual es necesario para que el cliente sepa cómo autenticarse. Lo corregí asegurando que la validación del token ocurra primero (devuelve 401 si falla) y la validación del rol ocurra después, solo con usuarios ya autenticados (devuelve 403 si no tienen permiso). Agregué manualmente el parámetro `headers={"WWW-Authenticate": "Bearer"}` en cada excepción 401.

---
**Firma digital:** [Orlando Tec Ciau]
