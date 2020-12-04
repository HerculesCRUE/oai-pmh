![](./CabeceraDocumentosMD.png)

| Fecha         | 04/12/2020                                                   |
| ------------- | ------------------------------------------------------------ |
|Titulo|Configuración e Instalación del servicio OAI-PMH para el SGI| 
|Descripción|Instrucciones de configuración e instalación del servicio OAI-PMH en Docker|
|Versión|0.1|
|Módulo|OAI-PMH|
|Tipo|Manual|
|Cambios de la Versión|Creación|

## Configuración e instalación

OAI PMH XML es un servicio web basado en OAI-PMH ([https://www.openarchives.org/OAI/openarchivesprotocol.html](https://www.openarchives.org/OAI/openarchivesprotocol.html)) que sirve XML ubicados dentro del propio servicio. Este servicio funciona como un mock-up, devuleve todos los registros con la fecha actual e ignora los parámetros from, until y resumptionToken. En lugar de ello cuando se le solicitan identificadores o records siempre deuelve el 50% de los que tiene de forma aleatoria.

Despliegue del servicio oaipmhxml
--------------------

Requisitos previos: docker + docker-compose

Para poder ejecutarlo debemos seguir el siguiente proceso (p.e. desde una ruta como /home/*usuario*/):

    mkdir oai-pmh-xml
    cd oai-pmh-xml

Una vez en el directorio "oai-pmh-xml" creamos un fichero docker-compose.yml con esta estructura (el puerto 5110 es configurable):

    version: '3'
     services:
      apioaipmhxml:
       image: apioaipmhxml
       ports:
        - 5110:80
       environment:
        ConfigUrl: "http://{dominio}/oai-pmh-xml/OAI_PMH"


El siguiente paso sería descargar la imagen docker:

    wget http://herc-as-front-desa.atica.um.es/docs/apioaipmhxml.tar.gz

Y finalmente la añadimos a docker y la ejecutamos:

    docker load < apioaipmhxml.tar.gz
    docker-compose up -d
