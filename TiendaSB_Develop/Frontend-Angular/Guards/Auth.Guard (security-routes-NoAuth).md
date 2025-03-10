## Creación de Guards en Angular

Para la creación de guards tenemos que escribir el siguiente comando en la terminal de nuestro proyecto angular

```cmd
1º Opción: ng g guard nombre-guard
2º Opción: ng g g nombre-guard
```

## Auth.guard

Este archivo tendrá como función contener el código que contenga la autenticación del guard en nuestro Proyecto utilizando la funcionalidad *CanActivateFn* . 

> El propósito de este guard es de proteger las rutas de nuestro proyecto y asegurarse que el usuario tenga un token valido antes de acceder a ciertas páginas de nuestro proyecto.

### 1º Importaciones iniciales del authentication guard

```typescript
import { CanActivateFn, Router } from '@angular/router';
import { inject } from '@angular/core';
import { TokenService } from '../auth/token.service';
import { firstValueFrom } from 'rxjs';
import { HttpClient } from '@angular/common/http';
import { environment } from '../../../environments/environment';
```

- **CanActivateFn**: Función de Angular Router para proteger las rutas.
- **Router**: Se utilizara siempre y cuando el usuario no cumple con la validación. 
- **inject**: Se utiliza para obtener servicios dentro de funciones.
- **TokenService**: Servicio de nuestro proyecto Angular que maneja la autenticación de *Tokens*.
- **firstValueFrom**: Convierte un observable de una promesa para usar await.
- **HttpClient** : Permite realizar peticiones HTTP al backend.
- **enviroment**: Contiene las variables de entorno de nuestro proyecto.

### 2º Definición del guard

```typescript
export const authGuard: CanActivateFn = async (route, state) => {
}
```

- Se exporta una constante `authGuard`, que es un **guard de autenticación**.
- Es una **función asincrónica** (`async`) porque va a hacer una petición HTTP para verificar el token.
- Recibe dos parámetros:
    - `route`: Contiene información de la ruta que se intenta acceder.
    - `state`: Contiene el estado actual de la navegación.
### 3º Inyección de dependencias 

```typescript
const tokenService = inject(TokenService);
const router = inject(Router);
const http = inject(HttpClient);
```

En **Angular v14+**, se usa `inject()` en lugar de `constructor` para obtener servicios dentro de funciones.

Aquí se inyectan tres servicios:

1. `TokenService`: Para obtener y gestionar los tokens de autenticación.
2. `Router`: Para redirigir al usuario en caso de que no tenga acceso.
3. `HttpClient`: Para hacer la validación del token con el backend.

### 4º Obtener los tokens del usuario

```typescript
const accessToken = tokenService.getAccessToken();
const refreshToken = tokenService.getAccessRefreshToken();
```

Utilizamos los métodos de nuestro servicio TokenService para poder obtener los tokens:
- `getAccessToken()`: Obtiene el **token de acceso** del usuario.
- `getAccessRefreshToken()`: Obtiene el **token de refresco** (aunque no se usa en este código).

### 5º Verificaciones del token del usuario

*- 1º Caso: No hay token -> Redirigir Login:*

```typescript
if (!accessToken) {
  router.navigate(['/login']);
  return false;
}
```

Si el usuario **no tiene un token**, es redirigido a `/login` y se bloquea el acceso (`return false`).

*- 2º Caso: Hay token por lo tanto validar Token con el backend (Si es valido o no):*

```typescript
try {
  const response: any = await firstValueFrom(
    http.post(`${environment.apiUrl}/users/check-token`, {
      usrname: "Alejandro",
      token: accessToken,
    })
  );

  return true;
}
```

- Se hace una **petición HTTP `POST`** a la API en `/users/check-token`.
- Se envía el **nombre de usuario** y el **token** en el cuerpo de la petición en formato *JSON*.
- `firstValueFrom()` convierte el observable en una promesa para poder usar `await`.
- Si la respuesta es correcta, se permite el acceso (`return true`). Sin embargo si no es correcta:

```typescript
catch (error) {
  // tokenService.removeToken();
  router.navigate(['/login']);
  return false;
}
```

En caso de no ser valido el token, redirige al usuario al login.

En este 2º caso, al llamar a la API estamos concretamente llamando a la función de nuestro controlador *UserController* con la url *{enviroment.apiURL}/users/check-token*.

```java
@PostMapping("/check-token")  
public ResponseEntity<Boolean> checkToken(@RequestBody CheckTokenRequest checkTokenRequest) {  
    return ResponseEntity.ok(this.userService.checkToken(checkTokenRequest));  
}
```

## Resumen del funcionamiento del Auth.guard

- **1º Verifica si hay un token**. Si no lo hay, redirige al login.
- **2º Si hay un token**, hace una petición al backend para validarlo.
- **3º Si el backend confirma que el token es válido**, permite el acceso.
- **4º Si el backend dice que el token es inválido**, redirige al login.

