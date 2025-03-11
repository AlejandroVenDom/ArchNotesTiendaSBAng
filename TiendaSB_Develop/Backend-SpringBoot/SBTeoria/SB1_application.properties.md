# **application.properties (archivo de configuración)**
## **¿ Para que sirve el archivo application.properties ?**

El archivo `application.properties` tiene como funcionalidad, poseer la configuración de nuestro proyecto sin necesidad de modificar código al respecto.

Usamos el archivo para configuraciones como :
- **Puerto del servidor**
- **Conexión a la base de datos**
- **Parámetros de seguridad**
- **Configuración de logs**
- **Configuración de Hibernate y JPA**
- **Propiedades personalizadas para la aplicación**
## **Partes del application.properties (tienda online)**

### **1º Configuración básica de Spring y del servidor**

```java
spring.application.name=Tienda Online
server.port=8080
server.servlet.context-path=/api
```

- `spring.application.name`: definimos el nombre de la aplicación
- `server.port`: definimos el puerto para ejecutar la aplicación
- `server.servlet.context-path=`: definimos una ruta base, es decir que en este caso establecemos que todas las rutas de la API estén bajo la ruta `/api`

> Esta ruta es importante ya que para realizar la conexión al *Backend (SpringBoot)* desde el *FrontEnd (Angular)* deberemos utilizar la ruta correspondiente a la aplicación en el archivo *enviroments.ts*.

```typescript
export const environment = {
  production: false,
  apiUrl: 'http://localhost:8080/api',
  tokenSecure: false
}
```

### **2. Configuración de la Base de Datos (MySQL)**

```java
spring.datasource.url=jdbc:mysql://localhost:3306/tienda
spring.datasource.username=aseck
spring.datasource.password=1234
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
```

- 1º conexión a MySQL en localhost:3306 (puerto determinado de MySQL).
	- Llamada a la base de datos (tienda)

- 2º Credenciales de MySQL:
	- Usuario: `aseck`
	- Contraseña: `1234`

- 3º Driver de JDBC para mysql

### **3. Configuración de Hibernate (ORM para la base de datos)**

```java
spring.jpa.database-platform=org.hibernate.dialect.MySQLDialect
spring.jpa.hibernate.ddl-auto=update
```

En este trozo de código, indicamos que utilizamos MySQL como base de datos. Además que Hibernate **creará o actualizará automáticamente las tablas según las entidades en el código.**

### **4. Logs de Hibernate (Para depuración)**

```java
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
logging.level.org.hibernate.SQL=DEBUG
logging.level.org.hibernate.type.descriptor.sql.BasicBinder=TRACE
```

- **`show-sql=true`**: Muestra en consola las consultas SQL ejecutadas.
- **`format_sql=true`**: Formatea mejor las consultas en consola.
- **`SQL=DEBUG` y `BasicBinder=TRACE`**: Muestra información detallada sobre las consultas.

### **5. Configuración de JWT (Autenticación con Tokens)**

```java
jwt.secret = 8f8d252a66034d182acf7abe7ba266604c59041b0281970e167fce66daf4e96f
jwt.expiration=86400000  # 1 día en milisegundos
```

- **`jwt.secret`**: Clave secreta para firmar los tokens JWT.
- **`jwt.expiration=86400000`**: El token expira en **1 día** (86,400,000 milisegundos).

## **Explicación breve de la funcionalidad del archivo**

Este archivo configura el backend de una tienda online con:
- **Spring Boot** en el puerto **8080**.
- **Base de datos MySQL** conectada con Hibernate.
- **Autenticación con JWT** para seguridad.
- **Logs detallados** de las consultas SQL en consola.