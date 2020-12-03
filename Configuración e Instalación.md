![](./Docs/media/CabeceraDocumentosMD.png)

## Configuración e instalación

Este documento explica cómo configurar el entorno en el que desplegar los
módulos desarrollados una vez descargados desde GIT y posteriormente
compilados.

## índice

 - [Configuración del entorno](#configuración-del-entorno)
   	 - [Instalar dotnet](#instalar-dotnet)
   	 - [HTPP + proxy](#htpp-+-proxy)
   	 - [Instalar Git](#instalar-git)
- [Descarga de los proyectos](#descarga-de-los-proyectos)
- [Instalación](#instalación)
	- [Configuración de los apis](#configuración-de-los-apis)
	- [Control de aplicaciones](#control-de-aplicaciones)
		- [Creación de los script para levantar los apis](#creación-de-los-script-para-levantar-los-apis)
		- [Creación de servicios](#creación-de-servicios)

## Configuración del entorno
### Instalar dotnet

Abra un terminal y ejecute el comando siguiente:

    sudo rpm -Uvh https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm

Instalación del SDK de .NET Core
Actualice los productos disponibles para la instalación y, después, instale el SDK de .NET Core. En el terminal, ejecute el comando siguiente.

    sudo yum install dotnet-sdk-3.1

Instalación del entorno de ejecución de ASP.NET Core
Actualice los productos disponibles para la instalación y, después, instale el entorno de ejecución de ASP.NET. En el terminal, ejecute el comando siguiente.

    sudo yum install aspnetcore-runtime-3.1

Instalación del entorno de ejecución de .NET Core
Actualice los productos disponibles para la instalación y, después, instale el entorno de ejecución de .NET Core. En el terminal, ejecute el comando siguiente.
sudo yum install dotnet-runtime-3.1


### HTTP + proxy

Para poder utilizar las aplicaciones debemos instalar un proxy que redirija las peticiones que hagamos al servidor apache al puerto donde tengamos levantada nuestra aplicación.
Primero instalamos httpd con este comando:

    yum install httpd mod_ssl

Para que nuestro proxy funcione correctamente debemos ejecutar el siguiente comando:

    /usr/sbin/setsebool -P httpd_can_network_connect 1

Para más información sobre el paso anterior:
https://unix.stackexchange.com/questions/174593/centos-7-httpd-failed-to-make-connection-with-backend
Lo primero que debemos hacer es indicar a httpd que use los módulos del proxy. Para ello debemos añadir estas líneas en /etc/httpd/conf/httpd.conf

    LoadModule proxy_module modules/mod_proxy.so
    LoadModule proxy_http_module modules/mod_proxy_http.so

Una vez hecho esto tenemos que hacer un archivo de configuración para redirigir las peticiones a httpd hacia el sitio correcto. Para ello creamos un archivo .conf en /etc/httpd/conf.d con un contenido como este:

    <VirtualHost *:80>
        ServerName pruebasdotnet.gnoss.com
        #OAI-PMH-XML
        ProxyPass /oai-pmh-xml http://127.0.0.1:5000
        ProxyPassReverse /oai-pmh-xml http://127.0.0.1:5000
    </VirtualHost>

Con esta configuración conseguimos que lo que se pida a través del puerto 80 a pruebasdotnet.gnoss.com/oai-pmh-xml el proxy lo redirija a localhost:5000 que es donde nuestra aplicación URIS está a la escucha.
Por último Activamos el servicio HTTPD y lo iniciamos con estos comandos:

 - `systemctl enable httpd`
 - `systemctl start httpd`

### Instalar Git

Para instalar Git basta con ejecutar el siguiente comando:

    yum install git


## Descarga de los proyectos

En esta apartado se explica como desplegar mediante la descarga de los proyectos
y su posterior compilación.Empezamos descargando el repositorio de git, con el comando:

    git clone https://github.com/HerculesCRUE/oai-pmh.git

Tras realizar este comando de git, se nos pedirá nuestra autenticación para verificar que tenemos acceso al repositorio. 
Una vez realizado tendremos descargada una carpeta GnossDeustoBackend con los diferentes proyectos:

 - **OAI_PMH_XML**: se encuentra en OAI_PMH_XML/OAI_PMH_XML
 
Las rutas anteriores son las que hay que compilar y ejecutar con los comandos dotnet, por lo que hay que tenerlas en cuenta.


## Instalación

### Configuración de los Apis

Antes de ejecutar los apis deberíamos configurar los elementos necesarios para que funcionen correctamente, por ello habrá que configurar los apis que aparecen a continuación:

 - **OAI_PMH_XML**: En este api tendremos que configurar en el fichero appsettings.json según se explica en el [readme](https://github.com/HerculesCRUE/oai-pmh/tree/master/readme.md).

### Control de aplicaciones

Aspectos a tener en cuenta:

 - Puerto en el que vamos a levantar la aplicación.
 - Ruta del servicio descargado: esta ruta la podemos obtener realizando el comando pwd dentro de la ruta del proyecto donde nos hemos descargado el repositorio GnossDeustoBackend.
 
Para levantar las aplicaciones tenemos dos opciones, mediante la ejecución de scripts o crear estas aplicaciones como servicios, que se explican a continuación.

#### Creación de los script para levantar los apis

En este apartado vamos a crear los script para levantar, para ello nos movemos a la raíz con cd # y creamos un scritp para cada servicio:

 1. Creamos el servicio con nano nombre_del_scritp.sh
 2. Dentro del script ponemos las siguientes líneas
	 1. `#!/bin/sh`
	 2. cd y la ruta que hemos obtenido con el comando pwd. Ejemplo:
		 1.  `cd /root/GnossDeustoBackend/oai-pmh/oai-pmh-xml`
	 3. El comando dotnet run –urls”http:/0.0.0.0:puertoConfigurado” y el carácter & para ejecutarlo en segundo plano. Ejemplo:
		 1. `dotnet run --urls "http://0.0.0.0:5000" &`
	 4. Pulsamos CTRL-O para guardar.
	 5. Pulsamos CTRL-X para salir.
 3. Finalmente deberemos darle permisos de ejecución por lo que tendremos que ejecutar el siguiente comando: `chmod +x apiUris.sh`
 4. Para ejecutar el script vale con ejecutar `./apiUris.sh` que levantará nuestro servicio. 

#### Creación de servicios
Para que nuestro sistema controle la ejecución de las aplicaciones como un servicio debemos crear un archivo .service por cada aplicación en /etc/systemd/system
Este es un archivo de servicio de ejemplo de la aplicación. Debemos indicar el WorkingDirectory, el puerto en ExecStart y el usuario con que vayamos a ejecutar la aplicación

    [Unit]
    Description=Example .NET Web API App running on Centos
    
    [Service]
    WorkingDirectory=”Ponemos el path donde esté la app”
    ExecStart=dotnet run --urls "http://0.0.0.0:puerto" 
    Restart=always
    # Restart service after 10 seconds if the dotnet service crashes:
    RestartSec=10
    KillSignal=SIGINT
    SyslogIdentifier=dotnet-example
    User=www-data
    Environment=ASPNETCORE_ENVIRONMENT=Production
    Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false
    
    [Install]
    WantedBy=multi-user.target

Por último ejecutamos estos comandos en el directorio donde estamos.

 - `systemctl enable “nombre del archivo.service”`
 - `systemctl start “nombre del archivo.service”`

De este modo no nos tendremos que preocupar de que nuestras aplicaciones se ejecuten porque serán servicios controlados por el sistema.
