# Actividad 4

## Redes

### **1.** Cree dos contenedores basados en alpine de forma interactiva llamados **uno‑default** y **dos‑default** respectivamente. Estos contenedores deben utilizar la red por defecto llamada **bridge** (para lo cual no debe especificar ninguna red usando `--network`). Luego verifique conectividad entre ellos usando el comando **ping**

> Utilice con el comando **`docker run`** usado para lanzar los contenedores **uno‑default** y **dos‑default** la opción **`-e VAR=uno`** y **`-e VAR=dos`** respectivamente.

* Verifique la ip de cada contenedor usando **`ip add ls`** y ejecute el comando **ping** y la IP del contenedor remoto.

* Repita el procedimiento anterior utilizando el nombre del contenedor remoto. ¿Funciona?

uno‑default:

```bash
$ docker run -it --name uno-default -e VAR=uno alpine
/ # ip add ls
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
8: eth0@if9: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue state UP 
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever
```

dos‑default:

```bash
docker run -it --name dos-default -e VAR=dos alpine 
/ # ip add ls
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
10: eth0@if11: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue state UP 
    link/ether 02:42:ac:11:00:03 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.3/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever
```

uno‑default:

```bash
/ # ping 172.17.0.3
PING 172.17.0.3 (172.17.0.3): 56 data bytes
64 bytes from 172.17.0.3: seq=0 ttl=64 time=0.265 ms
64 bytes from 172.17.0.3: seq=1 ttl=64 time=0.165 ms
64 bytes from 172.17.0.3: seq=2 ttl=64 time=0.179 ms
64 bytes from 172.17.0.3: seq=3 ttl=64 time=0.179 ms
64 bytes from 172.17.0.3: seq=4 ttl=64 time=0.182 ms
^C
--- 172.17.0.3 ping statistics ---
5 packets transmitted, 5 packets received, 0% packet loss
round-trip min/avg/max = 0.165/0.194/0.265 ms
```

dos‑default:

```bash
/ # ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2): 56 data bytes
64 bytes from 172.17.0.2: seq=0 ttl=64 time=0.162 ms
64 bytes from 172.17.0.2: seq=1 ttl=64 time=0.142 ms
64 bytes from 172.17.0.2: seq=2 ttl=64 time=0.179 ms
64 bytes from 172.17.0.2: seq=3 ttl=64 time=0.178 ms
64 bytes from 172.17.0.2: seq=4 ttl=64 time=0.178 ms
^C
--- 172.17.0.2 ping statistics ---
5 packets transmitted, 5 packets received, 0% packet loss
round-trip min/avg/max = 0.142/0.167/0.179 ms
```

### **2.** La opción `docker run --link` no es aconsejada. Sin embargo, es la forma en que un contenedor puede conectar con otro por nombre cuando se utiliza la red por defecto. Repita el ejercicio 1, esta vez utilizando la opción **`--link`** para lanzar el contenedor **dos‑default**

> Utilice con el comando **`docker run`** usado para lanzar los contenedores **uno‑default** y **dos‑default** la opción **`-e VAR=uno`** y **`-e VAR=dos`** respectivamente.

* Verifique el comando ping contra el contenedor remoto utilizando ahora el nombre del contenedor. ¿Funciona en todos los contenedores o sólo en el que incluye --link?

    uno-default:

    ```bash
    / # ping dos-default
    ping: bad address 'dos-default'
    ```

    dos-default:

    ```bash
    docker run -it --name dos-default -e VAR=dos --link uno-default alpine 
    / # ping uno-default
    PING uno-default (172.17.0.2): 56 data bytes
    64 bytes from 172.17.0.2: seq=0 ttl=64 time=0.232 ms
    64 bytes from 172.17.0.2: seq=1 ttl=64 time=0.138 ms
    64 bytes from 172.17.0.2: seq=2 ttl=64 time=0.173 ms
    64 bytes from 172.17.0.2: seq=3 ttl=64 time=0.147 ms
    64 bytes from 172.17.0.2: seq=4 ttl=64 time=0.176 ms
    ^C
    --- uno-default ping statistics ---
    5 packets transmitted, 5 packets received, 0% packet loss
    round-trip min/avg/max = 0.138/0.173/0.232 ms
    ```

* Verifique además las variables de ambiente en **uno‑default** y **dos‑default** usando el comando **`env`**. ¿Qué observa?

    uno-default:

    ```bash
    / # env
    HOSTNAME=0b75d4c7386a
    SHLVL=1
    HOME=/root
    VAR=uno
    TERM=xterm
    PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
    PWD=/
    ```

    dos-default:

    ```bash
    / # env
    HOSTNAME=dd6bb541f555
    SHLVL=1
    HOME=/root
    VAR=dos
    TERM=xterm
    PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
    UNO_DEFAULT_ENV_VAR=uno
    UNO_DEFAULT_NAME=/dos-default/uno-default
    PWD=/
    ```

    El contenedor **dos-default** tiene la variable de ambiente `VAR` de **uno-default** bajo el nombre `UNO_DEFAULT_ENV_VAR`.

