# Práctica Servidor Backend con Docker: Spring Boot, PostgreSQL y pgAdmin

## 1. Título

Automatización del despliegue de una aplicación backend con PostgreSQL y pgAdmin usando Docker y Docker Compose

## 2. Tiempo de duración

90 minutos

## 3. Fundamentos

Docker permite contenerizar aplicaciones y sus dependencias, asegurando consistencia en entornos locales y productivos.
En esta práctica, se automatiza el despliegue de una aplicación backend desarrollada desde cero con Spring Boot, conectada a una base de datos PostgreSQL, y se incluye pgAdmin como herramienta visual de administración.
Se utiliza `docker-compose` para orquestar los servicios, definiendo redes personalizadas y volúmenes persistentes. Además, se emplean técnicas de construcción multi-stage para optimizar la imagen final de la aplicación.

## 4. Conocimientos previos

* Fundamentos de aplicaciones backend (Spring Boot)
* Comandos básicos de Docker y Docker Compose
* Conceptos de redes y volúmenes en Docker
* Variables de entorno y conexión a base de datos
* Fundamentos básicos de PostgreSQL

## 5. Objetivos a alcanzar

* Crear servicios de PostgreSQL y pgAdmin en Docker Compose
* Configurar volúmenes y redes personalizadas
* Crear un `Dockerfile` para contenerizar la aplicación backend
* Aplicar multi-stage builds para optimizar la imagen
* Validar conexión entre aplicación backend y base de datos
* Acceder a pgAdmin y verificar conexión con PostgreSQL
* Desarrollar una API funcional que permita insertar y consultar usuarios

## 6. Equipo necesario

* Computadora con Docker y Docker Compose instalados
* Java 21 y Maven
* Navegador Web
* Editor de código (VSCode recomendado)

## 7. Material de apoyo

