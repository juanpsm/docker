# Actividad 2

## Construcción de imágenes

### **1.** Generar una imagen basada en **`httpd`** \(apache\) mediante la definición de un Dockerfile. Tendrá que modificar el mensaje **It works!** por el mensaje **¡Hola Mundo!**

> Puede leer la documentación de la [imagen](https://hub.docker.com/_/httpd) para poder cambiar el contenido del `DocumentRoot`. El que ya viene en la configuración es **`/usr/local/apache2/htdocs`**.

* La imagen debe llamarse **mikroways/hola‑mundo‑httpd**
* Verificar que la imagen se creó correctamente listando las imágenes guardadas en su host.
* Verifique el funcionamiento instanciando la imagen y accediendo al servicio usando curl o un navegador.

Primero creamos una carpeta con un Dockerfile que contenga simplemente:

```text
FROM httpd:2.4
```

Luego armamos la imagen con el comando

```bash
docker build -t mikroways/hola-mundo-httpd .
```

Para verificar:

```bash
$ docker image ls
REPOSITORY                   TAG       IMAGE ID       CREATED             SIZE
mikroways/helloworld         1.0.0     3ffa500e0dad   About an hour ago   124MB
nginx                        latest    62d49f9bab67   2 weeks ago         133MB
httpd                        2.4       0b932df43057   2 weeks ago         138MB
mikroways/hola-mundo-httpd   latest    0b932df43057   2 weeks ago         138MB
ubuntu                       latest    26b77e58432b   3 weeks ago         72.9MB
hello-world                  latest    d1165f221234   7 weeks ago         13.3kB
```

Como vemos ya había una imagen de la actividad anterior con el mismo nombre, entonces se actualizó. Luego podemos ver el archivo que muestra apache por defecto: `$ docker run --rm httpd:2.4 cat /usr/local/apache2/htdocs/index.html > index.html`

Esto nos creará el fichero `index.html` en la maquina host, que podremos modificar cambiando

```diff
-<html><body><h1>It works!</h1></body></html>
+<html><body><h1>¡Hola Mundo!</h1></body></html>
```

Luego modificamos el Dockerfile:

```text
FROM httpd:2.4
COPY ./index.html /usr/local/apache2/htdocs/index.html
```

Volvemos a armar la imagen usando el comando antes mencionado. La corremos con: `docker run -dit --name apache -p 8080:80 mikroways/hola-mundo-httpd`

El resultado:

```bash
$ curl localhost:8080
<html><body><h1>¡Hola Mundo!</h1></body></html>
```

### **2.** Push de imagen: cree una imagen a su gusto y luego realice un push en docker hub y gitlab. La idea es disponibilizar la nueva imagen públicamente indicando el repositorio y comandos para que podamos descargarlas

* Si no tiene una cuenta en Docker Hub, registrarse. Una vez creada la cuenta, iniciar sesión y crear un nuevo repositorio en el cual subirá su imagen docker. Para ello deberá iniciar sesión desde la cli de docker usando: `docker login`.
* Si no tienen una cuenta en GitLab, [registrarse](https://gitlab.com). Una vez creada la cuenta, iniciar sesión y crear un nuevo repositorio en el cual subirá su imagen Docker. Al crear un nuevo repositorio, en el panel de la izquierda, se tiene un menú **Packages & Registries &gt; Container Registry**. Ingresando a este item se indican las acciones necesarias para subir la imagen a la registry de GitLab.

#### Gitlab

Para Gitlab, luego de seguir los pasos de la consigna:

```bash
docker login registry.gitlab.com
# Introducir credenciales de gitlab
```

Nos paramos en el directorio donde tenemos el Dockerfile deseado. Luego el nombre de la imagen debe ser:

`registry.gitlab.com/`**`gitlab-user`**`/`**`gitlab-repo`**

En mi caso:

```bash
docker build -t registry.gitlab.com/juanpsm/actividades-docker .
docker push registry.gitlab.com/juanpsm/actividades-docker
```

Para ver si anda, eliminamos la imagen del host y volvamos a correrla. Veremos que la baja del repositorio recién creado

egistry.gitlab.com/juanpsm/docker-test

```bash
$ docker image rm registry.gitlab.com/juanpsm/actividades-docker:latest 
$ docker run -dit --rm --name gitlab -p 8080:80 registry.gitlab.com/juanpsm/actividades-docker
Unable to find image 'registry.gitlab.com/juanpsm/actividades-docker:latest' locally
latest: Pulling from pibyte/docker-test
Digest: sha256:557e0c3a8ee17384ac8c08d9a67250ca68e7549f42579652952ca9e2d41f79fd
Status: Downloaded newer image for registry.gitlab.com/juanpsm/actividades-docker:latest
b9f2349009978d1c75c3dd0b48dab86d68c397f4d2b9de7237c20571d4065f0b

$ curl localhost:8080
<html><body><h1>¡Hola Mundo!</h1></body></html>
```

#### Dockerhub

Luego de crear la cuenta de Dockerhub, loguearse:

```bash
docker login
# Introducir credenciales de dockerhub
```

```bash
docker build -t juanpsm/docker-test:prueba .
docker push juanpsm/docker-test:prueba
```

Probar:

```bash
$ docker image rm juanpsm/docker-test:prueba
$ docker run -it --rm --name dockerhub-test -p 8080:80 juanpsm/docker-test:prueba
$ docker pull juanpsm/docker-test:prueba
Unable to find image 'juanpsm/docker-test:prueba' locally
prueba: Pulling from juanpsm/docker-test
Digest: sha256:557e0c3a8ee17384ac8c08d9a67250ca68e7549f42579652952ca9e2d41f79fd
Status: Downloaded newer image for juanpsm/docker-test:prueba
a6080f434c8c9a212dd6fb8c9a6f015462ebf6730b218b0235f8eff8fc2e42cc

$ curl localhost:8080
<html><body><h1>¡Hola Mundo!</h1></body></html>
```

### **3.** Crear una imagen en donde al instanciar el contenedor debe imprimir **“HolaMundo”**. Para esto deberá utilizar un ENTRYPOINT en el Dockerfile y **CMD** para parametrizar el mensaje que por defecto será **Hola mundo**

Probemos la diferencia entre CMD y ENTRYPOINT

`Dockerfile.cmd`:

```text
FROM alpine
CMD ["echo", "hello cmd"]
```

```bash
$ docker build -t juanpsm/docker-test:cmd -f Dockerfile.cmd .
$ docker run --rm juanpsm/docker-test:entry-cmd gg
hello cmd

$ docker run --rm juanpsm/docker-test:cmd "prueba"
docker: Error response from daemon: OCI runtime create failed: container_linux.go:380: starting container process caused: exec: "prueba": executable file not found in $PATH: unknown.

$ docker run --rm juanpsm/docker-test:cmd echo "prueba"
prueba

$ docker run --rm juanpsm/docker-test:cmd ls
bin
dev
etc
...
```

Funciona pero no puedo pasarle el parámetro que quiero que corra el `echo` directamente. Hay que volver a ponerle el comando y lo pisa, pero lo mismo sucedería con cualquier otro comando como `ls`

Así si quiero que siempre si o si corra el `echo` lo tengo que poner en el `ENTRYPOINT`

`Dockerfile.entry`:

```text
FROM alpine
ENTRYPOINT ["echo", "hello entry"]
```

```bash
$ docker build -t juanpsm/docker-test:entry -f Dockerfile.entry .
$ docker run --rm juanpsm/docker-test:entry
hello entry

$ docker run --rm juanpsm/docker-test:entry prueba
hello entry prueba

$ docker run --rm juanpsm/docker-test:entry echo prueba
hello entry echo prueba

$ docker run --rm --entrypoint="" juanpsm/docker-test:entry echo prueba
prueba
```

Ahora toma los parametros, pero no los reemplaza, los anexa luego del declarado en el `ENTRYPOINT`. Para pisarlo hay que indicar `--entrypoint=""`.

`Dockerfile.entry.cmd`:

```text
FROM alpine
ENTRYPOINT ["echo"]
CMD ["Hola mundo"]
```

```bash
$ docker build -t juanpsm/docker-test:entry-cmd -f Dockerfile.entry.cmd .
$ docker run --rm juanpsm/docker-test:entry-cmd
Hola mundo

$ docker run --rm juanpsm/docker-test:entry-cmd prueba
prueba

$ docker run --rm juanpsm/docker-test:entry-cmd echo prueba
echo prueba
```

Combinando ambos comandos, `CMD` contiene los parámetros que se le pasan al `ENTRYPOINT`. Los primeros son mas sencillos de reemplazar. El contenedor siempre ejecuta `echo` con lo que le pase en el `run` \(a menos que `--entrypoint=""`\) y si no recibe ningún parametro escribe por defecto  `Hola mundo`

### **4.** Cree un Dockerfile para generar una imagen llamada **mikroways/file‑creator**

* Utilice como base la imagen `alpine:latest`.
* Investigue el comando `WORKDIR` dentro de un Dockerfile.
* Dentro del Dockerfile cambie el WORKDIR a `/tmp`.
* Utilizando un ENTRYPOINT Y CMD, al invocar el contenedor debe crear un archivo de 10M o del espacio recibido como argumento.

  > El comando `dd` copia archivos. El siguiente ejemplo muestra como crear un archivo de 1MB lleno de ceros.

```bash
dd if=/dev/zero bs=1M count=1 of=created-file
```

Ejemplo de uso de la imagen mikroways/file‑creator:

```bash
docker run mikroways/file-creator # crea un archivo de 10 MB en /tmp
docker run mikroways/file-creator 100M # creará un archivo de 100 MB en /tmp
```

Puede verificar el tamaño del contenedor resultante usando:

```bash
docker ps -asn 2
```

Indique qué interpreta en la salida del tamaño del contenedor lanzado.

#### Resolución 4

WORKDIR /path/to/workdir

La instrucción WORKDIR setea el directorio de trabajo para los subsiguientes RUN,CMD,ENTRYPOINT, COPY y ADD.
Si la ruta no existe se crea. Se puede declarar varias veces y en cada una la ruta es relativa a la anterior.
También puede resolver variables de entorno, si estas fueron creadas previamente en un ENV.

`WORKDIR /path/to/workdir`

El dockerfile con lo requerido queda:

```text
FROM alpine:latest
WORKDIR /tmp
ENTRYPOINT dd if=/dev/zero count=1 of=created-file bs=
CMD ["10M"]
```

Contruimos imagen:

```bash
docker build -t mikroways/file-creator .
```

Corremos:

```bash
$ docker run mikroways/file-creator
dd: invalid number ''
```

```bash
$ docker inspect $(docker ps -lq) --format '{{ .Path }} {{ join .Args " " }}'
/bin/sh -c dd if=/dev/zero count=1 of=created-file bs= 10M
```

El problema es que no se "unen" los parámetros. No encontré una manera de unirlos

Entonces, cambiamos el ENTRYPOINT
a modo _excec_ para lo que hay que indicar ahora la ruta completa a `dd`
Luego el default `bs=10M` lo ponemos en CMD para que sea fácil reemplazarlo.

```text
FROM alpine:latest
WORKDIR /tmp
ENTRYPOINT ["/bin/dd", "if=/dev/zero", "count=1", "of=created-file"]
CMD ["bs=10M"]
```

```bash
$ docker build -t mikroways/file-creator .
$ docker run mikroways/file-creator
1+0 records in
1+0 records out

$ docker run mikroways/file-creator bs=100M
1+0 records in
1+0 records out

$ docker ps -asn 2 --format "{{ .Size }}"
105MB (virtual 110MB)
10.5MB (virtual 16.1MB)
```

> Nota: no encontré la manera de poder correr pasando como argumento `100M` así solo, si no que tiene que
> empezar con **bs=**, de otra forma `dd` no lo reconoce. No encontré como "armar" el parámetro
> por ejemplo `bs=$1` para tomar los argumentos del `docker run`

### **5.** Modifique la imagen anterior para que se fuerce a correr con un usuario diferente a ROOT

Dockerfile:

```text
FROM alpine:latest
ARG USER=default
ENV HOME /home/$USER
RUN adduser -D $USER
WORKDIR $HOME/tmp
RUN chown -R $USER:$USER $HOME
USER $USER
RUN whoami && ls -l $HOME
ENTRYPOINT ["/bin/dd", "if=/dev/zero", "count=1", "of=created-file"]
CMD ["bs=10M"]
```

La línea `RUN whoami && ls -l $HOME` es símplemente para checkear que el usuario se haya
creado correctamente y que tenga los permisos de escritura necesarios

Para no cambiar los permisos de la carpeta `/tmp` se crea otra carpeta en el home del usuario,
donde se trabajará.

### **6.** Utilizando el concepto de multistage builds, genere una imagen de docker que contenga el sitio web de mikroways. Los fuentes del sitio, están disponibles en [un repositorio en github](https://github.com/Mikroways/mikroways.net/). Para poder construir el sitio, es necesario partir de una imagen de **ruby versión 2.7**, clonar el repositorio usando git, instalar las dependencias y luego generar el sitio estático usando [jekyll](https://jekyllrb.com/). A continuación mostramos la secuencia de comandos para construir el sitio y obtener en la carpeta `_site/` el sitio estático. Ese contenido deberá incluirlo en un web server de contenidos estáticos como ser **nginx**

```bash
git clone https://github.com/Mikroways/mikroways.net.git /tmp/site
cd /tmp/site
bundle install
jekyll build
```

#### Resoluc. 6

```text
FROM ruby:2.7 as webpage
RUN git clone https://github.com/Mikroways/mikroways.net.git /tmp/webpage
WORKDIR /tmp/webpage
RUN bundle
RUN bundle exec jekyll build

FROM nginx
COPY --from=webpage /tmp/webpage/_site /usr/share/nginx/html
```

```bash
docker build -t mikroways/webpage .
docker run --rm -p 8090:80 mikroways/webpage
```

### 7. Usando el mismo proceso antes mencionado, es posible generar un sitio con su currículum vitae. Si ha tomado con Mikroways el curso de _Workflow Contínuo de Desarrollo, Testing y Producción_, seguramente haya desarrollado utilizando despliegue continuo su CV. En caso que no lo haya realizado, puede tomar como referencia el repositorio [https://github.com/chrodriguez/chrodriguez.github.io](https://github.com/chrodriguez/chrodriguez.github.io)

> Es importante destacar que con el comando `npm run resume exportindex.html` es el _index.html_ resultante del comando anterior el único archivo estático que debe utilizarse en un web server para servir este contenido.

#### Resoluc. 7

```text
FROM node:16.5 as resume
RUN git clone https://gitlab.com/juanpsm/juanpsm.gitlab.io.git /tmp/resume
WORKDIR /tmp/resume
RUN npm i -q \
&& npm run resume export -- --theme elegant index.html

FROM nginx
COPY --from=resume /tmp/resume/index.html /usr/share/nginx/html
```

```bash
docker build -t juanpsm/resume .
docker run --rm -p 8090:80 juanpsm/resume
```

### 8. Analice cómo funciona `.dockerignore`. Analice el contexto de un repositorio git que en su raíz tenga un Dockerfile. ¿Qué sucede con el directorio `.git` si se encuentra dentro del contexto del `docker build`?

El `.dockerignore` es una archivo que sirve para determinar que archivos deben excluirse del contexto antes que la CLI se lo envie al proceso docker.

En el caso planteado, el directorio `.git` se pasa al contexto del `build` y eso puede causar que las imágenes sean de mayor tamaño, que luego repercute en el tiempo que de construccion, push y pull de las imagenes y el tamaño del contenedor en tiempo de ejecución.

Se puede ver en:

`docker build -t juanpsm/test-git .`

`Sending build context to Docker daemon`  **62.98kB**

...

### **9.** Cree un directorio en su filesystem `/tmp/docker-image-size` e ingrese al mismo. Luego cree dos archivos de 1MB cada uno usando

```bash
dd if=/dev/zero bs=1M count=1 of=file-1
dd if=/dev/zero bs=1M count=1 of=file-2
```

### En ese mismo directorio cree un `Dockerfile` con el siguiente contenido

```text
FROM busybox
ADD . /app
RUN chmod 400 /app/*
```

### Construya una imagen llamada `mikroways/image‑size` con el dockerfile mencionado y analice por qué la imagen base se incrementa en 4MB en vez de 2MB. ¿Podría proponer una solución para incrementar únicamente 2MB?

#### Resoluc. 9

```bash
$ mkdir /tmp/docker-image-size && cd /tmp/docker-image-size
$ nano Dockerfile
$ docker image ls mikroways/image-size
REPOSITORY             TAG       IMAGE ID       CREATED         SIZE
mikroways/image-size   latest    a392c5b0708d   6 seconds ago   1.24MB

$ dd if=/dev/zero bs=1M count=1 of=file-1 && dd if=/dev/zero bs=1M count=1 of=file-2
$ docker image ls mikroways/image-size
REPOSITORY             TAG       IMAGE ID       CREATED         SIZE
mikroways/image-size   latest    c9edeff888d4   2 minutes ago   5.43MB

$ docker history mikroways/image-size
IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
cb82b802ac69   39 seconds ago   /bin/sh -c chmod 400 /app/*                     2.1MB     
da2dc0b50d6d   49 seconds ago   /bin/sh -c #(nop) ADD dir:8c7507505a8b764eff…   2.1MB     
69593048aa3a   6 weeks ago      /bin/sh -c #(nop)  CMD ["sh"]                   0B        
<missing>      6 weeks ago      /bin/sh -c #(nop) ADD file:ab1db978794665f04…   1.24MB 
```

Lo que sucede es que cada capa de la imágen (el ADD y el RUN).
Como se explica en [ADD or COPY](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#add-or-copy),
es mejor usar COPY para este caso. Igualmente eso no lo soluciona, el problema es el RUN. Ahi es donde se repite la indormacion de la capa, se modifican los pormisos del los archivos y se vuelven a copiar en otra capa.

Para resolver esto se encuentra en [desarrollo](https://github.com/moby/moby/pull/36123) agregar un flag `--chmod` al comando
COPY. Para utilizarlo hay que [Habilitar las Buildkit builds](https://docs.docker.com/develop/develop-images/build_enhancements/#to-enable-buildkit-builds).

```bash
FROM busybox
COPY --chmod=400 file-1 /app/
COPY --chmod=400 file-2 /app/
```

```bash
$ DOCKER_BUILDKIT=1 docker build -t mikroways/image-size .
[+] Building 3.5s (8/8) FINISHED
...

$ docker image ls mikroways/image-size
REPOSITORY             TAG       IMAGE ID       CREATED          SIZE
mikroways/image-size   latest    648ec77c3289   38 seconds ago   3.33MB

$ docker history mikroways/image-size
IMAGE          CREATED          CREATED BY                                      SIZE      COMMENT
648ec77c3289   44 seconds ago   COPY file-2 /app/ # buildkit                    1.05MB    buildkit.dockerfile.v0
<missing>      46 seconds ago   COPY file-1 /app/ # buildkit                    1.05MB    buildkit.dockerfile.v0
<missing>      6 weeks ago      /bin/sh -c #(nop)  CMD ["sh"]                   0B        
<missing>      6 weeks ago      /bin/sh -c #(nop) ADD file:ab1db978794665f04…   1.24MB    
```

### 10. Es una buena práctica escribir entrypoints como shell scripts bash o bourn shell. Esos scripts en sus primeras líneas suelen utilizar la sentencia `set -eo pipefail`. Analice por qué es tan importante utilizar esas opciones

> Para entender como funciona puede probar instanciar un contenedor busybox y probar en él qué sucede si setea por un lado `set -e` y luego la instrucción completa. Para cada caso, probar luego de setear las opciones comandos como: `ls /not-exist` y `ls /not-exist | echo No problem`

`set -o pipefail` significa que en la secuencia de comandos de un pipe, con que uno de los comandos retorne un valor distinto de cero, todo el pipe devuelve distinto de cero, incluso si el último comando devuelve 0.

`set -e` significa que una vez que el valor de retorno de un comando en el script es distinto de cero, el script se cerrará inmediatamente y los comandos subsiguientes no se ejecutarán.

```bash
echo -e 'FROM busybox\nCOPY script.sh /tmp/\nRUN chmod +x /tmp/script.sh\nENTRYPOINT ["/bin/sh", "-c", "./tmp/script.sh"]' > Dockerfile
echo -e "set -e\nls /not-exist" > script.sh 
docker build -t set-e .
docker run set-e
# ls: /not-exist: No such file or directory
docker ps -al --format '{{ .Status }}'
# Exited (1) 21 seconds ago


echo -e "set -e\nls /not-exist | echo No problem" > script.sh 
docker build -t set-e .
docker run set-e
# No problem
# ls: /not-exist: No such file or directory
docker ps -al --format '{{ .Status }}'
# Exited (0) 6 seconds ago


echo -e "set -eo pipefail\nls /not-exist" > script.sh 
docker build -t set-e .
docker run set-e
# ls: /not-exist: No such file or directory
docker ps -al --format '{{ .Status }}'
# Exited (1) 9 seconds ago


echo -e "set -eo pipefail\nls /not-exist | echo No problem" > script.sh 
docker build -t set-e .
docker run set-e
# No problem
# ls: /not-exist: No such file or directory
docker ps -al --format '{{ .Status }}'
# Exited (1) 4 seconds ago
```

## Entregables

### Los ejercicios a entregar son 1, 2, 3, 4, 5, 6, 9. Las entregas deben responder a las preguntas de cada ejercicio, los comandos o el Dockerfile usados para completarlos
