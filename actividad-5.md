---
description: Docker Compose
---

# Actividad 5

## Docker Compose

### Referencias

Comandos disponibles para `docker-compose`:

* `build`

  ```text
  Services are built once and then tagged as `project_service`,
  e.g. `composetest_db`. If you change a service's `Dockerfile` or the
  contents of its build directory, you can run `docker-compose build` to rebuild it.
  ```

* `create`

  ```text
  Creates containers for a service.
  This command is deprecated. Use the `up` command with `--no-start` instead.
  ```

* `down`

  ```text
  Stops containers and removes containers, networks, volumes, and images
  created by `up`.

  By default, the only things removed are:
  - Containers for services defined in the Compose file
  - Networks defined in the `networks` section of the Compose file
  - The default network, if one is used

  Networks and volumes defined as `external` are never removed.
  ```

* `start`

  ```text
  Start existing containers.
  ```

* `stop`

  ```text
  Stop running containers without removing them.
  They can be started again with `docker-compose start`.

  ```

* `restart`

  ```text
  Restart running containers.
  ```

* `up`

  ```text
  Builds, (re)creates, starts, and attaches to containers for a service.
  Unless they are already running, this command also starts any linked services.

  Usage: up [options] [--scale SERVICE=NUM...] [SERVICE...]

  Options:
      -d, --detach               Detached mode: Run containers in the background,
                                print new container names. Incompatible with
                                --abort-on-container-exit.
      --no-deps                  Don't start linked services.
      --force-recreate           Recreate containers even if their configuration
                                and image haven't changed.
      --always-recreate-deps     Recreate dependent containers.
                                Incompatible with --no-recreate.
      --no-recreate              If containers already exist, don't recreate
                                them. Incompatible with --force-recreate and -V.
      --no-build                 Don't build an image, even if it's missing.
      --no-start                 Don't start the services after creating them.
      --build                    Build images before starting containers.
  ```

* `rm`

  ```text
  Removes stopped service containers.

  By default, anonymous volumes attached to containers will not be removed. You
  can override this with `-v`. To list all volumes, use `docker volume ls`.
  ```

* `run`

  ```text
  Run a one-off command on a service.

  For example:

      $ docker-compose run web python manage.py shell

  By default, linked services will be started, unless they are already
  running. If you do not want to start linked services, use
  `docker-compose run --no-deps SERVICE COMMAND [ARGS...]`.
  ```

* `exec`

  ```text
  Execute a command in a running container

  Usage: exec [options] [-e KEY=VAL...] SERVICE COMMAND [ARGS...]

  Options:
      -d, --detach      Detached mode: Run command in the background.
      --privileged      Give extended privileges to the process.
      -u, --user USER   Run the command as this user.
      -T                Disable pseudo-tty allocation. By default `docker-compose exec`
                        allocates a TTY.
      --index=index     index of the container if there are multiple
                        instances of a service [default: 1]
      -e, --env KEY=VAL Set environment variables (can be used multiple times,
                        not supported in API < 1.25)
      -w, --workdir DIR Path to workdir directory for this command.
  ```

Ejecutando **`docker-compose COMANDO --help`** se obtiene una descripción y forma de uso del comando.

### Ejercicios

