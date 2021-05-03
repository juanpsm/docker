# Actividad 2

## Construcción de imágenes

### **1.** Generar una imagen basada en **`httpd`** (apache) mediante la definición de un Dockerfile. Tendrá que modificar el mensaje **It works!** por el mensaje **¡Hola Mundo!**
> Puede leer la documentación de la [imagen](https://hub.docker.com/_/httpd) para poder cambiar el contenido del `DocumentRoot`. El que ya viene en la configuración es **`/usr/local/apache2/htdocs`**.

- La imagen debe llamarse **mikroways/hola‑mundo‑httpd**
- Verificar que la imagen se creó correctamente listando las imágenes guardadas en su host.
- Verifique el funcionamiento instanciando la imagen y accediendo al servicio usando curl o un navegador.

Primero creamos una carpeta con un Dockerfile que contenga simplemente:
```docker
FROM httpd:2.4
```
Luego armamos la imagen con el comando `docker build -t mikroways/hola-mundo-httpd .`
Para verificar:
```
$ docker image ls
REPOSITORY                   TAG       IMAGE ID       CREATED             SIZE
mikroways/helloworld         1.0.0     3ffa500e0dad   About an hour ago   124MB
nginx                        latest    62d49f9bab67   2 weeks ago         133MB
httpd                        2.4       0b932df43057   2 weeks ago         138MB
mikroways/hola-mundo-httpd   latest    0b932df43057   2 weeks ago         138MB
ubuntu                       latest    26b77e58432b   3 weeks ago         72.9MB
hello-world                  latest    d1165f221234   7 weeks ago         13.3kB
```
Como vemos ya había una imagen de la actividad anterior con el mismo nombre, entonces se actualizó.
Luego podemos ver el archivo que muestra por defecto y poder modificarlo desde el host:
`$ docker run --rm httpd:2.4 cat /usr/local/apache2/htdocs/index.html > index.html`
Esto nos creará el fichero `index.html` que podremos modificar.
Luego modificamos el Dockerfile:
```docker
FROM httpd:2.4
COPY ./index.html /usr/local/apache2/htdocs/index.html
```
Volvemos a armar la imagen el comandos antes mencionado. La corremos con:
`docker run -dit --name apache -p 8080:80 mikroways/hola-mundo-httpd`
El resultado:
```bash
$ curl localhost:8080
<html><body><h1>¡Hola Mundo!</h1></body></html>
```

### **2.** Push de imagen: cree una imagen a su gusto y luego realice un push en docker hub y gitlab. La idea es disponibilizar la nueva imagen públicamente indicando el repositorio y comandos para que podamos descargarlas.

- Si no tiene una cuenta en Docker Hub, registrarse. Una vez creada la cuenta, iniciar sesión y crear un nuevo repositorio en el cual subirá su imagen docker. Para ello deberá iniciar sesión desde la cli de docker usando: `docker login`.

- Si no tienen una cuenta en GitLab, [registrarse](https://gitlab.com). Una vez creada la cuenta, iniciar sesión y crear un nuevo repositorio en el cual subirá su imagen Docker. Al crear un nuevo repositorio, en el panel de la izquierda, se tiene un menú **Packages & Registries > Container Registry**. Ingresando a este item se indican las acciones necesarias para subir la imagen a la registry de GitLab.

Para Gitlab, luego de seguir los pasos de la consigna:
```
docker login registry.gitlab.com

# Nos paramos en el directorio donde tenemos el Dockerfile deseado
docker build -t registry.gitlab.com/pibyte/docker-test .
docker push registry.gitlab.com/pibyte/docker-test

# Veamos si anda, eliminamos la imagen del host y volvamos a correrla. Veremos que la baja del repositorio recien creado
$ docker image rm registry.gitlab.com/pibyte/docker-test:latest 
$ docker run -dit --rm --name gitlab -p 8080:80 registry.gitlab.com/pibyte/docker-test
Unable to find image 'registry.gitlab.com/pibyte/docker-test:latest' locally
latest: Pulling from pibyte/docker-test
Digest: sha256:557e0c3a8ee17384ac8c08d9a67250ca68e7549f42579652952ca9e2d41f79fd
Status: Downloaded newer image for registry.gitlab.com/pibyte/docker-test:latest
3f84cb204329e5c3e11c2d00c774cf6aaa375eadf647a8bebb29469133e699ce

$ curl localhost:8080
<html><body><h1>¡Hola Mundo!</h1></body></html>
```

Dockerhub:
```
$ docker build -t pibytes/docker-test:prueba .
$ docker push pibytes/docker-test:prueba
```
Se puede bajar con 
```
$ docker pull pibytes/docker-test:prueba
```
### **3.** Crear una imagen en donde al instanciar el contenedor debe imprimir **“HolaMundo”**. Para esto deberá utilizar un ENTRYPOINT en el Dockerfile y **CMD** para parametrizar el mensaje que por defecto será **Hola mundo**.

### **4.** Cree un Dockerfile para generar una imagen llamada **mikroways/file‑creator**.

- Utilice como base la imagen `alpine:latest`.
- Investigue el comando `WORKDIR` dentro de un Dockerfile.
- Dentro del Dockerfile cambie el WORKDIR a `/tmp`.
- Utilizando un ENTRYPOINT Y CMD, al invocar el contenedor debe crear un archivo de 10M o del espacio recibido como argumento.
> El comando `dd` copia archivos. El siguiente ejemplo muestra como crear un archivo de 1MB lleno de ceros.

```sh
dd if=/dev/zero bs=1M count=1 of=created-file
```
Ejemplo de uso de la imagen mikroways/file‑creator:
```sh
docker run mikroways/file-creator # crea un archivo de 10 MB en /tmp
docker run mikroways/file-creator 100M # creará un archivo de 100 MB en /tmp
```
Puede verificar el tamaño del contenedor resultante usando:
```sh
docker ps - asn 2
```
Indique qué interpreta en la salida del tamaño del contenedor lanzado.

### **5.** Modifique la imagen anterior para que se fuerce a correr con un usuario diferente a ROOT.

### **6.** Utilizando el concepto de multistage builds, genere una imagen de docker que contenga el sitio web de mikroways. Los fuentes del sitio, están disponibles en [un repositorio en github](https://github.com/Mikroways/mikroways.net/). Para poder construir el sitio, es necesario partir de una imagen de **ruby versión 2.7**, clonar el repositorio usando git, instalar las dependencias y luego generar el sitio estático usando [jekyll](https://jekyllrb.com/). A continuación mostramos la secuencia de comandos para construir el sitio y obtener en la carpeta `_site/` el sitio estático. Ese contenido deberá incluirlo en un web server de contenidos estáticos como ser **nginx**.

```sh
git clone https://github.com/Mikroways/mikroways.net.git /tmp/site
cd /tmp/site
bundle install
jekyll build
```
### 7. Usando el mismo proceso antes mencionado, es posible generar un sitio con su currículum vitae. Si ha tomado con Mikroways el curso de *Workflow Contínuo de Desarrollo, Testing y Producción*, seguramente haya desarrollado utilizando despliegue continuo su CV. En caso que no lo haya realizado, puede tomar como referencia el repositorio [https://github.com/chrodriguez/chrodriguez.github.io](https://github.com/chrodriguez/chrodriguez.github.io).

> Es importante destacar que con el comando `npm run resume exportindex.html` es el *index.html* resultante del comando anterior el único archivo estático que debe utilizarse en un web server para servir este contenido.

### 8. Analice cómo funciona `.dockerignore`. Analice el contexto de un repositorio git que en su raíz tenga un Dockerfile. ¿Qué sucede con el directorio `.git` si se encuentra dentro del contexto del `docker build`?

### **9.** Cree un directorio en su filesystem `/tmp/docker-image-size` e ingrese al mismo. Luego cree dos archivos de 1MB cada uno usando:
```sh
dd if=/dev/zero bs=1M count=1 of=file-
dd if=/dev/zero bs=1M count=1 of=file-
```
### En ese mismo directorio cree un `Dockerfile` con el siguiente contenido:
```docker
1 FROM busybox
2 ADD . /app
3 RUN chmod 400 /app/*
```
### Construya una imagen llamada mikroways/image‑size con el dockerfile mencionado y analice por qué la imagen base se incrementa en 4MB en vez de 2MB. ¿Podría proponer una solución para incrementar únicamente 2MB?

### 10. Es una buena práctica escribir entrypoints como shell scripts bash o bourn shell. Esos scripts en sus primeras líneas suelen utilizar la sentencia `set -eo pipefail`. Analice por qué es tan importante utilizar esas opciones.

>Para entender como funciona puede probar instanciar un contenedor busybox y probar en él qué sucede si setea por un lado `set -e` y luego la instrucción completa. Para cada caso, probar luego de setear las opciones comandos como: `ls /not-exist` y `ls /not-exist | echo No problem`

## Entregables

### Los ejercicios a entregar son 1, 2, 3, 4, 5, 6, 9. Las entregas deben responder a las preguntas de cada ejercicio, los comandos o el Dockerfile usados para completarlos.