### **3.** Cree una red de tipo bridge definida por el usuario y corra dos contenedores basados en alpine de forma interactiva llamados **uno‑user** y **dos‑user**

> Utilice con el comando **`docker run`** usado para lanzar los contenedores **uno‑default** y **dos‑default** la opción **`-e VAR=uno`** y **`-e VAR=dos`** respectivamente.

```bash
$ docker rm uno-default dos-default 
$ docker network create my-net
5bcd437cbd5ddb6f6dacf7b35e67dd0945850cb699d921da495376a4fc7c3b50
```

* Verifique el comando ping contra el contenedor remoto utilizando ahora el nombre del contenedor. ¿Funciona en todos los contenedores?

    uno-default:

    ```bash
    docker run -it --name uno-default -e VAR=uno --network my-net alpine
    / # ping dos-default
    PING dos-default (172.20.0.3): 56 data bytes
    64 bytes from 172.20.0.3: seq=0 ttl=64 time=0.564 ms
    64 bytes from 172.20.0.3: seq=1 ttl=64 time=0.181 ms
    ^C
    --- dos-default ping statistics ---
    2 packets transmitted, 2 packets received, 0% packet loss
    round-trip min/avg/max = 0.181/0.372/0.564 ms
    ```

    dos-default:

    ```bash
    docker run -it --name dos-default -e VAR=dos --link uno-default --network my-net alpine 
    / # ping uno-default
    PING uno-default (172.20.0.2): 56 data bytes
    64 bytes from 172.20.0.2: seq=0 ttl=64 time=0.219 ms
    64 bytes from 172.20.0.2: seq=1 ttl=64 time=0.147 ms
    ^C
    --- uno-default ping statistics ---
    2 packets transmitted, 2 packets received, 0% packet loss
    round-trip min/avg/max = 0.147/0.183/0.219 ms
    ```

* Verifique además las variables de ambiente en **uno‑user** y **dos‑user** usando el comando **`env`**. ¿Qué observa?

    uno-default:

    ```bash
    / # env
    HOSTNAME=19858ce067a3
    SHLVL=1
    HOME=/root
    VAR=uno
    TERM=xterm
    PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
    PWD=/
    ```

    dos-default:

    ```bash
    / # env
    HOSTNAME=47bcd42634ed
    SHLVL=1
    HOME=/root
    VAR=dos
    TERM=xterm
    PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
    PWD=/
    ```

    No se encuentran las variables del otro contenedor.

* Desconecte uno de los contenedores mientras desde el contenedor remoto ejecuta el comando ping. Luego vuelva a conectarlo
    > Para desconectar y conectar contenedores investigue el uso de los comandos **`docker network disconnect`** y **`docker network connect`**.

    uno-default:

    ```bash
    / # ping dos-default
    PING dos-default (172.20.0.3): 56 data bytes
    64 bytes from 172.20.0.3: seq=0 ttl=64 time=0.141 ms
    64 bytes from 172.20.0.3: seq=1 ttl=64 time=0.144 ms
    64 bytes from 172.20.0.3: seq=2 ttl=64 time=0.180 ms
    64 bytes from 172.20.0.3: seq=3 ttl=64 time=0.222 ms
    64 bytes from 172.20.0.3: seq=16 ttl=64 time=0.268 ms
    64 bytes from 172.20.0.3: seq=17 ttl=64 time=0.338 ms
    64 bytes from 172.20.0.3: seq=18 ttl=64 time=0.180 ms
    64 bytes from 172.20.0.3: seq=19 ttl=64 time=0.149 ms
    ^C
    --- dos-default ping statistics ---
    20 packets transmitted, 8 packets received, 60% packet loss
    round-trip min/avg/max = 0.141/0.202/0.338 ms
    ```

    Desde otra consola hago el `docker network disconnect my-net dos-default` y la terminal que esta haciendo el ping se queda clavada. Cuando reconecto el contenedor **dos-default** con `docker network connect my-net dos-default` vuelve a recibir los paquetes, pero los que envió en el tiempo desconectado se perdieron. Notar el "salto" en el numero `seq`.

### **4.** Indique el comando necesario para correr un contenedor que no tenga conexión de red

```bash
$ docker run -it --network none alpine 
/ # ip add ls
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
/ # 
```

También funcionan las opciones `--network=none`, `--network='none'` y `--network="none"`.

### **5.** Cree una red definida por el usuario con una subred que defina usted. Luego repita el ejercicio 3 verificando que cada contenedor toma una IP del segmento definido por la red creada

```bash
$ docker network create --subnet 200.22.22.0/16 my-subnet
89833286de6d2ea292c06798a96ac641838f5f5d3e82aa5ed9fa063d3f237144
```

```bash
$ docker run -it --name uno-default -e VAR=uno --network my-subnet alpine
/ # ip add ls
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
34: eth0@if35: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue state UP 
    link/ether 02:42:c8:16:00:02 brd ff:ff:ff:ff:ff:ff
    inet 200.22.0.2/16 brd 200.22.255.255 scope global eth0
       valid_lft forever preferred_lft forever
/ # ping dos-default
PING dos-default (200.22.0.3): 56 data bytes
64 bytes from 200.22.0.3: seq=0 ttl=64 time=0.178 ms
64 bytes from 200.22.0.3: seq=1 ttl=64 time=0.143 ms
^C
--- dos-default ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max = 0.143/0.160/0.178 ms
```

