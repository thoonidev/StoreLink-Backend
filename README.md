# StoreLink — Backend

API REST para una tienda en línea. Gestiona el catálogo de productos, clientes, pedidos y promociones, con autenticación JWT y permisos para administradores y clientes.

## Funcionalidades

- Gestión de productos y categorías, con búsqueda de productos.
- Registro de clientes e inicio de sesión con roles `ADMIN` y `CUSTOMER`.
- Creación y consulta de pedidos y sus artículos, cambios de estado y cancelaciones.
- Promociones por categoría con porcentaje de descuento y fechas de vigencia.
- Registro de pagos simulados y consulta de su estado.
- Administración del estado de las cuentas y documentación interactiva con Swagger.

## Tecnologías

Java 17 · Spring Boot 4.0.5 · Spring Security · Spring Data JPA · MySQL · Maven · JWT · BCrypt · Springdoc OpenAPI

## Instalación y configuración

Requiere **JDK 17** y **MySQL**. El repositorio incluye Maven Wrapper.

```bash
git clone https://github.com/thoonidev/StoreLink-Backend.git
cd StoreLink-Backend
```

Ajusta la conexión en `src/main/resources/application.properties`:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/storelink?createDatabaseIfNotExist=true
spring.datasource.username=tu_usuario
spring.datasource.password=tu_password
```

La conexión permite crear la base de datos si el usuario tiene permisos. Hibernate crea o actualiza las tablas al iniciar mediante `spring.jpa.hibernate.ddl-auto=update`. Mantén las credenciales reales fuera de los cambios que publiques.

## Ejecución

En Windows (PowerShell):

```powershell
.\mvnw.cmd spring-boot:run
```

En Linux o macOS:

```bash
./mvnw spring-boot:run
```

La API estará disponible en `http://localhost:8080/storelink/api` y Swagger en `http://localhost:8080/storelink/swagger-ui/index.html`.

Después del primer inicio, crea el rol necesario para registrar clientes si aún no existe:

```sql
USE storelink;
INSERT INTO role_details (role_name)
SELECT 'CUSTOMER'
WHERE NOT EXISTS (
    SELECT 1 FROM role_details WHERE role_name = 'CUSTOMER'
);
```

El repositorio no incluye datos iniciales ni una cuenta administradora preconfigurada. El acceso administrativo requiere un usuario con contraseña BCrypt y el rol `ADMIN` asociado en la base de datos.

## Rutas principales

Todas las rutas siguientes parten de `/storelink/api`:

| Ruta | Uso |
| --- | --- |
| `POST /customers` | Registro de clientes con nombre, correo, teléfono y contraseña. |
| `POST /auth` | Inicio de sesión con `email` y `password`; devuelve el token y los roles. |
| `/products` y `/categories` | Catálogo y gestión de productos y categorías. |
| `/orders` y `/order-items` | Pedidos y detalle de artículos. |
| `/category-promotions` | Gestión y consulta de promociones. |
| `/payments` | Registro y consulta de pagos simulados. |

Envía `Authorization: Bearer <token>` en las solicitudes protegidas. El origen permitido para el frontend es `http://localhost:4200`, configurado en `Config/WebConfig.java`.

Los pagos utilizan validaciones de prueba de CVV y OTP; no están conectados a una pasarela de cobros. La clave JWT está definida en `Service/JwtService.java`; sustitúyela por una configuración externa antes de desplegar.

## Comandos

| Comando (Windows) | Descripción |
| --- | --- |
| `.\mvnw.cmd test` | Ejecuta la prueba de carga del contexto de Spring. |
| `.\mvnw.cmd clean package` | Compila, ejecuta las pruebas y genera el JAR en `target/`. |

En Linux o macOS, sustituye `.\mvnw.cmd` por `./mvnw`. Las pruebas requieren acceso a MySQL y una configuración válida.
