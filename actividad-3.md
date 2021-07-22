# Actividad 3

## Registries

### **1.** Utilizando la [imagen docker oficial de una registry](https://hub.docker.com/_/registry), instanciar localmente una registry

### **2.** Pushear una imagen a la nueva registry

* Crear una imagen a partir de nginx que cambie el index.html
* Pushear en la nueva imagen a la registry instanciada en el ejercicio 1.

### **3.** Eliminar del docker engine local la imagen antes pusheada

```bash
docker rmi <IMAGEN>
```

Descargue desde la registry creada en el ejercicio 1, la imagen eliminada recientemente.

## Volúmenes

### **1.** Crear un contenedor con un **volumen anónimo**

* Crear un archivo dentro del directorio definido como un volumen.
* Verificar el estado del contenedor y los volúmenes creados.
* Realizar un inspect del volumen y obtener el directorio en donde se encuentra almacenado.
* ¿Existe el archivo creado anteriormente en el directorio obtenido con el inspect?

```bash 
$ docker run -d --name voltest --mount type=volume,target=/app nginx:latest
$ docker exec voltest touch /app/prueba.txt
$ docker inspect voltest
...
"Mounts": [
            {
                "Type": "volume",
                "Name": "1b3451eb3de572ebeb15373587e4ea15b90ec4e324ce9951ffb7f8f3e88857a4",
                "Source": "/var/lib/docker/volumes/1b3451eb3de572ebeb15373587e4ea15b90ec4e324ce9951ffb7f8f3e88857a4/_data",
                "Destination": "/app",
                "Driver": "local",
                "Mode": "z",
                "RW": true,
                "Propagation": ""
            }
        ],
...

$ docker volume ls
DRIVER    VOLUME NAME
local     1b3451eb3de572ebeb15373587e4ea15b90ec4e324ce9951ffb7f8f3e88857a4

$ docker volume inspect 1b3451eb3de572ebeb15373587e4ea15b90ec4e324ce9951ffb7f8f3e88857a4 
[
    {
        "CreatedAt": "2021-07-22T02:34:22-03:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/1b3451eb3de572ebeb15373587e4ea15b90ec4e324ce9951ffb7f8f3e88857a4/_data",
        "Name": "1b3451eb3de572ebeb15373587e4ea15b90ec4e324ce9951ffb7f8f3e88857a4",
        "Options": null,
        "Scope": "local"
    }
]

$ ls /var/lib/docker/volumes/1b3451eb3de572ebeb15373587e4ea15b90ec4e324ce9951ffb7f8f3e88857a4/_data
prueba.txt
```

### **2.** Repetir los pasos del ejercicio 1

* ¿Existen los archivos creados anteriormente? Justifique su respuesta.

> Utilizar `docker volume ls` y `docker volume inspect`

```bash
$ docker rm voltest -f
$ docker run --name voltest --mount type=volume,target=/app nginx:latest
$ docker exec voltest touch /app/prueba2.txt
$ docker inspect voltest
$ docker volume ls
DRIVER    VOLUME NAME
local     1b3451eb3de572ebeb15373587e4ea15b90ec4e324ce9951ffb7f8f3e88857a4
local     8703e150b9042e0933bd17987db6f9f15c8c62ea534a2e04f69b713bf88680bf

$ docker volume inspect 8703e150b9042e0933bd17987db6f9f15c8c62ea534a2e04f69b713bf88680bf
[
    {
        "CreatedAt": "2021-07-22T02:40:26-03:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/8703e150b9042e0933bd17987db6f9f15c8c62ea534a2e04f69b713bf88680bf/_data",
        "Name": "8703e150b9042e0933bd17987db6f9f15c8c62ea534a2e04f69b713bf88680bf",
        "Options": null,
        "Scope": "local"
    }
]

$ ls /var/lib/docker/volumes/8703e150b9042e0933bd17987db6f9f15c8c62ea534a2e04f69b713bf88680bf/_data
prueba2.txt
```

Son distintos los archivos porque son distintos volúmenes.

### **3.** Instanciar nuevamente un contenedor con un volumen anónimo añadiendo la opción `--rm` y verificar el comportamiento de los contenedores y volúmenes al finalizar. ¿Cómo es el ciclo de vida del volumen en este caso?

El volúmen existe mientras está corriendo el contenedor

```bash
$ docker run --rm -d --name voltest --mount type=volume,target=/app nginx:latest
$ docker volume ls
DRIVER    VOLUME NAME
local     e60d1292f8698444c4decddd7428ceb6a0475c470d2e8552c58d11f089fabcac
$ docker stop voltest 
voltest
$ docker volume ls
DRIVER    VOLUME NAME


```

### **4.** Crear un contenedor con un **volumen nombrado**

* Crear un archivo dentro del directorio definido como volumen.
* Verificar el estado del contenedor y los volúmenes creados.
* Realizar un inspect del volumen y obtener el directorio en donde se encuentra almacenado.
* ¿Existe el archivo creado anteriormente en el directorio obtenido con el inspect?

### **5.** Repetir el ejercicio 4

* ¿Existen los archivos creados anteriormente? Justifique su respuesta.

> Utilizar **docker volume ls** y **docker volume inspect**

### **6.** Reutilizar el volumen creado en el ejercicio 4

* Utilizar una imagen diferente y un punto de montaje diferente al utilizado en el punto 4.
* Al iniciar el contenedor verificar si existen los archivos creados anteriormente en el nuevo punto de montaje.

### **7.** Crear un contenedor con un **volumen bind mount**

* Crear un directorio en su máquina.
* Montar a este directorio como un volumen.
* Crear archivos en el directorio creado.
  * ¿Puede ver estos nuevos archivos dentro del contenedor?
