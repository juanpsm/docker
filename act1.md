# act1

Actividad 1

### Fundamentos de docker

## cli de docker

### Referencias

* Para resolver los ejercicios se recomienda seguir ladocumentación oficial de Docker.
* Adicionalmente tome como referencia los comandos básicos detallados a continuación. Serán utilizados a lo largo de la capacitación.

  * `docker run`
  * `docker ps`
  * `docker logs`
  * `docker image`
  * `docker start`
  * `docker stop`
  * `docker rm`
  * `docker history`
  * `docker tag`
  * `docker restart`
  * `docker rmi`
  * `docker commit`
  * `docker exec`
  * `docker search`

  Nota: para ver las opciones de cada comando utilizar `–help`. Ejemplo: `docker comando –help`

### Ejercicios

1. Instalar Docker:
   * Realizar la instalación en su máquina siguiendo los pasos de acuerdo a su Sistema Operativo.
2. Correr su primer aplicación con contenedores: “Hello World”

   `docker run hello‑world`

   * ¿Qué imagen se utiliza para instanciar el contenedor?
   * ¿Qué puede decir de los mensajes que se pueden ver en la consola?

3. Luego de correr el contenedor de la aplicación “Hello World” ¿Que sucede con el contenedor que se instanció? ¿Continúa existiendo? Utilize el comando **docker ps** y **docker ps ‑a**.
4. Correr un nuevo contenedor basado en la imagen “ubuntu:18.04”

   `docker run ubuntu:18.04 /bin/bash`

   * Utilizando **docker ps** ¿Qué sucedió con el estado del contenedor?
   * Investigue el parámetro ‑i ‑t del comando **docker run**
   * Instancie nuevamente un contenedor utilizando ahora-i -t\(o-it\)
     * ¿Qué otra información se puede obtener con el comando **docker ps**?
     * ¿Se le asigna alguna descripción/nombre al contenedor creado? ¿Cúal?
     * ¿Es posible asignar un nombre a un contenedor cuando es creado? ¿Con qué parámetros?
     * Cree otro contenedor con el nombre **capacitacion‑practica‑**.
     * Listar los contenedores creados hasta el momento en su máquina y verificar que se acaba de crear un contenedor con el nombre asignado.

5. ¿Con qué comando es posible eliminar un contenedor?
   * ¿Es posible eliminarlos mediante su identificador \(completo o parcial\) o con su nombre?

     `docker rm –help`
6. Luego de ejecutar un contenedor y éste finalice su ejecución, el mismo cambia su estado a detenido pero sigue existiendo.
   * ¿Qué ventajas y desventajas cree que podría causar este comportamiento?
   * Investigue el comportamiento del parámetro **–rm** de **docker run**
7. Correr un servicio Apache
   * Busque con el comando **docker search apache** el nombre de la imagen oficial.
   * Correr un contenedor con la imagen oficial de apache.
     * Listar los contenedores que se están ejecutando. ¿Qué información relevante puede observar en la columna PORTS?
     * ¿Es posible acceder por el navegador al servidor web iniciado en dicho puerto? De ser necesario, realice el mapeo de puertos adecuado para acceder al contenedor desde su propio navegador. Ayuda:  `docker run ‑p`
     * ¿Qué sucede si se cierra la consola en donde se encuentra corriendo el servidor web Apache? \(Ctrl+C\)

       Chequear el estado del contenedor con el comando docker ps y docker ps ‑a.
8. Volver a correr Apache utilizando Docker, en este oportunidad cuando cierre la consola el servidor web no debe detenerse. De esta manera cuando ejecute el comando docker ps , el estado del contenedor debe ser Up y debería continuar siendo accesible mediante el navegador.
   * Investigue el parámetro **‑d** de **docker run**

     9.Cuando se ejecuta un contenedor sin la opcióndetachedse pueden ver los logs de la aplicación desde la consola, teniendo el problema de que al cerrar la consola se detiene la ejecución del contenedor. Investigar como se podrían obtener los logs de un contenedor que se ejecuta como detached.

   * Analice cómo ver las últimas 10 lineas de logs
   * De qué forma ver los logs de hace 5 minutos hasta hace 4 minutos, es decir los logs de hace 5 minutos, durante un minuto.
9. Cree una contenedor basado en alpine Linux. En él instale apache y cree una imagen usando `commit`. El paquete de apache es apache2 y para iniciar apache se utilizahttpd -D FOREGROUNDpara que inicie en foreground
10. Inicie un contenedor de nginx. Luego utilicedocker execpara ingresar al conentedor y modificar el index.html
11. Indique la diferencia entre las políticas de reinicio disponibles para un contenedor.

### Entregables

Los ejercicios a entregar son 2, 3, 6, 7, 9, 10, 12. Las entregas deben responder a las preguntas de cada ejercicio o los comandos usados para completarlo.