```bash
$ docker run -it --name dos-default -e VAR=dos --network my-subnet alpine 
/ # ip add ls
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
40: eth0@if41: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue state UP 
    link/ether 02:42:c8:16:00:03 brd ff:ff:ff:ff:ff:ff
    inet 200.22.0.3/16 brd 200.22.255.255 scope global eth0
       valid_lft forever preferred_lft forever
/ # ping uno-default
PING uno-default (200.22.0.2): 56 data bytes
64 bytes from 200.22.0.2: seq=0 ttl=64 time=0.218 ms
64 bytes from 200.22.0.2: seq=1 ttl=64 time=0.150 ms
64 bytes from 200.22.0.2: seq=2 ttl=64 time=0.187 ms
64 bytes from 200.22.0.2: seq=3 ttl=64 time=0.186 ms
64 bytes from 200.22.0.2: seq=4 ttl=64 time=0.187 ms
^C
--- uno-default ping statistics ---
5 packets transmitted, 5 packets received, 0% packet loss
round-trip min/avg/max = 0.150/0.185/0.218 ms
```

## Límite de recursos

> **RECUERDE QUE PARA PODER LIMITAR LA MEMORIA Y FUNCIONE DEBE DESACTIVAR LA SWAP DE SU MÁQUINA:** `sudo swapoff -a`.

### **1.** Lance el contenedor a partir de la imagen **progrium/stress**

```bash
docker run --rm progrium/stress -c 1 --vm 1 --vm-bytes 200m -t 60
```

> Mientras corre el comando durante 1 minuto, verifique las estadísticas de consumo:
> **`docker stats $(docker ps -ql)`**

```bash
$ docker run --rm progrium/stress -c 1 --vm 1 --vm-bytes 200m -t 60
...
stress: dbug: [1] --> hogvm worker 1 [8] forked
stress: dbug: [1] <-- worker 7 signalled normally
stress: dbug: [1] <-- worker 8 signalled normally
stress: info: [1] successful run completed in 60s

# en otra terminal
$ docker stats $(docker ps -ql)
CONTAINER ID   NAME                 CPU %     MEM USAGE / LIMIT     MEM %     NET I/O       BLOCK I/O   PIDS
760c2826645b   flamboyant_solomon   203.14%   24.73MiB / 3.719GiB   0.65%     2.88kB / 0B   0B / 0B     3
```

**¿¿ CPU 200% ??**

El contenedor asigna toda la capacidad de CPU disponible (por cada CPU se tiene 100%).

> **Deshabilite la swap en su PC para la siguientes pruebas:** `sudo swapoff -a`

### **2.** Corra el mismo comando del ejercicio 1, pero limitando la cantidad de memoria usando la opción **`-m 128M`**

> Quite la opción `--rm` para poder inspeccionar el contenedor finalizado.

```bash
$ sudo swapoff -a
$ docker run -m 128M progrium/stress -c 1 --vm 1 --vm-bytes 200m -t 60
stress: FAIL: [1] (416) <-- worker 9 got signal 9
stress: WARN: [1] (418) now reaping child worker processes
stress: FAIL: [1] (452) failed run completed in 0s
stress: info: [1] dispatching hogs: 1 cpu, 0 io, 1 vm, 0 hdd
stress: dbug: [1] using backoff sleep of 6000us
stress: dbug: [1] setting timeout to 60s
stress: dbug: [1] --> hogcpu worker 1 [8] forked
stress: dbug: [1] --> hogvm worker 1 [9] forked
stress: dbug: [1] <-- worker 8 reaped
```

* ¿Cuál es el exit code?

    ```bash
    $ docker ps -al
    CONTAINER ID   IMAGE             COMMAND                  CREATED          STATUS                      PORTS     NAMES
    e6f46e31920c   progrium/stress   "/usr/bin/stress --v…"   39 seconds ago   Exited (1) 35 seconds ago             agitated_cohen
    ```

* Inspeccionando el contenedor, ¿podemos inferir que fue parado por OOMKiller?

    Sí. Se puede ver el flag `OOMKilled`:

    ```bash
    $ docker inspect $(docker ps -alq)
    ...
    "State": {
                "Status": "exited",
                "Running": false,
                "Paused": false,
                "Restarting": false,
                "OOMKilled": true,
                "Dead": false,
                "Pid": 0,
                "ExitCode": 1,
                "Error": "",
                "StartedAt": "2021-07-26T16:11:40.68217574Z",
                "FinishedAt": "2021-07-26T16:11:40.776408033Z"
            },
    ...
    ```

## Entregables

### Los ejercicios a entregar de la sección redes son el 1, 2, 3, 4 y 5. Para la sección de límites de recursos son el 1 y 2. Las entregas deben responder a las preguntas de cada ejercicio o los comandos usados para completarlos
