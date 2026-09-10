# Manual: base de datos de Telo en Supabase

## 1. Crear el proyecto

1. Entrar en [Supabase](https://supabase.com/dashboard) e iniciar sesión.
2. Seleccionar una organización o crear una y pulsar **New project**.
3. Usar un nombre como `telo-dev`, elegir la región y definir una contraseña de base de datos. Guardarla en un gestor de contraseñas.
4. En **Security**, desmarcar **Automatically expose new tables** y **Enable Data API**. Telo se conecta a PostgreSQL mediante JDBC y no necesita la API REST de Supabase. **Enable automatic RLS** puede quedar desmarcado para este arranque; esa opción afecta a las tablas nuevas de `public`, mientras que Telo utilizará su propio esquema.
5. Crear el proyecto y esperar a que esté disponible.

Supabase provisiona una base PostgreSQL llamada `postgres`; se utilizará esa base con un esquema `telo`. No hace falta ejecutar `CREATE DATABASE`.

## 2. Preparar el esquema

En **SQL Editor**, ejecutar:

```sql
create schema if not exists telo;
```

Comprobarlo con:

```sql
select schema_name
from information_schema.schemata
where schema_name = 'telo';
```

Debe aparecer una fila con `telo`. Mantener este esquema fuera de los esquemas expuestos por la Data API: el backend accederá mediante JDBC. Esta entrega no necesita tablas; las de habitaciones, tarifas y reservas se crearán con migraciones en una etapa posterior.

## 3. Obtener los datos de conexión

En el panel **Connect to your project**, seguir esta ruta (según la interfaz mostrada en la captura):

1. Pulsar **Connect** desde el proyecto.
2. Seleccionar la pestaña **Direct — Connection string**, en la fila donde también aparecen Framework, Server, ORM y MCP.
3. En **Connection Method**, seleccionar **Session pooler** para una conexión desde una red IPv4. La pestaña **Direct** y el método **Direct connection** son opciones diferentes: estar en la pestaña Direct permite elegir Session pooler.
4. En **Type**, mantener **URI**, como aparece en la captura.
5. Desplazarse hacia abajo dentro del panel, hasta **Connection string**. Debajo de **Shared pooler** aparece **Connection parameters**, con `host`, `port`, `database` y `user`. Copiar cada valor con su botón o usar **Copy all**. No es necesario extraerlos de la cadena horizontal.
6. **Install Agent Skills (optional)** es opcional y no se necesita para arrancar Telo.

Para el proyecto `telo-dev` de la captura, los parámetros son:

| Parámetro | Valor |
| --- | --- |
| host | `aws-0-us-east-2.pooler.supabase.com` |
| port | `5432` |
| database | `postgres` |
| user | `postgres.qvshgxlrsakttvzvaiqm` |

La cadena tendrá una estructura similar a esta; es un ejemplo, no una dirección para copiar literalmente:

```text
postgresql://postgres.<referencia-del-proyecto>:[YOUR-PASSWORD]@<host-del-session-pooler>:5432/postgres
```

Extraer de la cadena los siguientes datos:

| Dato | Dónde encontrarlo | Propiedad de Telo |
| --- | --- | --- |
| Usuario | Entre `postgresql://` y `:[YOUR-PASSWORD]` | `spring.datasource.username` |
| Host | Después de `@` y antes del puerto | Se incorpora a `spring.datasource.url` |
| Puerto | Después del host; `5432` para Session pooler | Se incorpora a `spring.datasource.url` |
| Base de datos | Después del puerto y `/`; normalmente `postgres` | Se incorpora a `spring.datasource.url` |

Copiar el host y el usuario exactos que proporciona Supabase. La **Project URL** que comienza con `https://` no es la dirección JDBC de PostgreSQL. El paso 4 muestra cómo construir la URL JDBC y pasar la contraseña por separado.

El aviso **Only use session pooler on an IPv4 network** describe cuándo usar este método. Si el entorno tiene conectividad IPv6, se puede seleccionar **Direct connection** y usar los datos de esa opción; no mezclar su host o usuario con los del Session pooler. Este manual continúa con Session pooler para el acceso desde IPv4.

Se necesita la contraseña de PostgreSQL definida al crear el proyecto, no una API key de Supabase. Si se olvidó, restablecerla en la configuración de base de datos.

## 4. Ejecutar en desarrollo

El perfil predeterminado es `dev`. La URL, el usuario y la contraseña de desarrollo están configurados en texto plano en `src/main/resources/application-dev.properties`, tal como se solicitó para este entorno. No es necesario definir variables ni introducir la contraseña en cada arranque. Si se cambia la contraseña en Supabase, actualizar ese archivo.

Después de crear el esquema del paso 2, ejecutar desde la carpeta del proyecto:

```powershell
.\mvnw.cmd spring-boot:run
```

Detener previamente cualquier instancia que ocupe el puerto 8080. La configuración usa SSL, hasta cinco conexiones y el esquema `telo`. Hibernate utiliza `ddl-auto=validate`; Flyway queda deshabilitado hasta incorporar la primera migración.

Para arrancar sin base de datos:

```powershell
.\mvnw.cmd '-Dspring-boot.run.profiles=local' spring-boot:run
```

Los perfiles disponibles son `dev`, `prod` y `local`. Activar un solo perfil de entorno a la vez. `local` deshabilita la autoconfiguración de base de datos.

## 5. Preparar producción más adelante

`src/main/resources/application-prod.properties` está preparado sin credenciales de desarrollo. Antes de usarlo:

1. Crear la base de producción y su esquema `telo` en un proyecto independiente.
2. Configurar `PROD_DB_URL` (URL JDBC con `sslmode=require`), `PROD_DB_USERNAME` y `PROD_DB_PASSWORD` en el entorno donde se desplegará.
3. Incorporar las migraciones del dominio y configurar Flyway cuando estén disponibles.
4. Activar únicamente `prod`:

```powershell
.\mvnw.cmd '-Dspring-boot.run.profiles=prod' spring-boot:run
```

En un despliegue con JAR se puede definir `SPRING_PROFILES_ACTIVE=prod`. El perfil falla si faltan sus variables; no usa la base de desarrollo como alternativa. Spring Boot no carga archivos `.env` automáticamente en este proyecto.

## 6. Comprobar el arranque

Los registros deben indicar el perfil `dev` (predeterminado) o `prod` (si se activó explícitamente), la inicialización del pool Hikari y `Started TeloApplication`, sin errores de conexión. Abrir <http://localhost:8080/> y comprobar `Hola mundo`.

El saludo no ejecuta consultas. Con los perfiles de base de datos, JPA establece una conexión durante el arranque; con `local`, ver el saludo solo verifica el servidor web. Las pruebas automatizadas usan explícitamente `local` para no depender de Supabase.

## Problemas frecuentes

| Problema | Qué revisar |
| --- | --- |
| `password authentication failed` | Contraseña PostgreSQL y usuario completos del panel Connect. |
| `UnknownHostException` o tiempo de espera | Host copiado, red, restricciones de acceso del proyecto y uso de Session pooler en redes IPv4. |
| Variable `PROD_DB_*` sin resolver | Configurar las tres variables de producción antes de activar `prod`. Para desarrollo, utilizar `dev`. |
| `schema telo does not exist` | Ejecutar el SQL del paso 2 en el proyecto correcto. |
| Puerto 8080 ocupado | Detener la otra aplicación o añadir `'-Dspring-boot.run.arguments=--server.port=8081'` al comando. |

## Referencias oficiales

- [Seguridad de los datos](https://supabase.com/docs/guides/database/secure-data): se puede deshabilitar la Data API cuando el acceso se realiza mediante conexiones PostgreSQL.

- [Supabase con Spring Boot](https://supabase.com/docs/guides/getting-started/quickstarts/spring-boot): creación del proyecto, JDBC, SSL y esquema propio.
- [Conectar a PostgreSQL](https://supabase.com/docs/guides/database/connecting-to-postgres): conexión directa y poolers.
