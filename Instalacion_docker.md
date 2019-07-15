
# Instalar docker
Instrucciones en **https://docs.docker.com/install/linux/docker-ce/ubuntu/**

## Instalar paquetes necesarios para la instalación de docker
```
sudo apt-get update
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
```

## Instalar llave para docker
`curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`

*Verify that you now have the key with the fingerprint 9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88, by searching for the last 8 characters of the fingerprint.*

`sudo apt-key fingerprint 0EBFCD88`

## add docker repository
```
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

## install docker
`sudo apt-get update`

Install the latest version of Docker CE and containerd, or go to the next step to install a specific version:

`sudo apt-get install docker-ce docker-ce-cli containerd.io`

Si aca sale un error *El paquete 'docker-ce' no tiene un candidato de instalación"*, hay que cambiar la versión del repositorio a 'bionic'.

## Para correr docker sin sudo!
```
sudo usermod -a -G docker user_name
newgrp docker 
```
* El último comando permite “refrescar” el grupo sin tener que reiniciar*


## Configuración e instalacion de aplicaciones
1. Agregar usuario con privilegios de administrador.
.* Para configurar el administrador, necesario para instalar packetes desde el *Toolshed*, es necesario editar/crear en la carpeta compartida el archivo config/galaxy.yml

## Algunos comandos útiles de docker

```
docker search TEXT #Busca en el repositorio las imagenes con TEXT
docker pull REPOSITORY[:TAG] #Para bajar una imagen:tag, donde el tag, corresponde, por ejemplo, a la version. Las tags, hay que buscarlas en Docker Hub: https://hub.docker.com/.
docker images #Lista las imagenes instaladas
docker tag IMAGE_ID REPOSITORY:TAG #Cambia el repositorio y tag, por si queremos hacer modificaciones, o para correrlo de forma más facil
docker run -i -t IMAGE_ID #Carga el contenedor por su IMAGE_ID y accede al mismo
docker ps -a #lista todos los contenedores creados y si estan activos on fueron cerrados
docker start IMAGE_ID #Carga el contenedor pero sin acceder. Para acceder de esta forma hay que correr el comando atach
docker stop IMAGE_ID #Cierra la imagen
docker atach IMAGE_ID #Accede a una imagen que se encuentre corriendo
docker commit CONTAINER_ID REPOSITORY:TAG # La instancia de una imagen es un contenedor, si se hacen cambios, para que se guarden en la imagen hay que hacer un commit.
docker rm CONTAINER_ID #borrar contenedor
docker rmi IMAGE_ID #borrar imagen 
```

### Dentro de una imagen
```
exit #Cierra la imagenes
detach #Sale de la imagen pero sin cerrarla, util en el caso de servidores como el de Galaxy
```
