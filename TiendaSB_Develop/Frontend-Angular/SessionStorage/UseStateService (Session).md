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

### **2º Guardar los datos del usuario en la sesión**

```typescript
save(username: string, role: string): void  {
  sessionStorage.setItem(this.USER_KEY, JSON.stringify({username, role}));
}
```

**Convierte un objeto `{ username, role }` a una cadena JSON** y lo guarda en `sessionStorage` bajo la clave `"tienda_online"`.

Ejemplo de como se vería:

```JSON
{
  "username": "Alejandro",
  "role": "admin"
}
```

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

## **Resumen del Session**

✅ **Guarda y gestiona el estado del usuario** en `sessionStorage`.  
✅ **Permite acceder al nombre y rol del usuario** fácilmente.  
✅ **Borra la sesión cuando el usuario cierra sesión**.  
✅ **Ideal para mantener la sesión del usuario** mientras la pestaña está abierta.