* En el contenedor creado moverse al directorio donde se encuentra montado el volumen y agregar nuevos archivos.
  * ¿Es posible ver estos nuevos archivos en el directorio de su SO?

### **8.** Crear un contenedor que utilice un volúmen anónimo

* Crear un segundo contenedor que utilice el mismo volumen que el contenedor creado anteriormente.

> Utilizar `--volumes-from`

### **9.** Crear una instancia de postgres

* Descargue la imagen de postgres versión 12
* Utilice `docker inspect postgres:12` para observar que volúmenes exporta la imagen.
* Instancie un contenedor basado en la imagen descargada, utilice un volumen nombrado para persistir la base de datos.

  > Recomendamos dar un nombre al contenedor. Además setear la password y mapeo de puertos:
  >
  > `docker run --name=psql-docker -e POSTGRES_PASSWORD=psql-docker -p 5432:5432 ...`

* Compruebe el volumen que creó.
* Conectar a la base de datos utilizando pgadmin4 como cliente:

  ```bash
  docker run -p 80:80 \
    -e 'PGADMIN_DEFAULT_EMAIL=docker@mikroways.net' \
    -e 'PGADMIN_DEFAULT_PASSWORD=mikroways' \
    --link psql-docker:db -d dpage/pgadmin4
  ```

* Utilizando un navegador ingresar a localhost y acceder con el usuario:**docker@mikroways.net** y la contraseña **mikroways**.
* Conectar a una base de datos:
  * Server: **db**
  * User: **postgres**
  * Password: **psql‑docker**
* Cree una base de datos “prueba”.
* Termine la ejecución del contenedor y bórrelo.
* ¿El volumen nombrado continua existiendo?
* Instancie un nuevo contenedor con características similares al anterior
  * Utilice el mismo volumen nombrado.
  * ¿Continua existiendo la base de datos creada?
* Dentro de la imagen de postgres existe la utilidad psql.
  * Utilícela en combinación con docker exec para acceder a la base de datos.

#### Resol 9

```bash
$ docker pull postgres:12
$ docker inspect postgres:12 
...
            "Volumes": {
                "/var/lib/postgresql/data": {}
            },
...
$ docker volume create psql-data
psql-data
$ docker run -d --name=psql-docker \
 -e 'POSTGRES_PASSWORD=psql-docker' \
 -e 'POSTGRES_USER=postgres' \
 -e 'POSTGRES_DB=TEST_SM' \
 -p 5432:5432 \
 --mount type=volume,source=psql-data,target=/var/lib/postgresql/data \
 postgres:12

$ docker volume inspect psql-data 
[
    {
        "CreatedAt": "2021-07-22T15:54:18-03:00",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/psql-data/_data",
        "Name": "psql-data",
        "Options": {},
        "Scope": "local"
    }
]

$ docker run -p 5555:80 \
  -e 'PGADMIN_DEFAULT_EMAIL=docker@mikroways.net' \
  -e 'PGADMIN_DEFAULT_PASSWORD=mikroways' \
  --link psql-docker:db -d dpage/pgadmin4

$ docker logs $(docker ps -lq)
NOTE: Configuring authentication for SERVER mode.

[2021-07-22 18:56:47 +0000] [1] [INFO] Starting gunicorn 20.1.0
[2021-07-22 18:56:47 +0000] [1] [INFO] Listening at: http://[::]:80 (1)
[2021-07-22 18:56:47 +0000] [1] [INFO] Using worker: gthread
[2021-07-22 18:56:48 +0000] [92] [INFO] Booting worker with pid: 92

```

En la interfaz cuando queria agregar un servidos no reconocía **db**
entonces:

```bash
$ docker inspect psql-docker
...
"Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "22c689dcaed471ddcf89d85759e9046597885e25b843f25a6873669288ad062d",
                    "EndpointID": "c4d281f08bb0e2c7c7fc44c1338df1016b9639c894f86143b859822f16631fa4",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",

```

Probando con el IP en el campo name/address funca.
Creo bd "prueba".

```bash
$ docker rm -f psql-docker
$ sudo ls /var/lib/docker/volumes/psql-data/_data
base	      pg_dynshmem    pg_logical    pg_replslot	 pg_stat      pg_tblspc    pg_wal		 postgresql.conf
global	      pg_hba.conf    pg_multixact  pg_serial	 pg_stat_tmp  pg_twophase  pg_xact		 postmaster.opts
pg_commit_ts  pg_ident.conf  pg_notify	   pg_snapshots  pg_subtrans  PG_VERSION   postgresql.auto.conf

$ docker run -d --name=psql-docker \
 -e 'POSTGRES_PASSWORD=psql-docker' \
 -e 'POSTGRES_USER=postgres' \
 -e 'POSTGRES_DB=TEST_SM' \
 -p 5432:5432 \
 --mount type=volume,source=psql-data,target=/var/lib/postgresql/data \
 postgres:12
```

Si se recarga la pagina de pgAdmin, vuelve a aparecer la db lo más bien.

Acceder a la BD:

```bash
docker exec -it psql-docker psql -U postgres
psql (12.7 (Debian 12.7-1.pgdg100+1))
Type "help" for help.

postgres=# 
```

No pide password ya que la obtiene de las variables de entorno seteadas al momento de crear el contenedor.

### **10.** Verifiqué el espacio utilizado por docker: `docker system df`

### **11.** Libere todo el espacio utilizado por volúmenes docker

* Pare todos los contenedores
* Elimine todos los volúmenes en desuso con **prune**

## Entregables

### Los ejercicios a entregar son sobre volúmenes y únicamente 2, 3 y 9. Las entregas deben responder a las preguntas de cada ejercicio o los comandos usados para completarlos
