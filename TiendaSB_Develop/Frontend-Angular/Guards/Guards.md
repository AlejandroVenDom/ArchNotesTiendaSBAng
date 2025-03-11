Los **guards** son funciones o servicios que se utilizan para proteger rutas dentro de una aplicación. Permiten controlar el acceso a ciertas rutas en función de condiciones específicas, como la autenticación o autorización del usuario. Los guards se ejecutan antes de que se cargue una ruta y, dependiendo de su resultado, pueden permitir o denegar el acceso a la ruta solicitada.

Existen varios tipos de guards en Angular:

1. **CanActivate**: Se utiliza para decidir si una ruta se puede activar o no. Generalmente, se usa para proteger rutas que requieren que el usuario esté autenticado.
2. **CanActivateChild**: Similar a `CanActivate`, pero se aplica a las rutas hijas de una ruta principal. Permite controlar el acceso a rutas secundarias.

3. **CanDeactivate**: Se usa para impedir que un usuario navegue fuera de una ruta si no se cumplen ciertas condiciones. Esto es útil, por ejemplo, cuando hay formularios sin guardar o cambios no confirmados.

4. **Resolve**: Permite pre-cargar datos antes de activar una ruta. Se utiliza cuando necesitas que ciertos datos estén disponibles antes de que la ruta se muestre al usuario.

5. **CanLoad**: Se utiliza para proteger la carga de módulos perezosos. Impide la carga de módulos antes de cumplir con ciertas condiciones.


### ¿Para qué sirven?

- **Autenticación y autorización**: Protegen rutas que solo deben ser accesibles a usuarios autenticados o con ciertos permisos.
- **Navegación condicional**: Impiden que los usuarios naveguen a ciertas rutas si no cumplen con las condiciones definidas.
- **Carga de datos antes de mostrar la ruta**: Permiten pre-cargar datos o recursos necesarios antes de que la ruta sea activada.
