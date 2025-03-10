## Creación de un Enviroment o Entorno de Trabajo
Siempre que iniciemos un nuevo proyecto, deberemos dirigirnos al apartado **Enviroments** para crear un nuevo entorno de trabajo. En el caso de la aplicación de la tienda online, se nombrara como **TiendaSB**. 

![[Pasted image 20250226122207.png]]

Una vez que hayamos creado nuestra espacio de trabajo, crearemos una variable por defecto que se nombrara como **URL** . Esta variable tendrá como valor http://localhost:8080/ (este es el puerto por defecto de SpringBoot). Su función es evitar que cada vez que queramos comprobar información por Postman no tengamos que escribir toda la url.
Si además tenemos una path Base, como por ejemplo en **TiendaSB**, deberemos agregarla a su valor, en este caso será api -> http://localhost:8080/api .

![EnviromentsV1.2](Explicación-enviroments.png)