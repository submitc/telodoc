# Publicar documentación

La documentación se construye con MkDocs Material. GitHub Pages publica únicamente el sitio estático generado desde `docs/`; el workflow no despliega la aplicación Spring Boot ni conecta a la base de datos.

## Configurar GitHub Pages

1. Subir el proyecto a su repositorio en GitHub, con la rama `main`.
2. Ir a **Settings → Pages → Build and deployment**.
3. En **Source**, seleccionar **GitHub Actions**.
4. En **Actions**, abrir **Deploy MkDocs to GitHub Pages** y ejecutar **Run workflow** sobre `main`, o subir un cambio de documentación a esa rama.
5. Consultar la URL publicada en el entorno `github-pages` del despliegue.

El workflow calcula las URLs a partir del propietario y nombre del repositorio. Está preparado para una página de proyecto con formato `https://<propietario>.github.io/<repositorio>/`. Un dominio propio o un repositorio especial `<propietario>.github.io` requiere ajustar `SITE_URL`.

No se necesita una rama `gh-pages` ni un token personal: se utilizan los permisos de GitHub Actions y su despliegue oficial de Pages. Los pull requests solo validan; publican los cambios de `main`.

## Validar localmente

Además del JDK de la aplicación, instalar Python 3.12 para las herramientas de documentación. Desde PowerShell, en la raíz del proyecto:

```powershell
python -m venv .venv-docs
.\.venv-docs\Scripts\python.exe -m pip install -r requirements-docs.txt
.\.venv-docs\Scripts\python.exe -m mkdocs build --strict
.\.venv-docs\Scripts\python.exe -m mkdocs serve
```

Abrir la dirección que muestre MkDocs, normalmente <http://127.0.0.1:8000/>. Detener con `Ctrl+C`.

`site/` contiene la salida generada y no se versiona. El modo estricto detiene la publicación si la construcción produce advertencias. Los diagramas Mermaid se renderizan en el navegador mediante el soporte de Material.

## Mantener la documentación

- Añadir las nuevas páginas al menú `nav` de `mkdocs.yml`.
- Usar enlaces relativos entre archivos dentro de `docs/`.
- Mantener alineado el onboarding de `docs/index.md` con el README del repositorio.
- No copiar contraseñas ni archivos de configuración con secretos dentro de `docs/`.

## Referencias

- [Publicación con un workflow de GitHub Pages](https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages).
- [Diagramas Mermaid en Material](https://squidfunk.github.io/mkdocs-material/reference/diagrams/).
