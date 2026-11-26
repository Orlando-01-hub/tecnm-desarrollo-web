# Hito 4 · Producto y demo final

**Sesión 40** · Peso: **7.5%** · Etiqueta: `v1.0-final`

> **Resultado:** una SPA de una página que consume la API, un Dockerfile reproducible, variables de entorno documentadas y una demo de 5 minutos con el flujo completo.

---

## 1. Qué debe existir al final

```text
GET  /                  -> SPA: login, lista, formulario y eliminar
GET  /docs              -> OpenAPI
GET  /health            -> {"status":"ok"}

Flujo E2E:
registro -> login -> crear principal -> crear hijo -> listar -> eliminar -> 401/403

docker build -t proyecto .
docker run --rm -p 8000:8000 --env-file .env proyecto
```

## 2. Qué construir

### SPA mínima

Una sola página con:

- Formulario de login (usuario y contraseña).
- Lista de registros principales.
- Formulario para crear un registro principal.
- Lista de hijos del registro seleccionado.
- Formulario para crear un hijo.
- Botón eliminar con confirmación.
- Manejo visible de errores 401, 403 y 422.

Sin React, Vue, Angular ni build tools. HTML, CSS y JavaScript vanilla con `fetch`.

### Dockerfile

Debe:

- Usar una versión fija de Python slim.
- Copiar e instalar dependencias antes de copiar el código.
- Ejecutarse con un usuario sin privilegios.
- Arrancar con `uvicorn` y escuchar en `0.0.0.0`.
- No copiar `.env` ni secretos.

Ejemplo base (adáptalo a tu estructura):

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
RUN useradd -m appuser && chown -R appuser /app
USER appuser
EXPOSE 8000
CMD ["uvicorn", "src.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### Variables de entorno

`.env.example` debe documentar, sin valores reales:

```bash
DATABASE_URL=sqlite:///proyecto.db
SECRET_KEY=cambia-esta-clave
ENTORNO=dev
PORT=8000
```

### Despliegue o demo reproducible

Elige una opción:

1. **Codespaces:** abre el puerto 8000 en público durante la demo.
2. **Render / plataforma similar:** despliega el servicio y usa la URL pública.
3. **Local + video:** si la nube falla, graba un video de 5 minutos con el mismo guion.

La URL pública es **opcional**. Lo obligatorio es que la demo sea reproducible: cualquiera debe poder levantar el proyecto con el README, el Dockerfile y el `.env.example`.

## 3. Criterios de aceptación

- [ ] La SPA permite iniciar sesión y guarda el token en memoria o `sessionStorage`.
- [ ] La lista se carga desde la API con `fetch` y `await response.json()`.
- [ ] Se puede crear un registro principal y un hijo desde la interfaz.
- [ ] Se puede eliminar un hijo; la lista se actualiza sin recargar la página.
- [ ] Un error 401 redirige al login o muestra un mensaje claro.
- [ ] Un 403 y un 422 muestran un mensaje comprensible.
- [ ] `docker build` y `docker run` funcionan con `.env.example`.
- [ ] El contenedor no corre como root y no incluye `.env`.
- [ ] El flujo E2E mínimo está documentado y pasa.
- [ ] El README explica instalación, variables, migración, pruebas y demo.
- [ ] El tag `v1.0-final` existe y apunta al commit de la demo.

## 4. Flujo E2E mínimo

| # | Paso | Resultado esperado |
| :---: | :--- | :--- |
| 1 | Registro | 201 y usuario creado |
| 2 | Login | 200 y token |
| 3 | Crear principal | 201 |
| 4 | Crear hijo y listar | 201 y 200 con el hijo |
| 5 | Eliminar hijo | 204 y lista sin el hijo |
| 6 | Acceso sin token | 401 |
| 7 | Acceso a recurso ajeno | 403 |

Puede ser una colección Postman/Newman o un script `pytest` con `httpx`.

## 5. Entregables

- [ ] SPA en `proyecto_integrador/frontend/`.
- [ ] Dockerfile y `.env.example`.
- [ ] README actualizado con demo y despliegue.
- [ ] `docs/decisiones.md` con la página del Hito 4:
  - Qué aprendí al integrar el frontend.
  - Qué problema de despliegue resolví.
  - Qué haría con una semana más (sin implementarlo).
- [ ] Flujo E2E en verde.
- [ ] Tag `v1.0-final`.
- [ ] Demo de 5 minutos ensayada.

## 6. Guion de la demo (5 minutos)

1. **0:00–0:30** — Saludo y una frase del problema.
2. **0:30–1:00** — `/health` y `/docs`.
3. **1:00–2:00** — Registro y login en la SPA.
4. **2:00–3:00** — Crear principal y listar.
5. **3:00–3:40** — Crear hijo y eliminarlo.
6. **3:40–4:20** — Provocar un 401 y un 403; explicar la diferencia.
7. **4:20–5:00** — Pruebas E2E, tag `v1.0-final` y cierre.

Ambos integrantes deben hablar al menos una vez.

## 7. Errores comunes que bajan la nota

- Una SPA que recarga la página en cada acción.
- Guardar el token en `localStorage` sin explicar el riesgo.
- Un Dockerfile que copia `.env` o corre como root.
- Un README que no permite levantar el proyecto desde cero.
- Una demo que solo muestra Swagger y no la interfaz.
- Un video que no corresponde al commit etiquetado.
- Un 401 tratado como 403 o un error silencioso.

## 8. Checklist final

- [ ] `pytest` o Newman en verde.
- [ ] `/health`, `/docs` y la SPA responden.
- [ ] Docker build/run probado en limpio.
- [ ] `.env.example` completo y `.env` fuera del repositorio.
- [ ] README con instalación, migración, pruebas y demo.
- [ ] Tag `v1.0-final` empujado.
- [ ] Video de respaldo grabado (opcional).
- [ ] Demo ensayada con cronómetro.
- [ ] Ambos integrantes pueden explicar cualquier parte.
