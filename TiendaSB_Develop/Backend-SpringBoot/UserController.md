## **1️⃣ Importaciones**

java

CopiarEditar

`import com.tienda.app.dtos.auth.LoginRequest; import com.tienda.app.dtos.auth.LoginResponse; import com.tienda.app.dtos.auth.RegisterRequest; import com.tienda.app.models.User; import com.tienda.app.services.UserService; import org.springframework.http.ResponseEntity; import org.springframework.security.authentication.BadCredentialsException; import org.springframework.web.bind.annotation.*;  import java.util.List;`

### 🔹 **¿Qué hacen estas importaciones?**

- **DTOs (Data Transfer Objects)**
    
    - `LoginRequest` → Contiene los datos de inicio de sesión (usuario y contraseña).
    - `LoginResponse` → Devuelve la respuesta del login (ejemplo: token de autenticación).
    - `RegisterRequest` → Contiene los datos necesarios para registrar un usuario.
- **Modelos y Servicios**
    
    - `User` → Representa el modelo de usuario en la base de datos.
    - `UserService` → Clase de servicio que maneja la lógica de negocio para usuarios.
- **Spring Boot y seguridad**
    
    - `ResponseEntity` → Permite construir respuestas HTTP personalizadas.
    - `BadCredentialsException` → Se usa cuando las credenciales son incorrectas.
    - `@RestController`, `@RequestMapping`, `@CrossOrigin` → Anotaciones de Spring Boot para definir un controlador y gestionar las rutas.

---

## **2️⃣ Comentarios sobre las URLs**

java

	

`/* * * http://localhost:8080/ -> Esta es la URL base para conectarnos a SpringBoot * http://localhost:8080/api -> Si tenemos el context-path en app.properties, la URL base cambia * */`

Estos comentarios explican las **URLs base** de la aplicación.  
Por defecto, el backend se ejecuta en `http://localhost:8080/`, pero si en `application.properties` configuras:

ini

CopiarEditar

`server.servlet.context-path=/api`

entonces todas las rutas estarán bajo `http://localhost:8080/api`.

---

## **3️⃣ Códigos de estado HTTP**

java

CopiarEditar

`/* * * 200 -> Todo bien * 201 -> Todo bien pero para update * 204 -> Todo bien pero para borrar * 400 -> Error de identificación * 401 -> Datos incorrectos * 403 -> Permiso denegado * 404 -> No se ha encontrado * 500 -> Error en el servidor (fallo en el código) * */`

Estos comentarios explican los códigos de estado HTTP usados en el backend:

- `200 OK` → Todo salió bien.
- `201 Created` → Se creó correctamente (ejemplo: usuario registrado).
- `204 No Content` → Se eliminó algo y no se devuelve contenido.
- `400 Bad Request` → La solicitud es incorrecta.
- `401 Unauthorized` → Credenciales inválidas.
- `403 Forbidden` → No tienes permisos.
- `404 Not Found` → No se encontró el recurso.
- `500 Internal Server Error` → Fallo en el servidor.

---

## **4️⃣ Definición del Controlador**

java

CopiarEditar

`@RestController @RequestMapping("/users") @CrossOrigin("*") public class UserController {`

### 🔹 **Anotaciones importantes**

- `@RestController` → Indica que esta clase es un **controlador REST**, y sus métodos devolverán respuestas en formato JSON.
- `@RequestMapping("/users")` → Define la ruta base del controlador (`/users`).
    - Todas las rutas de esta clase comenzarán con `/users`.
    - Ejemplo: `http://localhost:8080/users/login`.
- `@CrossOrigin("*")` → Permite que este controlador sea accesible desde **cualquier origen** (evita problemas de CORS en el frontend).

---

## **5️⃣ Inyección del Servicio**

java

CopiarEditar

`private final UserService userService;  public UserController(UserService userService) {     this.userService = userService; }`

Aquí se inyecta el servicio `UserService`, que contiene la lógica de negocio para usuarios.

