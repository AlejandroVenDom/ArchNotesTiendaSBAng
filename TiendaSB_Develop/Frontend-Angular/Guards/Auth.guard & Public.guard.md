### 🔐 **`authGuard`**

**Objetivo:** Proteger páginas que solo deben ver los usuarios **autenticados** (que han iniciado sesión).

- **¿Qué hace?**
    - Verifica si el usuario tiene un **token de acceso** (una especie de "llave" que demuestra que está logueado).
    - Si no tiene token, lo redirige a la página de **login**.
    - Si tiene token, se asegura de que sea válido haciendo una petición al servidor. Si es válido, deja que entre a la página que quería ver; si no es válido, lo manda al **login**.

**En resumen:** Protege las páginas para que solo las vean los usuarios que están logueados con un token válido.

---

### 🌍 **`publicGuard`**

**Objetivo:** Proteger las páginas que solo deben ver los usuarios **no autenticados** (como login o registro).

- **¿Qué hace?**
    - Verifica si el usuario **ya está logueado** (si tiene un token de acceso).
    - Si el usuario está logueado, lo redirige a la página principal (porque ya no debería estar en login o registro).
    - Si no está logueado, lo deja acceder a la página de **login** o **registro**.

**En resumen:** Evita que los usuarios logueados vean páginas como el **login** o **registro**, y les permite acceder solo si no están logueados.

>Ambos aseguran que las personas solo puedan acceder a las páginas para las que tienen permiso (si están logueadas o no).

