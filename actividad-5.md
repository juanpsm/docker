---
description: Docker Compose
---

# Actividad 5

## Docker Compose

### Referencias

Comandos disponibles para `docker-compose`:

* `build`
* `create`
* `down`
* `start`
* `stop`
* `restart`
* `up`
* `rm`
* `run`
* `exec`

Ejecutando **`docker-compose COMANDO --help`** se obtiene una descripción y forma de uso del comando.

### Ejercicios

1. Instalación de Wordpress:
   * Copiar el **`docker-compose.yml`** visto en la teoría y replicar la instalación.
   * Analizar cada opción y relacionar cada una de estas con las opciones de **`docker run`**.
   * Agregar un nuevo servicio que permita gestionar la base de datos usando `phpmyadmin`.
2. Indicar las diferencias entre los siguientes comandos:
   * **`docker-compose create`** y **`docker-compose up`**
   * **`docker-compose stop`** y **`docker-compose down`**
   * **`docker-compose run`** y **`docker-compose exec`**
3. Redmine
   * Siguiendo la documentación de la imagen oficial de redmine instanciarlo usando el compose propuesto en su máquina.
   * Analizar qué volúmenes debe agregar para que la solución persista, tanto para los datos propios de la base, como los uploads que se realicen en redmine.
   * Acceder al redmine con usuario admin y password admin y navegar el sistema.
   * Eliminar los servicios usando **docker-compose down** y verificar que si se reinicia siguen funcionando.
   * Verificar en la administración de redmine, que no hay plugins instalados.
   * Analizar cómo puede instalarse el plugin [Additionals](https://github.com/alphanodes/additionals) en la instancia de redmine.

     > En el directorio donde esté el proyecto de compose, puede clonar el repositorio de additionals usando:

     ```bash
      git clone -b v3-stable https://github.com/AlphaNodes/additionals.git plugins/additionals
     ```

     > Considerar que el directorio clonado debe montarse en el contenedor en el directorio: **`/usr/src/redmine/plugins`**. Además debe tener en cuenta que los plugins requieren correr **database migrations** y existe una opción en la imagen docker que lo considera.
4. Cambiar el ejercicio anterior para que construya una imagen de Redmine con el plugin mencionado incorporado.
5. Indique los comandos para conectar con el servicio de redmine directamente con la consola de ruby on rails. En esa consola ejecutar

   ```ruby
    user = User.find\_by login: 'admin'
    user.password = 'Mikroways2020'
    user.save!
   ```

   Luego, vuelva a iniciar sesión verificando que la contraseña del usuario **admin** ahora es **Mikroways2020**

   > Para conectar a la consola de rails, se corre el comando **rails console**

## Entregables

### Los ejercicios a entregar son todos. Las entregas deben responder a las preguntas de cada ejercicio, los comandos usados para completarlos, archivos de compose o Dockerfiles