🔹 **¿Por qué usamos inyección de dependencias?**

- Evita instanciar la clase manualmente (`new UserService()`), permitiendo que Spring gestione la instancia.

---

## **6️⃣ Método para Obtener Todos los Usuarios**

java

CopiarEditar

`@GetMapping public ResponseEntity<List<User>> getAllUsers() {     return ResponseEntity.ok(this.userService.getAllUsers()); }`

### 🔹 **Explicación**

- `@GetMapping` → Indica que este método responde a peticiones `GET` en `/users`.
- Llama a `userService.getAllUsers()` para obtener la lista de usuarios.
- Retorna una **respuesta HTTP 200 OK** con la lista de usuarios en formato JSON.

🔹 **Ejemplo de solicitud:**

http

CopiarEditar

`GET http://localhost:8080/users`

🔹 **Ejemplo de respuesta JSON:**

json

CopiarEditar

`[   {"id":1, "nombre":"Alejandro", "email":"alejandro@example.com"},   {"id":2, "nombre":"María", "email":"maria@example.com"} ]`

---

## **7️⃣ Método para Iniciar Sesión**

java

CopiarEditar

`@PostMapping("/login") public ResponseEntity<?> login(@RequestBody LoginRequest credentials){     try {         LoginResponse loginResponse = this.userService.login(credentials);         return ResponseEntity.ok(loginResponse);     }     catch (BadCredentialsException e){         return ResponseEntity.status(401).body(e.getMessage());     } }`

### 🔹 **Explicación**

- `@PostMapping("/login")` → Indica que este método responde a peticiones `POST` en `/users/login`.
- `@RequestBody LoginRequest credentials` → Recibe los datos de inicio de sesión en el cuerpo de la petición.
- Llama al método `userService.login(credentials)`, que valida las credenciales.
- Si las credenciales son correctas, devuelve un `LoginResponse` con un **token JWT**.
- Si son incorrectas (`BadCredentialsException`), devuelve un error `401 Unauthorized`.

🔹 **Ejemplo de solicitud:**

http

CopiarEditar

`POST http://localhost:8080/users/login Content-Type: application/json  {   "email": "alejandro@example.com",   "password": "123456" }`

🔹 **Ejemplo de respuesta en caso de éxito:**

json

CopiarEditar

`{   "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." }`

🔹 **Ejemplo de respuesta en caso de error:**

json

CopiarEditar

`{   "message": "Credenciales incorrectas" }`

---

## **8️⃣ Método para Registrar un Usuario**

java

CopiarEditar

`@PostMapping("/registro") public ResponseEntity<?> register(@RequestBody RegisterRequest registerRequest){     try {         User user = this.userService.createUser(registerRequest);         return ResponseEntity.ok(user);     } catch (IllegalArgumentException e) {         return ResponseEntity.status(401).body(e.getMessage());     } }`

### 🔹 **Explicación**

- `@PostMapping("/registro")` → Responde a `POST /users/registro`.
- `@RequestBody RegisterRequest registerRequest` → Recibe los datos del usuario.
- Llama a `userService.createUser(registerRequest)`, que crea el usuario.
- Si todo sale bien, retorna `200 OK` con el usuario creado.
- Si hay un error (`IllegalArgumentException`), retorna `401 Unauthorized`.

🔹 **Ejemplo de solicitud:**

http

CopiarEditar

`POST http://localhost:8080/users/registro Content-Type: application/json  {   "nombre": "Alejandro",   "email": "alejandro@example.com",   "password": "123456" }`

🔹 **Ejemplo de respuesta:**

json

CopiarEditar

`{   "id": 1,   "nombre": "Alejandro",   "email": "alejandro@example.com" }`

---

## **📌 Conclusión**

🔹 `UserController` maneja el **inicio de sesión**, el **registro** y la **obtención de usuarios**.  
🔹 Se conecta con `UserService`, que implementa la lógica de negocio.  
🔹 Usa `ResponseEntity` para devolver respuestas HTTP adecuadas.