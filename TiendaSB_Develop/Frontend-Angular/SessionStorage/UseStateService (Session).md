# **UseStateService & SessionStorage**

`UseStateService`, **gestiona el estado del usuario en la sesión del navegador** utilizando `sessionStorage`.
## **Funcionalidad del servicio**

El servicio **guarda, obtiene y elimina información del usuario** (nombre de usuario y rol) en el almacenamiento de sesión (`sessionStorage`).  
Esto permite **mantener la información del usuario** mientras el navegador está abierto, pero **se pierde al cerrar la pestaña o el navegador**.

## **Pasos de creación del Servicio**
### **1º Definir una clave para almacenar los datos**

```typescript
private readonly USER_KEY = 'tienda_online';
```

- Se define una **clave única** (`USER_KEY`) que se usará en `sessionStorage` para almacenar los datos del usuario.
- En este caso, la clave es **"tienda_online"**, pero se puede cambiar según la aplicación.

### **2º Guardar los datos del usuario en la sesión con sessionStorage** 

```typescript
save(username: string, role: string): void  {
  sessionStorage.setItem(this.USER_KEY, JSON.stringify({username, role}));
}
```

Creamos un metodo save donde le diremos que es lo que queremos guardar en el sessionStorage para poder después imprimirlo por pantalla. Llamamos al sessionStorage y utilizamos su método setItem que lo que hará es ingresar en el sessiónStorage los datos del usuario en forma de JSON.

>**Convierte un objeto `{ username, role }` a una cadena JSON** y lo guarda en `sessionStorage` bajo la clave `"tienda_online"`.

Ejemplo de como se vería:

```JSON
{
  "username": "Alejandro",
  "role": "admin"
}
```

Para comprobar que los datos se guardan correctamente, tenemos que dirigirnos a las herramientas de desarrollador de la web (Inspeccionar) y una vez dentro, dirigirnos a Application y buscar sessionStorage donde encontraremos guardado en forma de JSON lo que le pedimos que guarde con el *save()*

>A continuación, crearemos los métodos que utilizaremos en nuestro componente que queramos visualizar dichos datos que recogemos en los métodos. 
>
>En este primer caso podremos obtener el nombre del usuario, importando este servicio dentro del component.ts de nuestro html para poder después imprimirlo.
### **3º Obtener el nombre del usuario**

```typescript
getUsername(): string | null {
  const session = JSON.parse(<string>sessionStorage.getItem(this.USER_KEY));
  if (!session) {
    return null;
  }
  return session.username;
}
```

- **Obtiene los datos del usuario desde `sessionStorage`**.
- **Si no hay sesión almacenada**, devuelve `null`.
- Si hay datos, extrae y devuelve el **nombre de usuario**.
### **4º Obtener el rol del usuario**

```typescript
getRole(): string | null {
  const session = JSON.parse(<string>sessionStorage.getItem(this.USER_KEY));
  if (!session) {
    return null;
  }
  return session.role;
}
```

**Funciona igual que `getUsername()`**, pero devuelve el **rol del usuario**.

### **5º Eliminar sesión**

```typescript
removeSession(): void {
  sessionStorage.removeItem(this.USER_KEY);
}
```

**Elimina los datos almacenados en `sessionStorage`**, cerrando la sesión del usuario.
Cuando queramos eliminar la sesión para después iniciar con otra cuenta, deberemos utilizar dicho método inyectandola en otros componentes.

## **Obtener Datos por pantalla**

Ahora una vez creados los métodos que nos servirán para poder obtener el nombre y el rol del usuario, inyectaremos dicho servicio dentro de nuestro componente en el que llamaremos a los métodos correspondientes.

>Profile.component.ts -> Componente de nuestro index.html

```typescript
import { Component } from '@angular/core';
import { UseStateService } from '../../services/auth/use-state.service';
@Component({
  selector: 'app-profile',
  imports: [],
  templateUrl: './profile.component.html',
  styleUrl: './profile.component.scss'
})

export class ProfileComponent {
  username: string | null;
  role: string | null;
  constructor(private useStateService: UseStateService) {

    this.username = this.useStateService.getUsername();
    this.role = this.useStateService.getRole();
  }
}
```

>Profile.component.html -> Html donde pintaremos los datos recogidos del sessionStorage

```html
<div class="profileData container col-6">
            <h1>¡Bienvenido {{username}}!</h1>
            <p>Información del Usuario:</p>
            <p>{{role}}</p>
            <p>{{firstName}} {{lastName}}</p>
        </div>
```

Finalmente, habremos obtenido los datos del usuario logueado y lo habremos mostrado en pantalla.
## **Resumen del Session**

✅ **Guarda y gestiona el estado del usuario** en `sessionStorage`.  
✅ **Permite acceder al nombre y rol del usuario** fácilmente.  
✅ **Borra la sesión cuando el usuario cierra sesión**.  
✅ **Ideal para mantener la sesión del usuario** mientras la pestaña está abierta.