1. Instalación de Wordpress:
   * Copiar el **`docker-compose.yml`** visto en la teoría y replicar la instalación.

    ```yaml
    version: '3.8'

    services:
      db:
        image: mysql:5.7
        volumes:
          - db_data:/var/lib/mysql
        restart: always
        environment:
          MYSQL_ROOT_PASSWORD: somewordpress
          MYSQL_DATABASE: wordpress
          MYSQL_USER: wordpress
          MYSQL_PASSWORD: wordpress

      wordpress:
        depends_on:
          - db
        image: wordpress:latest
        volumes:
          - wp_data:/var/www/html
        ports:
          - "80:80"
        restart: always
        environment:
          WORDPRESS_DB_HOST: db:3306
          WORDPRESS_DB_USER: wordpress
          WORDPRESS_DB_PASSWORD: wordpress
          WORDPRESS_DB_NAME: wordpress
    volumes:
      db_data: {}
      wp_data: {}
    ```

   * Analizar cada opción y relacionar cada una de estas con las opciones de **`docker run`**.
   * Agregar un nuevo servicio que permita gestionar la base de datos usando `phpmyadmin`.

    Hay que agregar el servicio en el `docker-compose.yml`

    ```yml
      phpmyadmin:
        depends_on:
          - db
        image: phpmyadmin/phpmyadmin
        restart: always
        ports:
          - '8080:80'
        environment:
          PMA_HOST: db
          MYSQL_ROOT_PASSWORD: somewordpress
    ```

    Luego correr `docker-compose up` que crea el nuevo contenedor que corre el servicio
    de phpMyAdmin:

    [http://localhost:8080/]

    Username: `root`
    Passvord: `somewordpress`

2. Indicar las diferencias entre los siguientes comandos:
   * **`docker-compose create`** y **`docker-compose up`**
   * **`docker-compose stop`** y **`docker-compose down`**
   * **`docker-compose run`** y **`docker-compose exec`**

3. Redmine
   * Siguiendo la documentación de la imagen oficial de redmine instanciarlo usando el compose propuesto en su máquina.

      ```yml
      version: '3.8'

      services:
        db:
          image: mysql:5.7
          volumes:
            - db_data:/var/lib/mysql
          restart: always
          environment:
            MYSQL_RANDOM_ROOT_PASSWORD: supersecretkey
            MYSQL_DATABASE: redmine
            MYSQL_USER: redmine
            MYSQL_PASSWORD: secret

        phpmyadmin:
          image: phpmyadmin/phpmyadmin
          restart: always
          ports:
            - '8080:80'
          environment:
            PMA_HOST: db
            MYSQL_ROOT_PASSWORD: supersecretkey

        redmine:
          image: redmine:4.2.1
          volumes:
            - redmine_data:/usr/src/redmine/files
          ports:
            - "8000:3000"
          restart: always
          environment:
            REDMINE_DB_MYSQL: db
            REDMINE_DB_USERNAME: redmine
            REDMINE_DB_PASSWORD: secret
            REDMINE_SECRET_KEY_BASE: supersecretkey2
      volumes:
        db_data: {}
        redmine_data: {}
      ```

   * Analizar qué volúmenes debe agregar para que la solución persista, tanto para los datos propios de la base, como los uploads que se realicen en redmine.

     Además de persistir la bd como ya se ha visto, se pueden guardar los archivos de redmine haciendo un volumen de `/usr/src/redmine/files`

   * Acceder al redmine con usuario admin y password admin y navegar el sistema.

     Por algún motivo no puedo crear issues en los projectos, pero si subir archivos etc.

   * Eliminar los servicios usando **docker-compose down** y verificar que si se reinicia siguen funcionando.

     Sip.

   * Verificar en la administración de redmine, que no hay plugins instalados.

     Administration > Plugins > "No data to display"

   * Analizar cómo puede instalarse el plugin [Additionals](https://github.com/alphanodes/additionals) en la instancia de redmine.

     > En el directorio donde esté el proyecto de compose, puede clonar el repositorio de additionals usando:

     ```bash
      git clone -b v3-stable https://github.com/AlphaNodes/additionals.git plugins/additionals
     ```

     > Considerar que el directorio clonado debe montarse en el contenedor en el directorio: **`/usr/src/redmine/plugins`**. Además debe tener en cuenta que los plugins requieren correr **database migrations** y existe una opción en la imagen docker que lo considera.

     Agrego un volumen:
     `- ./plugins:/usr/src/redmine/plugins`

     Donde previamente descargué el plugin de github

     importante variable `REDMINE_PLUGINS_MIGRATE: 1`

4. Cambiar el ejercicio anterior para que construya una imagen de Redmine con el plugin mencionado incorporado.

  Nota: la rama `v3-stable` ya no existe

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
