# Telo

Documentación del proyecto hotelero: primer arranque, conexión a PostgreSQL y diseño técnico de la V1.

## Requisitos

Instalar JDK 21 y configurar `JAVA_HOME`. Maven Wrapper descarga Maven y sus dependencias durante la primera ejecución; necesita acceso a Internet. No es necesario instalar Maven por separado.

## Primer arranque sin base de datos

Desde PowerShell, en la carpeta del proyecto:

```powershell
cd C:\262dev\telo
.\mvnw.cmd '-Dspring-boot.run.profiles=local' spring-boot:run
```

Si el proyecto está en otra carpeta, ajustar la ruta. En Linux/macOS:

```bash
./mvnw -Dspring-boot.run.profiles=local spring-boot:run
```

Abrir <http://localhost:8080/> y comprobar el saludo. Detener con `Ctrl+C`.

El perfil `local` desactiva la autoconfiguración de base de datos: no requiere PostgreSQL, Supabase ni credenciales.

## Conectar desarrollo

Seguir el [manual de Supabase](supabase.md) y revisar `application-dev.properties`. Después de detener la instancia local:

```powershell
.\mvnw.cmd spring-boot:run
```

Sin indicar perfil se utiliza `dev`, que necesita conexión a la base de desarrollo.

| Perfil | Uso |
| --- | --- |
| `local` | Hola mundo sin base de datos. |
| `dev` | Desarrollo con PostgreSQL en Supabase; predeterminado. |
| `prod` | Producción, pendiente de configurar con variables `PROD_DB_*`. |

Activar un solo perfil de entorno a la vez.

## Pruebas

```powershell
.\mvnw.cmd test
```

Las pruebas actuales utilizan `local` y no requieren base de datos.

## Documentos del proyecto

- [Alcance y diseño técnico V1](alcance-v1.md), incluidos diagramas de clases.
- [Creación y conexión de la base de datos](supabase.md).
- [Publicación de esta documentación en GitHub Pages](publicar-documentacion.md).

La aplicación contiene el arranque inicial y la configuración. Las funcionalidades hoteleras están en fase de diseño.
