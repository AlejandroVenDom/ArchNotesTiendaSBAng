El servicio **`TokenService`** gestiona **la persistencia de los tokens** en las cookies, pero no directamente su **vida útil**. El servicio se encarga de almacenar, recuperar y eliminar los tokens de acceso y refresco en las cookies, pero no controla cuándo deben expirar o renovarse.
En este servicio se estará creando cookies para almacenar los tokens de acceso y refresco

## Código detallado del servicio

```typescript
import { Injectable } from '@angular/core';
import { CookieService } from 'ngx-cookie-service';
import { environment } from '../../../environments/environment';

@Injectable({
  providedIn: 'root'
})
// Servicio de cookies
// Archivo importante si hay inicio de sesión, vale el mismo archivo
// en todos los proyectos
export class TokenService {

  // Variable privada de solo lectura
  private readonly ACCESS_TOKEN_KEY: string = 'tienda_token';
  private readonly REFRESH_TOKEN_KEY: string = 'tienda_refresh_token';

  /**
   * La variable refresh sirve para que cuando el token llegue a su fin, 
   * es decir, se concluye su tiempo de caducidad. El refresh se encargará 
   * de renovarlo para que el cliente no tenga que estar volviendo a iniciar
   * sesión en la página web.
   */

  constructor(
    private cookieService: CookieService,
  ) { }

  // Método para guardar los tokens en las cookies
  saveTokens(token: string, refreshToken: string) {
    // Guardar el token de acceso en la cookie
    this.cookieService.set(this.ACCESS_TOKEN_KEY, token, {
      path: '/', // Asegura que la cookie esté disponible en todo el dominio
      secure: environment.tokenSecure, // En producción esto debería ser true (solo en HTTPS)
      sameSite: "Strict" // Restringe los accesos desde sitios cruzados (previene CSRF)
    });

    // Guardar el token de refresco en la cookie
    this.cookieService.set(this.REFRESH_TOKEN_KEY, refreshToken, {
      path: '/', // Asegura que la cookie esté disponible en todo el dominio
      secure: environment.tokenSecure, // En producción esto debería ser true (solo en HTTPS)
      sameSite: "Strict" // Restringe los accesos desde sitios cruzados (previene CSRF)
    });
  }

  // Método para obtener el token de acceso
  getAccessToken() {
    return this.cookieService.get(this.ACCESS_TOKEN_KEY); // Devuelve el valor de la cookie del token de acceso
  }

  // Método para obtener el token de refresco
  getAccessRefreshToken() {
    return this.cookieService.get(this.REFRESH_TOKEN_KEY); // Devuelve el valor de la cookie del token de refresco
  }

  // Método para eliminar ambos tokens de las cookies
  removeToken() {
    this.cookieService.delete(this.ACCESS_TOKEN_KEY, '/', '', environment.tokenSecure, 'Strict'); // Elimina la cookie del token de acceso
    this.cookieService.delete(this.REFRESH_TOKEN_KEY, '/', '', environment.tokenSecure, 'Strict'); // Elimina la cookie del token de refresco
  }

}
```

## **Explicación por partes del servicio TokenService

### **1º Declaración de las claves para las cookies

```typescript
private readonly ACCESS_TOKEN_KEY: string = 'tienda_token';
private readonly REFRESH_TOKEN_KEY: string = 'tienda_refresh_token';
```

Estas dos variables son las **claves** que usaremos para almacenar los tokens en las cookies. `ACCESS_TOKEN_KEY` es para el token de acceso y `REFRESH_TOKEN_KEY` es para el token de refresco. Se definen como **constantes** de solo lectura para evitar cambios accidentales.

### **2º Método `saveTokens`**

```typescript
saveTokens(token: string, refreshToken: string) {
  this.cookieService.set(this.ACCESS_TOKEN_KEY, token, {
    path: '/',
    secure: environment.tokenSecure,
    sameSite: "Strict"
  });

  this.cookieService.set(this.REFRESH_TOKEN_KEY, refreshToken, {
    path: '/',
    secure: environment.tokenSecure,
    sameSite: "Strict"
  });
}
```

- Este método guarda los tokens (`token` y `refreshToken`) en las cookies del navegador, es decir, que se crea la cookie para después guardar el token.

- **`this.cookieService.set`**: Se usa para crear una cookie. Toma tres parámetros principales:

    - **Nombre de la cookie**: `this.ACCESS_TOKEN_KEY` para el token de acceso y `this.REFRESH_TOKEN_KEY` para el token de refresco.
    
    - **Valor de la cookie**: El valor de cada token (`token` y `refreshToken`).
    
    - **Opciones de configuración**:
    
        - **`path: '/'`**: Asegura que las cookies estén disponibles en todo el dominio de la aplicación.
        - **`secure: environment.tokenSecure`**: Si en `environment.tokenSecure` está configurado como `true`, solo se enviarán las cookies a través de HTTPS. Esto es crucial en producción.
        - **`sameSite: "Strict"`**: Esto ayuda a prevenir ataques de tipo CSRF (Cross-Site Request Forgery) restringiendo el acceso a la cookie solo a las solicitudes del mismo sitio.

```typescript
this.cookieService.set(this.nombreCookie,valor_cookie,{opcionesConfiguracion})
```

## **3º Método `getAccessToken` 

```typescript
getAccessToken() {
  return this.cookieService.get(this.ACCESS_TOKEN_KEY);
}

getAccessRefreshToken() {
  return this.cookieService.get(this.REFRESH_TOKEN_KEY);
}
```

**`this.cookieService.get`**: Este método devuelve el valor de la cookie correspondiente. En este caso, devuelve el token de acceso y el token de refresco.

## **4º Método `removeToken`

```typescript
removeToken() {
  this.cookieService.delete(this.ACCESS_TOKEN_KEY, '/', '', environment.tokenSecure, 'Strict');
  this.cookieService.delete(this.REFRESH_TOKEN_KEY, '/', '', environment.tokenSecure, 'Strict');
}
```

**`this.cookieService.delete`**: Se utiliza para eliminar las cookies almacenadas. Aquí se eliminan tanto el token de acceso como el de refresco.

- **`path: '/'`**: Indica que las cookies que se eliminarán están disponibles en todo el dominio.
- **`secure: environment.tokenSecure`**: Similar a la creación de las cookies, garantiza que solo se eliminen en una conexión segura.
- **`sameSite: 'Strict'`**: Se asegura de que las cookies solo se eliminen de las solicitudes que provienen del mismo dominio.

## **Resumen de la configuración de las cookies

- **Almacenamiento y recuperación de tokens:** Los métodos del servicio gestionan el almacenamiento de tokens en cookies y la recuperación de los mismos cuando es necesario.
- **Seguridad en las cookies:** Se configura el uso de cookies seguras (`secure: true`), restringe el acceso desde otros sitios (`sameSite: "Strict"`), y se asegura de que las cookies estén disponibles en todo el dominio (`path: '/'`).
- **Eliminación de tokens:** El servicio también gestiona la eliminación de los tokens de las cookies cuando el usuario cierra sesión o cuando ya no se necesita el token.