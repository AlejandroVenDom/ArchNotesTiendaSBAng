## Collections Configuration

Una vez que tengamos creado nuestra entorno de desarrollo en Postman, crearemos una colección en el apartado **Collections** . 
Creamos una colección con el nombre de nuestro proyecto, y la creamos como una colección **REST API basics** .

![[Pasted image 20250226123737.png]]

Una vez creado nos aparecerá lo siguiente:

![[Pasted image 20250226123943.png]]

Se nos creará una serie de apartados REST donde podremos pedir, meter, borrar y eliminar de nuestra base de datos.
Dentro de la colección, crearemos otra carpeta donde estará especificado el CRUD de nuestra clase, en este caso **Users**. 

## Método getAllUsers (Get)

El método getAllUsers de nuestro proyecto consiste en traer a todos los usuarios de nuestra base de datos. Para realizar dicha petición de datos, simplemente escribiremos la siguiente estructura:
**{{Variable}}/dirección**. El ejemplo de la tienda online sería:

```java
{{url}}/users
```

Esto viene debido a nuestra clase **UserController**, donde como el nombre indica se trata de un controlador que ayudara a la petición e insercción de datos:

clase *UserController.java* .
```java
package com.tienda.app.controllers;  
  
import com.tienda.app.dtos.auth.LoginRequest;  
import com.tienda.app.dtos.auth.LoginResponse;  
import com.tienda.app.dtos.auth.RegisterRequest;  
import com.tienda.app.models.User;  
import com.tienda.app.services.UserService;  
import org.springframework.http.ResponseEntity;  
import org.springframework.security.authentication.BadCredentialsException;  
import org.springframework.web.bind.annotation.*;  
  
import java.util.List;  

@RestController  
@RequestMapping("/users")  
@CrossOrigin("*")  
public class UserController {  
  
    private final UserService userService;  
  
    public UserController(UserService userService) {  
        this.userService = userService;  
    }  
  
    @GetMapping  
    public ResponseEntity<List<User>> getAllUsers() {  
        return ResponseEntity.ok(this.userService.getAllUsers());  
    }  
  
    // http://localhost:8080/api/users/login  --> Usamos elementos por post ya que no lo introduciremo por URL  
    @PostMapping("/login")  
    public ResponseEntity<?> login(@RequestBody LoginRequest credentials){  
        try {  
            LoginResponse loginResponse = this.userService.login(credentials);  
            return ResponseEntity.ok(loginResponse);  
        }  
        catch (BadCredentialsException e){  
            return ResponseEntity.status(401).body(e.getMessage());  
        }  
    }  
  
    // Esta prohibido enviar datos sensibles a través de get  
    @PostMapping("/register")  
    public ResponseEntity<?> register(@RequestBody RegisterRequest registerRequest){  
        try {  
            User user = this.userService.createUser(registerRequest);  
            return ResponseEntity.ok(user);  
        } catch (IllegalArgumentException e) {  
            return ResponseEntity.status(401).body(e.getMessage());  
        }  
    }  
}
```

>Como el *@RequestMapping()* tiene como dirección */users*, tendremos que poner en la url:
> http://localhost:8080/users 
> para la obtención de datos de todos los usuarios, ya que por defecto, dicha dirección recoge el método *getAllUsers()* .

```java
@GetMapping  
    public ResponseEntity<List<User>> getAllUsers() {  
        return ResponseEntity.ok(this.userService.getAllUsers());  
    }  
```

- `@GetMapping` → Indica que este método responde a peticiones `GET` en `/users`.
- Llama a `userService.getAllUsers()` para obtener la lista de usuarios.
- Retorna una **respuesta HTTP 200 OK** con la lista de usuarios en formato JSON.

Finalmente pedimos por la url y nos debería salir un array vacío debido a que aún no se habrán metido ningún usuario.

![[Pasted image 20250226123635.png]]

## Método RegisterUser (Post)

El método *RegisterUser* lo que hace es ayudarnos a la insercción de usuarios en la base de datos, para ello deberemos configurar las caracteristicas de petición de la siguiente manera:

- Primero configuramos los *headers* de manera que sea de tipo **Content-Type application/json**.

![[Pasted image 20250226134341.png]]

