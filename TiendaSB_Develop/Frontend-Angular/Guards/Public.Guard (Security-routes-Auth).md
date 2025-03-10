**(`publicGuard`) en nuestro proyecto Angular**, se usa para **proteger las rutas de acceso público**. Su propósito es evitar que los usuarios autenticados (es decir, los que tienen un token de acceso) accedan a rutas que deberían ser solo para usuarios no autenticados, como la página de **login** o **registro**.

## 1º Importaciones iniciales de public.guard

```typescript
import { CanActivateFn, Router } from '@angular/router';
import { inject } from '@angular/core';
import { TokenService } from '../auth/token.service';
```

- **`CanActivateFn`**: Es una función de **Angular Router** que se utiliza para proteger rutas.
- **`Router`**: Es el servicio de Angular utilizado para realizar las redirecciones de las rutas.
- **`inject`**: Es un método de Angular para obtener servicios dentro de funciones (nuevo en Angular 14+).
- **`TokenService`**: Es un servicio propio que maneja los tokens de autenticación del usuario.

## 2º Definición del guard

```typescript
export const publicGuard: CanActivateFn = (route, state) => {
```

- Se exporta una constante **`publicGuard`** que es un guard de **canActivate**.
- Esta función es utilizada para **proteger rutas de acceso público**.
- No es asincrónica como el `authGuard` anterior, porque no necesita hacer peticiones HTTP.
- Recibe dos parámetros:
    - **`route`**: Contiene información sobre la ruta a la que se intenta acceder.
    - **`state`**: Contiene el estado actual de la navegación (aunque no se usa en este caso).

## 3º Inyección de dependencias

```typescript
const tokenService = inject(TokenService);
const router = inject(Router);
```

Usando el método **`inject()`**, se obtienen los servicios:

- **`TokenService`**: Para obtener el token de acceso del usuario.
- **`Router`**: Para redirigir al usuario si ya tiene un token.

## 4º Obtener los tokens del usuario

```typescript
const accessToken = tokenService.getAccessToken();
const refreshToken = tokenService.getAccessRefreshToken();
```

- **`getAccessToken()`**: Obtiene el **token de acceso** del usuario si está autenticado.
- **`getAccessRefreshToken()`**: Obtiene el **token de refresco** (aunque no se usa aquí).

## 5º Verificaciones de acceso

*- 1º Caso: El usuario esta autenticado -> Redirigir a página principal:*

```typescript
if (accessToken) {
  router.navigate(['']);
  return false;
}
```

- Si el usuario ya tiene un **token de acceso**, significa que está **autenticado**.
- En este caso, **se le redirige a la página principal** (`''` es la ruta raíz) porque no debería acceder a rutas de acceso público como login o registro.
- El `return false` significa que **no se permite la navegación a la ruta solicitada** (en este caso, rutas como `/login` o `/register`).

*- 2º Caso: El usuario no esta autenticado -> Permitir acceso a Login o Registro*

```typescript
return true;
```

- Si el usuario **no tiene un token de acceso**, significa que no está autenticado.
- **Se permite el acceso** a la ruta pública solicitada (por ejemplo, `/login` o `/register`).
- El `return true` significa que la navegación está permitida.
## Resumen del funcionamiento de Public.guard

- **1º Verifica si el usuario tiene un token de acceso**.
- **2º Si el usuario tiene un token**, lo redirige a la página principal (porque ya está autenticado).
- **3º Si el usuario no tiene un token**, le permite acceder a rutas de acceso público como login o registro.