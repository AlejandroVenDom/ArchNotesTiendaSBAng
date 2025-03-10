Nuestro archivo *app.routes.ts* define todas las rutas de nuestro proyecto donde incluiremos las rutas principales que serian las del **CLIENTE** y las del **ADMINISTRADOR o BACKOFFICE**.

## Estructura de rutas del CLIENTE

Como podemos comprobar, una vez creados los guards de nuestro proyecto, los implementaremos en nuestro archivo de rutas del proyecto.

```typescript
{
  path: "", component: LayoutComponent, children: [
    {path: "", component: HomeComponent}, // -> www.ejemplo.com
    {path: "login", component: LoginComponent, canActivate: [publicGuard]}, // -> www.ejemplo.com/login
    {path: "registro", component: RegistroComponent, canActivate: [publicGuard]}, // -> www.ejemplo.com/registro
    {path: "tienda", component: TiendaComponent}, // www.ejemplo.com/tienda
  ]
}
```

- **Ruta base (`path: ""`)**:  
    Cuando el usuario visita la raíz del sitio (`www.ejemplo.com`), se carga el componente **`LayoutComponent`**. Este componente tiene rutas **hijas** (dentro de `children`) que definen qué se muestra dentro del layout según la URL.
    
    - **Ruta vacía dentro de `children` (`path: ""`)**: Esta ruta muestra el componente **`HomeComponent`** en la página de inicio (`www.ejemplo.com`).

- **Ruta `/login`**:  
    Esta ruta muestra el componente **`LoginComponent`** en la página de inicio de sesión (`www.ejemplo.com/login`).
    
    - **Protección con `publicGuard`**:  
        Solo los usuarios que **no están logueados** pueden acceder a esta página. Si un usuario autenticado intenta ir a esta ruta, será redirigido a otra parte (como la página principal).

- **Ruta `/registro`**:  
    Similar a la ruta de login, esta ruta muestra el componente **`RegistroComponent`** en la página de registro (`www.ejemplo.com/registro`).
    
    - **Protección con `publicGuard`**:  
        También protegida para que solo los usuarios no logueados puedan acceder a ella.

- **Ruta `/tienda`**:  
    Esta ruta muestra el componente **`TiendaComponent`** en la página de la tienda (`www.ejemplo.com/tienda`). No tiene guard, por lo que está disponible para todos los usuarios.

## Estructura de rutas del BACKOFFICE O ADMINISTRADOR

```typescript
{
  path: "app", canActivate: [authGuard], component: LayoutBackComponent, children: [
    {path: "", redirectTo: "control-panel", pathMatch: "full"}, // -> www.ejemplo.com/app
    {path: "control-panel", component: ControlPanelComponent}, // -> www.ejemplo.com/app/control-panel
  ]
}
```

- **Ruta `/app`**:  
    Esta ruta carga el **`LayoutBackComponent`**, que es el layout de la parte de **backoffice** de la aplicación (`www.ejemplo.com/app`).
    
    - **Protección con `authGuard`**:  
        La ruta está protegida por el **`authGuard`**, lo que significa que solo los usuarios **logueados** pueden acceder a esta parte de la aplicación. Si un usuario no autenticado intenta acceder a `/app`, será redirigido al login.

- **Ruta `/app/control-panel`**:  
    Esta ruta muestra el componente **`ControlPanelComponent`** en la página de administración del panel de control (`www.ejemplo.com/app/control-panel`).
    
    - **Redirección automática**:  
        La ruta vacía dentro de `children` (`path: ""`) redirige automáticamente a **`/app/control-panel`** cuando el usuario visita solo `/app`.

## Combinación con los guards

- **`publicGuard`**:
    
    - Protege las rutas que deberían ser solo para usuarios **no logueados** (como el login y registro).
    - Si un usuario logueado intenta acceder a una página protegida por `publicGuard`, será redirigido a la página principal.

- **`authGuard`**:
    
    - Protege las rutas que deberían ser solo para usuarios **autenticados** (como el backoffice).
    - Si un usuario no logueado intenta acceder a una página protegida por `authGuard`, será redirigido al login.