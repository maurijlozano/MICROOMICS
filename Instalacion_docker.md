INSATALAR DOCKER
================

# Instalar docker en Linux
Instrucciones en [https://docs.docker.com/install/linux/docker-ce/ubuntu/](https://docs.docker.com/install/linux/docker-ce/ubuntu/)

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

*Verificar que tenemos la llave correcta 9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88, buscando los últimos 8 caracteres.*

`sudo apt-key fingerprint 0EBFCD88`

## Agregar el repositorio de Docker
```
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

## Instalar docker
`sudo apt-get update`

Instalar la última versión de docker:

`sudo apt-get install docker-ce docker-ce-cli containerd.io`

Si aca sale un error *El paquete 'docker-ce' no tiene un candidato de instalación"*, hay que cambiar la versión del repositorio a 'bionic'.

## Para correr docker sin sudo!
```
sudo usermod -a -G docker user_name
newgrp docker 
```
* El último comando permite “refrescar” el grupo sin tener que reiniciar*


# Instalar Docker en windows
Bajar el programa del siguiente link [Docker](https://download.docker.com/win/stable/Docker%20for%20Windows%20Installer.exe) e instalarlo ejecutando el archivo descargado.

## Corriendo docker en windows
Buscar el programa en el menu de windows y ejecutarlo.
*No tildar la opción* "usar contenedores de windows en vez de contenedores de linux".

Cuando aparece la ballenita de docker en la bandeja de aplicaciones de windows (barra de windows, en la esquina derecha). Hacer click sobre el icono para verificar que la aplicacion se está ejecutando.

Los comandos de docker se ejecutaran utilizando **Powershell** de windows!

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

# Instalar el Docker de Microomics
Para descargar el Docker necesario para el curso, ejecutar en terminal (o Powershell si se encuentra en windows) el siguiente comando:
```
docker pull maurijlozano/microomics
```

## Correr Galaxy/MicroOmics
El primer paso será iniciar el Docker de Galaxy en un puerto local, y montando la carpeta de trabajo. Dado que las instacias de Docker son de solo lectura, es necesario exportar los datos en una carpeta compartida.
```
docker run -d -p 8080:80 -v ruta/a/la/carpeta/de/trabajo:/export/ maurijlozano/microomics
```

En este comando, el texto siguiente "ruta/a/la/carpeta/de/trabajo" debe ser reemplazado por la ruta en la cual se guardarán los archivos.

# Acceder a Galaxy/MicroOmics
Para acceder a Galaxy deberá abrir su explorador de internet, y escribir la dirección:
[http://localhost:8080](http://localhost:8080).  

## Configuración e instalación de aplicaciones
Para instalar paquetes adicionales a la imagen, deberán acceder como administradores. La cuenta de administrador por default es: 

admin_users: admin@galaxy.org  
password: admin

Una vez que ingresaron con privilegios de administrador aparecerá la pestaña *admin* en donde podrán seleccionar instalar aplicaciones desde toolshed.

