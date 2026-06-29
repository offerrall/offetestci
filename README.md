# offetestci

Paquete de prueba para validar el flujo completo de CI y publicación
automática a PyPI vía GitHub Actions (trusted publishing, sin tokens).
No tiene funcionalidad real; existe solo para comprobar que el pipeline
funciona de principio a fin.

## Publicación a PyPI — registro previo (importante)

El workflow de publicación (`.github/workflows/publish.yml`) sube el
paquete usando **trusted publishing**: GitHub se autentica ante PyPI con
un token OIDC temporal, sin guardar ningún secreto en el repo.

Para que eso funcione, PyPI tiene que confiar en este repo **de antemano**.
Como el paquete aún no existe en PyPI, se registra como *pending publisher*:

1. Entra en [pypi.org](https://pypi.org) (con 2FA activado).
2. Ve a los ajustes de tu cuenta → **Publishing**
   (no dentro de un proyecto, sino en la cuenta).
3. Añade un *pending publisher* de tipo **GitHub** con estos datos exactos:

   | Campo | Valor |
   |---|---|
   | PyPI Project Name | `offetestci` |
   | Owner | `offerrall` |
   | Repository name | `offetestci` |
   | Workflow name | `publish.yml` |
   | Environment | *(vacío)* |

4. Guarda.

> **El error típico:** "Workflow name" es el nombre del **fichero**
> (`publish.yml`), no el `name:` que pone dentro del YAML. Si pones el de
> dentro, PyPI rechaza la publicación con un error poco claro.

Una vez registrado, la primera publicación crea el proyecto en PyPI y
establece el trusted publisher de golpe.

## Publicar

1. Sube la versión en `src/offetestci/__init__.py`.
2. En GitHub → **Releases** → crea un release con su tag (ej. `v0.1.0`).
3. El workflow `publish.yml` se dispara solo: verifica, construye y sube.

Tras el registro previo, todo el flujo es desde GitHub — sin tokens, sin
publicar desde tu máquina.