# Hito 2 · Capas y seguridad

**Sesión 20** · Peso: **7.5%** · Etiqueta: `v0.2-hito2`

> **Resultado:** la misma API del Hito 1, ahora con capas separadas, inyección de dependencias, registro/login con JWT y roles `user`/`admin`. Diez pruebas en verde.

---

## 1. Qué debe existir al final

```text
POST /api/registro   {"usuario":"ana","password":"secreta"} -> 201
POST /api/token      {"usuario":"ana","password":"secreta"} -> 200 + access_token
GET  /api/[padres]   Authorization: Bearer <token>           -> 200 (solo los míos)
GET  /api/[padres]   sin token                               -> 401
DELETE /api/[hijos]/{id}  dueño                              -> 204
DELETE /api/[hijos]/{id}  otro usuario                       -> 403
GET  /api/[padres]   admin                                   -> 200 (todos)
```

## 2. Qué construir

### Capas

```text
src/
├── routers/        # recibe HTTP y valida el contrato
├── services/       # reglas de negocio
├── repositories/   # acceso a datos (todavía en memoria)
├── models/         # entidades internas
├── schemas/        # DTOs de entrada y salida
└── core/           # configuración, seguridad y dependencias
```

El router no debe saber cómo se guardan los datos. El repositorio no debe importar FastAPI.

### Seguridad

- `POST /api/registro`: guarda `password_hash` con `bcrypt` o `argon2`; nunca devuelve la contraseña ni el hash.
- `POST /api/token`: valida credenciales y emite un JWT con `sub`, `rol` y `exp`.
- `GET /api/[padres]`: exige Bearer; un `user` ve solo sus registros; un `admin` ve todos.
- `DELETE /api/[hijos]/{id}`: solo el dueño o un admin; si no, **403**.
- Sin token o con token inválido: **401**, no 403.

### Inyección de dependencias

- Una función `get_repositorio()` (o equivalente) que devuelve la implementación.
- Los endpoints reciben la dependencia con `Depends`.
- El repositorio puede sustituirse en pruebas sin reescribir el endpoint.

## 3. Criterios de aceptación

- [ ] Registro responde 201 y no filtra contraseña ni hash.
- [ ] Login correcto responde 200 con `access_token` y `token_type`.
- [ ] Login incorrecto responde 401.
- [ ] Ruta protegida sin token responde 401.
- [ ] Token alterado responde 401.
- [ ] El dueño puede eliminar su registro; otro usuario recibe 403.
- [ ] El admin puede ver todos los registros; el usuario solo los suyos.
- [ ] Las capas están separadas en archivos distintos.
- [ ] `Depends` se usa en al menos un endpoint.
- [ ] Las **diez pruebas** quedan en verde.
- [ ] Tag `v0.2-hito2` creado.

## 4. Las diez pruebas

| # | Caso | Resultado esperado |
| :---: | :--- | :--- |
| 1 | Registro válido | 201 sin contraseña |
| 2 | Registro duplicado | 409 o 400 |
| 3 | Login correcto | 200 + token |
| 4 | Login incorrecto | 401 |
| 5 | Perfil/ruta protegida con token | 200 |
| 6 | Ruta protegida sin token | 401 |
| 7 | Token alterado | 401 |
| 8 | Dueño elimina su hijo | 204 |
| 9 | Otro usuario intenta eliminar | 403 |
| 10 | Admin lista todos | 200 |

## 5. Entregables

- [ ] Capas separadas en `src/`.
- [ ] Registro/login con hash y JWT.
- [ ] Roles y 401/403 correctos.
- [ ] `docs/decisiones.md` con la página del Hito 2:
  - Cómo separé las capas.
  - Dónde vive la seguridad.
  - Qué dependencia inyecté y por qué.
- [ ] Diez pruebas en verde.
- [ ] Tag `v0.2-hito2`.
- [ ] Checkpoint de la sesión 25 preparado.

## 6. Demo de 3 minutos

1. Registrarse.
2. Iniciar sesión y mostrar el token.
3. Acceder a la ruta protegida con token.
4. Quitar el token y mostrar el 401.
5. Intentar eliminar un recurso ajeno y mostrar el 403.
6. Entrar como admin y listar todos.
7. Mostrar la salida de las diez pruebas.

## 7. Errores comunes que bajan la nota

- Guardar contraseñas en texto plano o usar MD5/SHA.
- Devolver 403 cuando falta el token (debe ser 401).
- Devolver 500 con un token mal formado.
- Meter la lógica de negocio dentro del router.
- Crear el repositorio dentro del endpoint en lugar de inyectarlo.
- No documentar el rol en el token o no validarlo.
- Pruebas que no cubren el caso de otro usuario.

## 8. Autoevaluación rápida

| Pregunta | Sí / No |
| :--- | :---: |
| ¿Puedo cambiar el repositorio sin tocar el router? | [ ] |
| ¿El token incluye rol y expiración? | [ ] |
| ¿La API distingue 401 de 403? | [ ] |
| ¿La contraseña nunca aparece en una respuesta? | [ ] |
| ¿Mis pruebas cubren dueño, intruso y admin? | [ ] |

Si alguna respuesta es “no”, aún no está listo para el Hito 2.
