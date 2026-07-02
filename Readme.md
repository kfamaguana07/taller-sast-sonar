# ESPAM Monorepo — SAST con SonarQube

## Requisitos

- Docker y Docker Compose instalados
- Git
- Cuenta en GitHub

---

## 1. Configurar variables de entorno

Crear archivo `.env` en la raíz del proyecto con:

```env
REPO_URL=https://github.com/tu-usuario/tu-repo
RUNNER_TOKEN=tu-token-de-registro
```

> `REPO_URL` es la URL de tu repo en GitHub.  
> `RUNNER_TOKEN` se obtiene en: _Settings → Actions → Runners → New self-hosted runner_.

---

## 2. Levantar todos los servicios

```bash
docker-compose up --build -d
```

Verificar que todos los contenedores estén `Up`:

```bash
docker-compose ps
```

---

## 3. Configurar SonarQube

1. Abrir `http://localhost:9000`
2. Login con **admin / admin**
3. Cambiar la contraseña cuando lo pida
4. Ir a **Administration → Security → Users**
5. Generar un **token** de análisis (seleccionar `auth-service`, `products`, `frontend`, `analisis-vulnerabilidades`)
6. Copiar el token

---

## 4. Agregar token a GitHub Secrets

En tu repo en GitHub:

1. Ir a **Settings → Secrets and variables → Actions**
2. Crear un **New repository secret** llamado `SONAR_TOKEN`
3. Pegar el token generado en SonarQube

---

## 5. Registrar el runner en GitHub

Una vez levantado el `github-runner`, automáticamente se registra con las credenciales del `.env`.

Si falla, reiniciar el runner:

```bash
docker-compose restart github-runner
```

Verificar que aparezca como **Idle** en: _Settings → Actions → Runners_.

---

## 6. Disparar el análisis

El workflow se ejecuta automáticamente al hacer push a `main`, `master` o `develop`.

Para probarlo manualmente, hacer un push cualquiera:

```bash
git add .
git commit -m "trigger sonar analysis"
git push
```

O ir a **Actions → SonarQube Analysis → Run workflow**.

---

## 7. Ver resultados

Cada app crea su proyecto en SonarQube:

| App | Project Key | URL |
|---|---|---|
| auth-service | `auth-service` | http://localhost:9000/dashboard?id=auth-service |
| products | `products` | http://localhost:9000/dashboard?id=products |
| frontend | `frontend` | http://localhost:9000/dashboard?id=frontend |
| analisis-vulnerabilidades | `analisis-vulnerabilidades` | http://localhost:9000/dashboard?id=analisis-vulnerabilidades |

---

## 8. Comandos útiles

```bash
# Logs de SonarQube
docker-compose logs -f sonarqube

# Logs del runner
docker-compose logs -f github-runner

# Detener todo
docker-compose down

# Detener y borrar volúmenes (borra BD y configuraciones)
docker-compose down -v
```

---

## 9. Aplicaciones del proyecto

| Servicio | Puerto | Descripción |
|---|---|---|
| auth-service | 3000 | API de autenticación (NestJS) |
| product-service | 3001 | API de productos (NestJS) |
| frontend | 80 | UI React |
| sonarqube | 9000 | Servidor SonarQube |

Usuarios de prueba del frontend:

| Rol | Email | Contraseña |
|---|---|---|
| Operador | admin@example.com | Admin12345 |
| Cliente | cliente@example.com | Cliente123 |