* [Documentación oficial de Docker](https://docs.docker.com/)
* [Spring Boot Reference](https://spring.io/projects/spring-boot)
* [PostgreSQL Documentation](https://www.postgresql.org/docs/)
* [pgAdmin Docs](https://www.pgadmin.org/docs/)
* [Dockerfile Multi-Stage Builds](https://docs.docker.com/build/building/multi-stage/)

## 8. Procedimiento

### Paso 1: Crear backend desde cero (Spring Boot)

Repositorio del backend: [Link al repositorio](https://github.com/ECarpio841/backend_for_docker)

* Proyecto generado con Spring Initializr
* Dependencias: Spring Web, Spring Data JPA, PostgreSQL Driver
* Clase `User`: entidad JPA con campos `id`, `name`, `email`
* Controlador con endpoints `GET /users` y `POST /users`

Figura 1: Creación de proyecto en SpringBoot desde 0

![Figura 1: Creación de proyecto en SpringBoot desde 0](src/image%20copy.png)

### Paso 2: Crear archivo `.env` con variables de entorno

```env
POSTGRES_DB=demo
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
PGADMIN_DEFAULT_EMAIL=admin@admin.com
PGADMIN_DEFAULT_PASSWORD=admin
SPRING_DATASOURCE_URL=jdbc:postgresql://postgres:5432/demo
SPRING_DATASOURCE_USERNAME=postgres
SPRING_DATASOURCE_PASSWORD=postgres
```

### Paso 3: Crear `docker-compose.yml` documentado y funcional

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:16
    container_name: postgres_container
    restart: always
    environment:
      POSTGRES_DB: ${POSTGRES_DB}
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - backend_net
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U $$POSTGRES_USER"]
      interval: 5s
      timeout: 5s
      retries: 5

  pgadmin:
    image: dpage/pgadmin4
    container_name: pgadmin_container
    restart: always
    environment:
      PGADMIN_DEFAULT_EMAIL: ${PGADMIN_DEFAULT_EMAIL}
      PGADMIN_DEFAULT_PASSWORD: ${PGADMIN_DEFAULT_PASSWORD}
    ports:
      - "5050:80"
    networks:
      - backend_net
    depends_on:
      - postgres

  backend:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: backend_container
    ports:
      - "8080:8080"
    environment:
      SPRING_DATASOURCE_URL: ${SPRING_DATASOURCE_URL}
      SPRING_DATASOURCE_USERNAME: ${SPRING_DATASOURCE_USERNAME}
      SPRING_DATASOURCE_PASSWORD: ${SPRING_DATASOURCE_PASSWORD}
    networks:
      - backend_net
    depends_on:
      postgres:
        condition: service_healthy

volumes:
  postgres_data:

networks:
  backend_net:
```

Figura 2: Creación de docker-compose.yml

![Figura 2: Creación de docker-compose.yml](src/image%20copy%202.png)

### Paso 4: Crear Dockerfile con multi-stage

```dockerfile
# Etapa 1: Build con Java 21
FROM maven:3.9.6-eclipse-temurin-21 AS build
WORKDIR /app
COPY . .
RUN mvn clean package -DskipTests

# Etapa 2: Runtime con Java 21
FROM eclipse-temurin:21-jdk
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

### Paso 5: Levantar la infraestructura

```bash
docker-compose up --build -d
```


Figura 3 y 4: Levantamiento de infraestrcuturas:

![Figura 3: Levantamiento de la infraestructura](src/image%20copy%206.png)

![Figura 4: Levantamiento de la infraestructura](src/image%20copy%209.png)

### Paso 6: Verificar servicios activos

* Backend: [http://localhost:8080/users](http://localhost:8080/users)

![Figura 5: Servicio de backend activo](src/image%20copy%207.png)



* pgAdmin: [http://localhost:5050](http://localhost:5050)

![Figura 6: Servicio de pgAdmin activo](src/image%20copy%203.png)


### Paso 7: Probar funcionalidad con Postman

**POST**: [http://localhost:8080/users](http://localhost:8080/users)

![Figura 7: Probar post con postman](src/image%20copy%204.png)



```json
{
  "name": "Esteban Carpio",
  "email": "esteban@example.com"
}
```


---


**GET**: [http://localhost:8080/users](http://localhost:8080/users)


![Figura 8: Probar GET con postman](src/image%20copy%205.png)

```json
[
  {
    "id": 1,
    "name": "Esteban Carpio",
    "email": "esteban@example.com"
  }
]
```




## BACKEND DESPLEGADO:
![Figura 5: Servicio de backend activo](src/image%20copy%207.png)


## 9. Resultados esperados

* La aplicación backend se ejecuta y accede a PostgreSQL correctamente
* pgAdmin permite ver y administrar la base de datos visualmente
* Todos los contenedores se encuentran conectados por red `backend_net`
* El uso de `multi-stage` reduce el tamaño de la imagen final

## 10. Diagrama de arquitectura del despliegue

![Figura 2: Visualización de postgreSQL](src/image%20copy%208.png)

Descripción del Diagrama de Arquitectura del Despliegue

El diagrama ilustra la arquitectura lógica del entorno de despliegue automatizado de una aplicación backend utilizando Docker y Docker Compose. Se compone de los siguientes elementos principales:

🔹 Usuario Externo

Representa al cliente o desarrollador que accede al sistema desde el exterior del host. Tiene dos vías de acceso:

A la aplicación backend a través del navegador o herramientas como Postman (por el puerto 8080).

A la interfaz visual de pgAdmin a través del navegador en http://localhost:5050.

🔹 Docker Host (localhost)

Este bloque representa la máquina donde se ejecutan los contenedores. Dentro de este host se encuentra definida una red personalizada de Docker llamada backend_net, que conecta a los tres servicios del sistema.

🔹 Contenedor: Spring Boot App (backend)

Es una aplicación Java creada con Spring Boot que expone una API REST.

Está contenida en su propio contenedor Docker.

Expone el puerto 8080, lo que permite acceso desde el exterior.

Se conecta a la base de datos PostgreSQL mediante JDBC, utilizando la URL:

jdbc:postgresql://postgres:5432/demo

🔹 Contenedor: PostgreSQL (postgres)

Motor de base de datos relacional que almacena la información de la aplicación.

No expone puertos al exterior directamente.

Internamente, escucha por el puerto 5432 y recibe variables de entorno como:

POSTGRES_DB=demo

POSTGRES_USER=postgres

POSTGRES_PASSWORD=postgres

Recibe conexiones tanto desde la app backend como desde pgAdmin.

🔹 Contenedor: pgAdmin

Es una interfaz visual que permite la administración y consulta de la base de datos PostgreSQL.

Expone el puerto 80 dentro del contenedor y es mapeado al puerto 5050 del host.

Utiliza las mismas credenciales (postgres / postgres) para conectarse a la base de datos.

Es accesible desde el navegador mediante http://localhost:5050.

🔹 Comunicación y redes

Todos los contenedores están conectados a la red backend_net, lo que permite el uso de nombres de servicios como postgres para referenciar la base de datos.

Las conexiones están claramente definidas:

Spring Boot App → PostgreSQL: mediante JDBC.

pgAdmin → PostgreSQL: mediante cliente visual.

🔹 Importancia del healthcheck y depends_on

La configuración asegura que el backend no intente conectarse antes de que PostgreSQL esté listo, gracias al uso de healthcheck y depends_on: condition: service_healthy. con los tres servicios (backend, postgres, pgadmin) conectados a una red Docker*

## 11. Conclusiones

Durante esta práctica se logró automatizar el despliegue completo de una aplicación backend utilizando contenedores Docker. La arquitectura modular permite una mejor organización, mantenibilidad y portabilidad de la aplicación. El uso de pgAdmin facilita la inspección y prueba de la base de datos.

## 12. Bibliografía

* Docker. (s.f.). Docker Documentation. [https://docs.docker.com/](https://docs.docker.com/)
* PostgreSQL Global Development Group. [https://www.postgresql.org/docs/](https://www.postgresql.org/docs/)
* pgAdmin. [https://www.pgadmin.org/docs/](https://www.pgadmin.org/docs/)
* Spring Boot Reference Guide. [https://spring.io/projects/spring-boot](https://spring.io/projects/spring-boot)

## 13. Audio Explicativo

[Audio explicativo práctica supletorios ](https://drive.google.com/file/d/19WwBNcetS9BKXmNRa2qkYJU5lhr25S3f/view?usp=sharing)
