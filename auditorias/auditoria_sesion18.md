# Bitácora de Requerimientos y Auditoría IA — Sesión 18
**Materia:** Desarrollo de Aplicaciones Web (DAB-2201)  
**Tema:** Rate Limiting, Throttling y Prevención de Abuso  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Orlando Tec Ciau]  
**Matrícula:** [23531101]  
**Track:** [ Python/FastAPI | PHP 8+ ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Contador manual de peticiones por IP en memoria  
*Describe qué dificultad encontraste al procesar la petición HTTP o validar campos a mano:*
> La dificultad fue controlar las peticiones de cada cliente y calcular correctamente el tiempo de espera. También fue necesario evitar que un cliente afectara el límite de los demás.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal (Contrato OpenAPI / DTO)
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | Python con FastAPI en Google Colab. |
| **Rol (R)** | Desarrollador backend especializado en APIs y seguridad web. |
| **Especificación (E)** | Permitir tres peticiones por IP cada 60 segundos y responder 429 en la cuarta, incluyendo Retry-After. |
| **Output (O)** | Archivo app.py funcional, respuestas JSON y pruebas HTTP correctas. |

### 2.2 Prompt Estructurado
```text
[Actúa como desarrollador backend especializado en FastAPI y seguridad web. Implementa un límite de tres peticiones por cliente en una ventana de 60 segundos para el endpoint GET /api/recurso. Las primeras tres peticiones deben responder HTTP 200 y la cuarta HTTP 429, incluyendo el encabezado Retry-After. Utiliza contadores independientes por IP, almacenamiento en memoria y respuestas JSON. El código debe cumplir los criterios de verificación.

]
```

---

## 3. Código Devuelto por el Modelo
```
%%writefile app.py

from fastapi import FastAPI, Request
from fastapi.responses import JSONResponse
import time
import math
import threading

app = FastAPI(
    title="API con Rate Limiting",
    version="1.0.0"
)

# Configuración del límite
LIMITE_PETICIONES = 3
VENTANA_SEGUNDOS = 60

# Almacena el contador de cada cliente
contadores = {}

# Protege el acceso al contador
bloqueo = threading.Lock()


@app.get("/api/recurso")
def obtener_recurso(request: Request):

    # Identificar al cliente mediante su IP
    cliente = request.client.host if request.client else "desconocido"

    # Obtener el tiempo actual
    ahora = time.monotonic()

    with bloqueo:

        # Consultar si el cliente ya tiene un contador
        if cliente not in contadores:
            contadores[cliente] = {
                "inicio": ahora,
                "peticiones": 0
            }

        registro = contadores[cliente]

        # Calcular cuánto tiempo ha pasado
        tiempo_transcurrido = ahora - registro["inicio"]

        # Reiniciar el contador si terminó la ventana
        if tiempo_transcurrido >= VENTANA_SEGUNDOS:
            registro["inicio"] = ahora
            registro["peticiones"] = 0

        # Comprobar si alcanzó el límite
        if registro["peticiones"] >= LIMITE_PETICIONES:

            tiempo_restante = (
                VENTANA_SEGUNDOS -
                (ahora - registro["inicio"])
            )

            retry_after = max(1, math.ceil(tiempo_restante))

            return JSONResponse(
                status_code=429,
                content={
                    "error": "límite de peticiones alcanzado"
                },
                headers={
                    "Retry-After": str(retry_after)
                }
            )

        # Registrar la petición permitida
        registro["peticiones"] += 1

    # Respuesta normal
    return {
        "mensaje": "Petición permitida",
        "estado": "correcto"
    }

```
==============================================================================

VERIFICACIÓN DEL RESULTADO · 4 casos · app.py

==============================================================================

✅ 01. Primera petición permitida

✅ 02. Segunda petición permitida

✅ 03. Tercera petición permitida

✅ 04. La cuarta petición se limita

------------------------------------------------------------------------------

RESULTADO: 4/4 casos correctos. ¡Resultado logrado!

---

## 4. Fase 3: El Auditor (Resultados del Test Harness HTTP)
*Pruebas ejecutadas:* Pruebas de ráfaga masiva y bloqueo temporal (HTTP 429)

* [ x ] Primera petición: HTTP 200 OK.
* [ x ] Segunda petición: HTTP 200 OK.
* [ x ] Tercera petición: HTTP 200 OK.
* [ x ] Cuarta petición: HTTP 429 Too Many Requests.
* [ x ] Se incluye el encabezado Retry-After.
* Las respuestas utilizan formato JSON.

### Reporte Crítico
*¿Qué código de estado HTTP o falla de seguridad omitió la IA y cómo lo corregiste?*
> La verificación permitió comprobar que era necesario controlar correctamente la cuarta petición y agregar el encabezado Retry-After. Se corrigió mediante un contador por IP y el cálculo del tiempo restante de la ventana de 60 segundos.

---
**Firma digital:** [Orlando Tec Ciau]
