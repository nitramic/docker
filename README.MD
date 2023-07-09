# Repo con base de aprendizaje en docker

## Docker Resgitry con Delete habilitado
Instalando desde el inicio y asi no tener que modficar el registro.
La diferencia está en indicar que le seteamos la opcion de delete


docker run -d -e REGISTRY_STORAGE_DELETE_ENABLED=true -p 5000:5000 --restart=always --name registry registry:2

## Instalacion Docker
Una vez instalado habilitar el daemon para que inicie al hacer el boot

sudo systemctl enable docker.service
sudo systemctl enable containerd.service


## Habilitar el demonino de docker para escuchar fuera de localhost 
### (y asi habilitar usar el context de forma rapida) [INSEGURO!!! Solo para test rapidos]

Una vez instalado docker en la maquina remota, modificar la config del daemon, para esto 
hacer ´$sudo systemctl edit docker.service´ editar el archivo y sumar las siguiente lineas
que indicaran al daemon a escuhar peticiones de todos lados sin usar certificados
En este caso estamos exponiendo el port 2375

´sudo systemctl edit docker.service´

[Service]
ExecStart=
ExecStart=/usr/bin/dockerd -H fd:// -H tcp://0.0.0.0:2375

Salvar el archivo y reniciar servicios de Docker

´sudo systemctl daemon-reload´
´sudo systemctl restart docker.service´

Nota: Instalar net-tools para poder verificar el port
´sudo apt install net-tools´

Para ver los ports en **LISTENING** de Docker

netstat -an | grep 2375
netstat -an | grep docker

Una vez realizado esto podemos ir a nuestro Docker y establecer un context apuntando a este server
(En mi caso la ip es 192.168.1.15)

$ docker context ls
$ docker context create ubu-vbox  --default-stack-orchestrator=swarm  --docker host=tcp://192.168.1.15:2375

Con el comando ejecutado, verificamos que el nuevo context esta presente, de ser asi luego hacermos el switch.

~$ docker context ls
NAME                TYPE                DESCRIPTION                               DOCKER ENDPOINT                                 KUBERNETES ENDPOINT   ORCHESTRATOR
default *           moby                Current DOCKER_HOST based configuration   unix:///var/run/docker.sock                                           
desktop-linux       moby                Docker Desktop                            unix:///home/user/.docker/desktop/docker.sock                         
ubu-vbox            moby                                                          tcp://192.168.1.15:2375   

~$ docker context use ubu-vbox
ubu-vbox
Current context is now "ubu-vbox"

Con esto ya estamos corriendo en el server remoto.

~$ docker context ls
NAME                TYPE                DESCRIPTION                               DOCKER ENDPOINT                                 KUBERNETES ENDPOINT   ORCHESTRATOR
default             moby                Current DOCKER_HOST based configuration   unix:///var/run/docker.sock                                           
desktop-linux       moby                Docker Desktop                            unix:///home/user/.docker/desktop/docker.sock                         
ubu-vbox *          moby                                                          tcp://192.168.1.15:2375   