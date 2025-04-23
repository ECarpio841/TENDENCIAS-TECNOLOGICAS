# Practica Red de Contenedores
## 1. Configuración de una red de contenedores con MySQL y PhpMyAdmin
## 2. Tiempo de diración.
30 minutos
## 3. Fundamentos:

La presente práctica se basa en el uso de Docker, una herramienta de virtualización a nivel de contenedor que permite empaquetar, distribuir y ejecutar aplicaciones de forma aislada. A diferencia de las máquinas virtuales tradicionales, Docker no necesita un sistema operativo completo para cada instancia, lo cual lo hace más ligero y eficiente. Esta característica es especialmente útil en entornos de desarrollo, pruebas e implementación continua.

¿Qué es un contenedor?
Un contenedor en Docker es una instancia ejecutable de una imagen que incluye todo lo necesario para ejecutar una aplicación: código, librerías, herramientas de sistema, etc. Se comporta como un proceso aislado del sistema operativo principal, pero puede comunicarse con otros contenedores a través de redes virtuales.

 Docker y su ecosistema
Docker trabaja con los siguientes componentes:

Docker Engine: el servicio que corre en segundo plano y permite la ejecución de contenedores.

Docker CLI: la herramienta de línea de comandos que usamos para crear, iniciar y administrar contenedores.

Docker Hub: un repositorio en línea desde donde se pueden descargar imágenes preconstruidas como mysql o phpmyadmin.

Contenedor MySQL
MySQL es un sistema gestor de bases de datos relacional (RDBMS). Al levantar un contenedor de MySQL con Docker, se define una contraseña de root, se puede crear una base de datos inicial, y establecer usuarios con permisos. Este contenedor funcionará como el servidor de bases de datos de nuestro entorno.

Contenedor phpMyAdmin
phpMyAdmin es una herramienta web que permite administrar bases de datos MySQL de forma gráfica. Con Docker, podemos lanzar esta herramienta como un contenedor aparte que se conecta al contenedor de MySQL mediante una red Docker personalizada, permitiendo así la gestión visual de las bases de datos sin necesidad de conectarnos por terminal.

Redes en Docker
Para que dos contenedores puedan comunicarse (por ejemplo, phpMyAdmin y MySQL), se deben conectar a una misma red Docker. Esta red virtual funciona como un switch que conecta ambos servicios internamente por sus nombres de contenedor (como si fueran nombres DNS).




## 4. Conocimientos previos.

- Habiitar y correr Docker.
- Comandos básicos de docker.
- Comandos para conectar contenedores a una misma red.

## 5. Objetivos a alcanzar
   
- Crear una red personalizada dentro de docker.
- Configurar las imágenes de MySQL y PhpMyAdmin para que estas se conecten a la misma red.
- Crear una base de datos de prueba dentro de la interfáz de PhpMyAdmin para confirmar que la conección se estableció en MySQL.

## 6. Equipo necesario:
  
- Computador con sistema operativo Windows/Linux/Mac. (En mi caso utilicé Fedora 41)
- Docker.
- Imágenes de PhpMyAdmin y MySQL.

## 7. Material de apoyo.
   
- Documentacion de docker.
- DockerHub.
- Cheat sheet redes de contenedores.
- Video Guía.

## 8. Procedimiento

Paso 1: En mi caso principalmente cree la red de contenedores, ya que al usar unos parámetro más en el comando para crear cada contenedor con su respectiva imágen se puede establecer una conección más rápida.
- A esta red la llamé: mysql-net

![Figura 1: Creación de red de contenedores.](src/Captura%20desde%202025-04-23%2000-44-21.png)
Figura 1. Creación de red de contenedores.


Paso 2: Después de esto cree el respectivo contenedor para MySQL y para PhpMyAdmin.
- Al contenedor de MyAQL lo nombré "mysql-container" y al de PhpMyAdmin lo nombré "phpmyadmin-container"
- Justamente aquí añadimos los parámeetros que tienen que ver con las credenciales de los contenedores com0o nombres, contraseñas de root y de usuarios, pero justamente también hay un parámetro que podemos añadir para conectar a la red de contenedores a cada contenedor a penas se los creen y este es: 

```
--network mysql-net \
```

![](src/Captura%20desde%202025-04-23%2000-45-02.png)
Figura 2. Creación de contenedor de MySQL.


![](src/Captura%20desde%202025-04-23%2000-45-34.png)
Figura 3. Creación de contenedor de PhpMyAdmin.

- Dentro de MySQL, al crear el contenedor también ya se puede crear una base de datos que luego puede verse reflejada dentro de PhpMyAdmin que se llama prueba, esto añadiendo esto a la creación del contenedor:

```
-e MYSQL_DATABASE=prueba \
```

Paso 4: Entramos a:

```
http://localhost:8080
```
de este modo podemos ver la interfaz lista de PhpMyAdmin:

![](src/Captura%20desde%202025-04-23%2001-17-43.png)

Figura 4. Interfáz de PhpMyAdmin funcionando en el navegador.

Y del mismo modo podemos ver la base de datos "prueba" que ya hemos creado en la creación del contenedor de MySQL:

![](src/Captura%20desde%202025-04-23%2000-46-57.png)
Figura 5. Base de datos "prueba" en PhpMyAdmin.

Paso 5: Con todo listo, podemos crear una base de datos de prueba DENTRO de la interfáz de PhpMyAdmin:
- A esta base la llamaré Prueba 2.

![](src/Captura%20desde%202025-04-23%2001-21-08.png)
Figura 6. Base de datos "prueba2" en PhpMyAdmin.

. Por último añadimos datos para llenar esta base de datos:

![](src/Captura%20desde%202025-04-23%2001-24-34.png)
Figura 7. Creación de tabla dentro de "prueba2".

## 9. Resultados esperados:
    
Crear y ejecutar correctamente dos contenedores Docker: uno para MySQL y otro para phpMyAdmin.

Establecer una red personalizada en Docker que permita la comunicación directa entre ambos contenedores.

Acceder a la interfaz web de phpMyAdmin desde el navegador y verificar que la conexión con el contenedor de MySQL es exitosa.

Crear una base de datos de prueba y al menos una tabla utilizando la interfaz de phpMyAdmin.

Insertar y visualizar datos básicos, comprobando así el correcto funcionamiento del sistema gestor de base de datos desde el entorno contenerizado.

Estos resultados demuestran la comprensión de los conceptos básicos de contenedores, redes Docker y administración de bases de datos mediante herramientas visuales como phpMyAdmin.



## 10. Bibliografía
 
- Docker Inc. (2023). *Get Started with Docker*. Docker Documentation.  
  https://docs.docker.com/get-started/

- Docker Inc. (2023). *Docker Networks Overview*. Docker Documentation.  
  https://docs.docker.com/network/

- phpMyAdmin. (2024). *phpMyAdmin Documentation*.  
  https://docs.phpmyadmin.net/
