# Bitácora de Requerimientos y Auditoría IA — Sesión 14
**Materia:** Desarrollo de Aplicaciones Web (DAB-2201)  
**Tema:** Hashing Seguro de Contraseñas (Bcrypt / Argon2)  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Orlando Tec Ciau]  
**Matrícula:** [23531101]  
**Track:** [ Python/FastAPI | PHP 8+ ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Cifrado manual con Salting y Factor de Costo  
*Describe qué dificultad encontraste al procesar la petición HTTP o validar campos a mano:*
> La principal dificultad fue comprender cómo generar un hash seguro para las contraseñas y cómo utilizar un salt para protegerlas. También fue necesario validar los datos recibidos y comprobar que la contraseña ingresada coincidiera con la almacenada, sin tener que guardar la contraseña original. Hacer todo esto manualmente sería más complicado y podría generar errores de seguridad.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal (Contrato OpenAPI / DTO)
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | Python 3.13, FastAPI y Pydantic en Google Colab. |
| **Rol (R)** | 	Arquitecto de software especializado en APIs REST y seguridad web. |
| **Especificación (E)** | Implementar registro y login utilizando Bcrypt, salt aleatorio, verificación de contraseñas y códigos HTTP 201, 200, 401 y 409. |
| **Output (O)** | Archivo app.py con modelos Pydantic, rutas de registro y login, almacenamiento de hashes y respuestas sin información sensible. |

### 2.2 Prompt Estructurado
```text
[Actúa como desarrollador backend especializado en Python, FastAPI y seguridad web.
Necesito implementar una API de registro e inicio de sesión utilizando Bcrypt para proteger las contraseñas.

La API debe cumplir los siguientes requisitos:
Crear la ruta POST /api/registro que reciba usuario y password.
Generar un hash seguro utilizando Bcrypt y un salt aleatorio.
Guardar únicamente el hash, nunca la contraseña original.
Evitar que un usuario repetido sobrescriba su contraseña.
Crear la ruta POST /api/login para verificar las credenciales mediante checkpw().
Responder HTTP 201 al registrar, HTTP 200 al autenticar correctamente y HTTP 401 cuando las credenciales sean incorrectas.
No devolver la contraseña ni el hash en ninguna respuesta.

Utiliza FastAPI, Pydantic y Bcrypt. Entrega el código completo en app.py con validaciones y manejo de errores HTTP.]
```

---

## 3. Código Devuelto por el Modelo
```python
# from fastapi import FastAPI, HTTPException
from pydantic import BaseModel, Field, ConfigDict
from bcrypt import hashpw, gensalt, checkpw

# 1. Crear la aplicación FastAPI
app = FastAPI(
    title="API de Registro y Login Seguro",
    version="1.0.0"
)

# 2. Almacenamiento temporal de usuarios
# Aquí se guardará el nombre y el hash de cada contraseña.
usuarios_hash = {}


# 3. Modelo para recibir los datos del usuario
class UsuarioEntrada(BaseModel):
    model_config = ConfigDict(extra="forbid")

    usuario: str = Field(min_length=1)
    password: str = Field(min_length=1)


# 4. Modelo de respuesta del registro
class RegistroRespuesta(BaseModel):
    usuario: str


# 5. Modelo de respuesta del login
class LoginRespuesta(BaseModel):
    usuario: str
    autenticado: bool


# 6. Ruta para registrar usuarios
@app.post(
    "/api/registro",
    status_code=201,
    response_model=RegistroRespuesta
)
def registrar_usuario(datos: UsuarioEntrada):

    # Comprobar si el usuario ya existe
    if datos.usuario in usuarios_hash:
        raise HTTPException(
            status_code=409,
            detail="El usuario ya existe"
        )

    # Convertir la contraseña a bytes
    password_bytes = datos.password.encode("utf-8")

    # Generar un salt y crear el hash con Bcrypt
    password_hash = hashpw(
        password_bytes,
        gensalt()
    )

    # Guardar únicamente el hash
    usuarios_hash[datos.usuario] = password_hash

    # Responder sin mostrar contraseña ni hash
    return {
        "usuario": datos.usuario
    }


# 7. Ruta para iniciar sesión
@app.post(
    "/api/login",
    response_model=LoginRespuesta
)
def iniciar_sesion(datos: UsuarioEntrada):

    # Buscar el hash del usuario
    password_hash = usuarios_hash.get(datos.usuario)

    # Si el usuario no existe, rechazar el acceso
    if password_hash is None:
        raise HTTPException(
            status_code=401,
            detail="Credenciales incorrectas"
        )

    # Verificar la contraseña con Bcrypt
    password_correcta = checkpw(
        datos.password.encode("utf-8"),
        password_hash
    )

    # Si la contraseña no coincide, rechazar el acceso
    if not password_correcta:
        raise HTTPException(
            status_code=401,
            detail="Credenciales incorrectas"
        )

    # Si coincide, permitir el acceso
    return {
        "usuario": datos.usuario,
        "autenticado": True
    }

```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness HTTP)
*Pruebas ejecutadas:* Pruebas contra ataques de Rainbow Tables y Timing

* [ x ] Registro HTTP 201.
* [ x ] Login correcto HTTP 200.
* [ x ] Login incorrecto HTTP 401.
* [ x ] Uso de Bcrypt.
* [ x ] Protección de contraseña y hash.
* [ x ] No sobrescribir usuarios existentes.

### Reporte Crítico
*¿Qué código de estado HTTP o falla de seguridad omitió la IA y cómo lo corregiste?*
> Durante la revisión consideré importante evitar que un usuario repetido pudiera sobrescribir una contraseña existente. Para solucionarlo agregué una validación que comprueba si el usuario ya está registrado y devuelve HTTP 409. También utilicé checkpw() para verificar las contraseñas sin comparar hashes manualmente y evité mostrar información sensible en las respuestas.

---
**Firma digital:** [Orlando Tec Ciau]
