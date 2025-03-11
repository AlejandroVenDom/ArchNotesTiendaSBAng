# **MVC (Modelo-Vista-Controlador)**

## **¿Qué es MVC?**

Es un patrón de diseño arquitectónico que **separa la lógica de negocio, la interfaz de usuario y el control de las interacciones** en tres capas bien definidas:

-  **Modelo (Model) y Repositorio:** Representa los datos y la lógica de negocio.  
-  **Vista (View):** Muestra los datos en la interfaz de usuario.
-  **Controlador (Controller):** Maneja las solicitudes del usuario y se comunica con el modelo.
## **MVC en Spring Boot**

En **Spring Boot**, se implementa de la siguiente manera:

- **Modelo (`Model`)** → Representado por las entidades JPA y los repositorios (`@Entity`, `@Repository`).
- **Controlador (`Controller`)** → Gestiona las peticiones HTTP (`@RestController`).
- **Servicio (`Service`)** → Capa intermedia que maneja la lógica de negocio. _(Opcional en MVC clásico, pero recomendada en buenas prácticas para desacoplar código.)_
## **Ejemplo básico de MVC en Spring Boot**

Vamos a crear una `API REST` con `Producto` como entidad.

### **1º Modelo y Repositorio**

>`Producto.java`: El **modelo** representa la entidad en la base de datos.

```java
@Entity
@Table(name = "productos")
public class Producto {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String nombre;
    private Double precio;

    // Constructor vacío
    public Producto() {}

    // Constructor con parámetros
    public Producto(String nombre, Double precio) {
        this.nombre = nombre;
        this.precio = precio;
    }

    // Getters y Setters
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }

    public String getNombre() { return nombre; }
    public void setNombre(String nombre) { this.nombre = nombre; }

    public Double getPrecio() { return precio; }
    public void setPrecio(Double precio) { this.precio = precio; }
}
```

>`ProductoRepository.java`: Se encarga de interactuar con la base de datos usando JPA

```java
@Repository
public interface ProductoRepository extends JpaRepository<Producto, Long> {
    List<Producto> findByNombreContaining(String nombre);
}
```

>Usa **JPA** para interactuar con la base de datos sin necesidad de escribir SQL manualmente.

#### **¿Para qué sirve un repositorio en un API REST con Spring Boot?**

Un **repositorio** en una API REST con **Spring Boot** sirve para **gestionar la comunicación con la base de datos**. Se encarga de realizar operaciones como:

✅ **Guardar datos** en la base de datos.  
✅ **Actualizar registros existentes.**  
✅ **Eliminar registros.**  
✅ **Buscar registros por ID o por condiciones específicas.**

En **Spring Boot**, los repositorios se crean como **interfaces** que extienden `JpaRepository`, lo que permite **interactuar con la base de datos sin necesidad de escribir consultas SQL manualmente**.

##### **Ejemplo de repositorio Producto**
```java
@Repository
public interface ProductoRepository extends JpaRepository<Producto, Long> {
    // Buscar productos por nombre que contengan una palabra clave
    List<Producto> findByNombreContaining(String nombre);
}

```

> **¿Qué hace `JpaRepository`?**  
> Extender `JpaRepository<Producto, Long>` ya nos proporciona métodos como:  
> 🔹 `findAll()` → Obtiene todos los productos.  
> 🔹 `findById(Long id)` → Busca un producto por su ID.  
> 🔹 `save(Producto producto)` → Guarda o actualiza un producto.  
> 🔹 `deleteById(Long id)` → Elimina un producto por su ID.

> **Consulta personalizada:**  
> Hemos añadido `findByNombreContaining(String nombre)`, que nos permite buscar productos por un **nombre parcial** sin escribir SQL.

### **2º Servicio**

El servicio maneja la lógica de negocio y separa la lógica del controlador para mejorar la **organización y reutilización** del código.

>`ProductoService.java`: La capa de servicio se encarga de interactuar con el **repositorio**, asegurando que la lógica de negocio esté separada del controlador.

```java
@Service
public class ProductoService {
    @Autowired
    private ProductoRepository productoRepository;

    public List<Producto> obtenerTodos() {
        return productoRepository.findAll();
    }

    public Producto obtenerPorId(Long id) {
        return productoRepository.findById(id).orElse(null);
    }

    public Producto guardar(Producto producto) {
        return productoRepository.save(producto);
    }

    public void eliminar(Long id) {
        productoRepository.deleteById(id);
    }
}

```

### **3º Controlador**

El controlador **maneja las peticiones HTTP** y se comunica con el servicio.
>`ProductoController.java`: Este controlador expone endpoints REST para **listar, obtener, crear y eliminar productos**.

```java
@RestController
@RequestMapping("/api/productos")
public class ProductoController {
    @Autowired
    private ProductoService productoService;

    @GetMapping
    public List<Producto> listarProductos() {
        return productoService.obtenerTodos();
    }

    @GetMapping("/{id}")
    public ResponseEntity<Producto> obtenerProducto(@PathVariable Long id) {
        Producto producto = productoService.obtenerPorId(id);
        return (producto != null) ? ResponseEntity.ok(producto) : ResponseEntity.notFound().build();
    }

    @PostMapping
    public ResponseEntity<Producto> crearProducto(@RequestBody Producto producto) {
        Producto nuevoProducto = productoService.guardar(producto);
        return ResponseEntity.status(HttpStatus.CREATED).body(nuevoProducto);
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> eliminarProducto(@PathVariable Long id) {
        productoService.eliminar(id);
        return ResponseEntity.noContent().build();
    }
}

```

## **Resumen MVC en Spring Boot**

🔹 **Modelo (`Model`)** → Define las entidades y cómo se almacenan en la base de datos.  
🔹 **Vista (`View`)** → No se usa en APIs REST, pero sería el frontend (React, Angular, etc.).  
🔹 **Controlador (`Controller`)** → Maneja las peticiones HTTP y llama a la capa de servicio.  
🔹 **Servicio (`Service`)** → Se encarga de la lógica de negocio.  
🔹 **Repositorio (`Repository`)** → Permite interactuar con la base de datos mediante JPA.