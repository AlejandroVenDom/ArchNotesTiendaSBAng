## Instalación de NGX Cookie Service

Este servicio o plugin sirve para configurar la duración de vida de los tokens. Para poder utilizar dicha configuración realizaremos los siguientes pasos.
- Primero nos dirigimos a la pagina Cookie service y copiamos la siguiente ruta que nos aparece que seria *npm i ngx cookie service*

![[Pasted image 20250304110835.png]]

Una vez que la copiemos, nos dirigiremos a la terminar del proyecto al que queramos instalar dichas dependencias.

![[Pasted image 20250304110924.png]]

Una vez que hayamos instalado NGX Cookie Service en nuestro ordenador, crearemos un servicio en nuestra carpeta auth de nuestro proyecto Angular con el nombre de token

![[Pasted image 20250304111057.png]]


## ACCESS_TOKEN | REFRESH_TOKEN

>El **refresh token** es un tipo de token que se utiliza para obtener un **nuevo access token** sin necesidad de que el usuario vuelva a autenticarse.

- El **access token** se utiliza para acceder a los recursos protegidos de una API o servidor. Tiene una vida útil corta, generalmente minutos u horas.
- El **refresh token**, en cambio, tiene una vida útil más larga, a veces incluso meses o años, y se utiliza para **renovar** el access token cuando este haya expirado.



