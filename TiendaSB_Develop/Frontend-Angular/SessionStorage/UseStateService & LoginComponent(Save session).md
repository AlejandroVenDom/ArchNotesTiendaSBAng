# **UseStateService & LoginComponent(Save session)**

El servicio `UseStateService` en `LoginComponent` se encarga de **almacenar el estado del usuario en `sessionStorage`** cuando inicia sesión.

## **¿Qué realiza el UseStateService?**

- **Después de iniciar sesión con éxito**, guarda el nombre de usuario y el rol en `sessionStorage`.
- **Permite acceder a estos datos en otras partes de la aplicación sin necesidad de hacer otra petición al servidor**.(IMPORTANTE)
- **Facilita la gestión de sesión**, asegurando que el usuario pueda navegar sin perder su información de autenticación.

## **Explicación de la inyección de UseStateService**
### **Guardar la sesión del usuario después del login**

```typescript
this.credentialsService.login(this.loginForm.value as LoginInterface).subscribe({
  next: (data) => {
	  // cuadro de carga al iniciar sesión (* No relevante )
	this.popupService.loader("Cargando...", "Espere un momento");

    this.tokenService.saveTokens(data.token, "234325423423")  
    this.useStateService.save(data.username, data.role)
    this.router.navigate(['/app/control-panel']);
  },
})
```

Cuando el usuario inicia sesión correctamente, el servicio `credentialsService` devuelve datos como el **token, username y rol**.

- Se guarda el **token de autenticación** con `this.tokenService.saveTokens(data.token, "234325423423")`.
- **Se almacena el nombre de usuario y su rol** con `this.useStateService.save(data.username, data.role)`.
- Se **redirecciona al panel de control (`/app/control-panel`)**.

>💡 **Esto significa que el usuario podrá seguir navegando sin perder su información mientras la pestaña esté abierta**.

### **¿Cómo se pueden usar después los datos?**

En cualquier otro componente de la aplicación, se puede acceder a la información del usuario con:

```typescript
const username = this.useStateService.getUsername();
const role = this.useStateService.getRole();
```

 >💡 **Esto es útil para personalizar la interfaz**, por ejemplo, **mostrar el nombre del usuario en la barra de navegación** o **mostrar opciones según su rol**.
 
## **Resumen del uso de session en el login**

✅ `UseStateService` almacena **nombre de usuario y rol** en `sessionStorage` después del login.  
✅ Permite que la aplicación **recuerde la sesión mientras la pestaña está abierta**.  
✅ Se puede acceder a estos datos fácilmente desde otros componentes sin hacer otra petición al servidor.  
✅ Se usa junto con `TokenService`, que maneja los tokens de autenticación.