- Segundo configuramos el *Body* de manera que le indicamos los datos que se van a proporcionar para el registro del usuario. Para saber que datos son necesarios para el registro nos dirigiremos a la clase *RegisterRequest*, donde nos aparecera lo siguiente

![[Pasted image 20250226134716.png]]

Una vez que sabemos que es lo que debemos indicar en formato .JSON, lo escribimos para enviar.
Es importante que lo escribamos tal cual.

![[Pasted image 20250226134521.png]]

La primera vez que se lo mandemos nos saldra un error debido a el *enum* de **roleName**.
Para ello nos dirigimos a phpMyAdmin para insertar dichos roles en nuestra tabla roles.

![[Pasted image 20250226135000.png]]

Una vez hemos configurado todo ya podremos visualizar la inserción de usuario.

![[Pasted image 20250226135059.png]]

Y si finalmente le damos al método getAllUsers recibiremos nuestro usuario de manera detallada y la información que pusimos que nos diese.

![[Pasted image 20250226135206.png]]

### Funcionamiento de registerUser

Para saber como es que funciona el registro de usuario nos dirigiremos al proyecto y nos centraremos en tan solo tres clases:

- *UserService.java*
- *RegisterRequest.java*
- *UserController.java*

Todas estas clases se concatenan entre si obteniendo así un buen funcionamiento del sistema

## Método LoginUser (Post)

El método *LoginUser* sirve para el inicio de sesión de un usuario ya registrado en la base de datos. Para ello es importante configurar las siguientes características:
- Primero configuramos los *Headers* donde pondremos que será de tipo **Content-Type application/json**.

![[Pasted image 20250226131958.png]]

- Segundo configuramos el *Body* donde tendremos que estructurar los datos que nos pediría el login. Para saber que datos son necesarios para iniciar sesión deberemos dirigirnos a nuestro archivo *LoginRequest*.

![[Pasted image 20250226132428.png]]

Como podemos comprobar, solamente se pide el *username* y el *password* por lo que lo escribimos en forma de .JSON en Postman
Finalmente lo ponemos de tipo *raw* y *JSON*.

![[Pasted image 20250226132325.png]]

Escribimos la dirección url para realizar el POST, la dirección se encuentra en *UserController*.

![[Pasted image 20250226132656.png]]

El resultado final sería lo siguiente:

![[Pasted image 20250226132821.png]]

### Vista de Problemas con LoginUser y funcionamiento del método login

Una vez hayamos comprobado que el usuario está en la base de datos, comprobaremos los errores de dicho programa.

>Nombre de Usuario incorrecto | Usuario no en la base de datos

![[Pasted image 20250226133148.png]]

> Contraseña de Usuario incorrecta

![[Pasted image 20250226133243.png]]

Estos problemas se encuentran en la clase *UserService* como el método **login()** de tipo **LoginResponse**, y se encuentra de la siguiente manera:

clase *UserService*
```java
public LoginResponse login(LoginRequest credentials){  
    // comprobamos si el usuario existe  
    User user = this.userRepository.findByUsername(credentials.getUsername()).orElseThrow(  
            () -> new BadCredentialsException("User not found")  
    );  
  
    // comprobamos si la contraseña existe  
    if(!this.passwordEncoder.matches(credentials.getPassword(), user.getPassword())) {  
        throw new BadCredentialsException("invalid password");  
    }  
  
  
    LoginResponse loginData = new LoginResponse();  
    loginData.setUsername(credentials.getUsername());  
    loginData.setRole(user.getRole().getRoleName());  
    loginData.setToken(this.jwtUtil.generateToken(credentials.getUsername()));  
  
    return loginData;  
  
  
}
```

Este método se le llama en la clase **UserController** donde cada vez que entremos por URL a la dirección https://localhost:8080/users/login nos llamara a dicho método al introducir los datos correspondientes.

clase *UserController*
```java
@PostMapping("/login")  
public ResponseEntity<?> login(@RequestBody LoginRequest credentials){  
    try {  
        LoginResponse loginResponse = this.userService.login(credentials);  
        return ResponseEntity.ok(loginResponse);  
    }  
    catch (BadCredentialsException e){  
        return ResponseEntity.status(401).body(e.getMessage());  
    }  
}
```



