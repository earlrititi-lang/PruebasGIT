# Guia de buenas practicas de Git: `origin`, `main`, `origin/main` y flujo de ramas

## 1) Conceptos clave

### `origin`
- Es el nombre por defecto del remoto principal.
- Normalmente apunta al repositorio en GitHub, GitLab, etc.
- Se puede ver con:

```bash
git remote -v
```

Cuando ejecutas `git remote -v` suelen aparecer dos lineas por remoto:
- `(fetch)`: URL que Git usa para **descargar** cambios del remoto a tu repositorio local (`git fetch`, `git pull`).
- `(push)`: URL que Git usa para **subir** tus commits locales al remoto (`git push`).

En muchos proyectos ambas URLs son iguales, pero pueden ser distintas (por ejemplo, lectura por HTTPS y escritura por SSH).

### `main`
- Es una rama **local** de tu repositorio.
- Vive en tu maquina.
- Puedes tener cambios no subidos en `main`.

### `origin/main`
- Es la referencia local que representa el estado de la rama `main` en el remoto `origin`.
- Se actualiza cuando haces `git fetch` o `git pull`.
- No es una rama donde trabajes directamente, es una referencia remota.

## 2) Diferencia rapida

- `main`: tu rama local actual.
- `origin/main`: "ultima foto conocida" de `main` en el remoto.
- `origin`: servidor remoto (el "donde subes y bajas").

## 2.1) Diferencia entre `git fetch` y `git pull`

- `git fetch`: descarga cambios del remoto y actualiza referencias remotas (como `origin/main`), pero **no** integra esos cambios en tu rama actual ni modifica tus archivos.
- `git pull`: hace `fetch` + integracion en tu rama actual (merge o rebase segun configuracion), por lo que **si** puede cambiar tus archivos.

Regla practica:
- Usa `fetch` si quieres revisar primero.
- Usa `pull` si quieres traer e integrar directamente.

## 3) Flujo recomendado (equipo o proyecto serio)

### Regla general
- `main` debe estar siempre estable y deployable.
- El trabajo diario se hace en ramas auxiliares (feature, fix, chore, hotfix).

### Pasos
1. Actualiza referencias remotas:

```bash
git fetch origin
```

2. Cambia a `main` y sincronizala:

```bash
git switch main
git pull --ff-only origin main
```

3. Crea una rama auxiliar desde `main` actualizada:

```bash
git switch -c feature/nombre-corto
```

4. Haz cambios y confirma en commits pequenos:

```bash
git add .
git commit -m "feat: descripcion clara del cambio"
```

5. Sube tu rama:

```bash
git push -u origin feature/nombre-corto
```

6. Abre Pull Request hacia `main` y revisa.
7. Fusiona por PR (evita commits directos en `main`).
8. Tras fusionar, limpia local:

```bash
git switch main
git pull --ff-only origin main
git branch -d feature/nombre-corto
```

## 4) Reglas de oro

- No trabajes directamente en `main` salvo cambios minimos urgentes.
- Haz `fetch/pull` antes de empezar una tarea nueva.
- Commits pequenos, con mensaje claro y verbo de accion.
- Una rama por tarea.
- Evita `git push --force` en ramas compartidas.
- Antes de subir: revisa `git status`.
- Antes de mezclar: revisa `git log --oneline --graph --decorate --all`.

## 5) Comandos utiles del dia a dia

```bash
git status
git branch -vv
git fetch origin
git pull --ff-only
git switch main
git switch -c feature/nueva-funcion
git log --oneline --graph --decorate --all
```

## 6) Recuperar estado remoto cuando te equivocas

### Sobrescribir un archivo local con lo del remoto

```bash
git fetch origin
git restore --source=origin/main -- ruta/del/archivo
```

### Sobrescribir toda tu rama local con remoto (peligroso)

```bash
git fetch origin
git reset --hard origin/main
git clean -fd
```

Usa lo anterior solo si sabes que quieres perder cambios locales no guardados en commit.

## 7) Convencion recomendada de nombres de ramas

- `feature/...` para nuevas funcionalidades.
- `fix/...` para correcciones.
- `hotfix/...` para urgencias en produccion.
- `chore/...` para mantenimiento.
- `docs/...` para documentacion.

Ejemplos:
- `feature/login-con-google`
- `fix/error-validacion-email`
- `docs/guia-instalacion`

## 8) Resumen corto

- `origin` = remoto.
- `main` = rama local principal.
- `origin/main` = estado remoto de `main` traido a local.
- Flujo sano: actualizar `main` -> crear rama auxiliar -> commit -> push -> PR -> merge -> limpiar rama.
