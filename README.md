![](./Docs/Images/CabeceraDocumentosMD.png)

## Acerca de OAI PMH XML
[<img align="right" width="100px" src="https://dotnetfoundation.org/img/logo_big.svg" />](https://dotnetfoundation.org/projects?searchquery=IdentityServer&type=project)

Accesible en pruebas en esta dirección a través de swagger: http://herc-as-front-desa.atica.um.es/oai-pmh-xml/swagger/index.html.

OAI PMH XML es un servicio web basado en OAI-PMH ([https://www.openarchives.org/OAI/openarchivesprotocol.html](https://www.openarchives.org/OAI/openarchivesprotocol.html)) que sirve XML ubicados dentro del propio servicio. La versión actual de este servicio funciona como un mock-up, devolviendo todos los registros con la fecha actual e ignorando los parámetros from, until y resumptionToken. En lugar de ello, cuando se le solicitan identificadores o registros siempre devuelve la mitad aproximada de los que tiene, de forma aleatoria. El metadataprefix que hay que enviar para obtener los xml es 'XML'.

## Fichero XMLs.zip
En el fichero [XMLs.zip](https://github.com/HerculesCRUE/oai-pmh/blob/master/XMLs.zip) se encuentra un set de pruebas de ficheros XML para devolver por el servicio OAI PMH XML, para el funcionamiento del servicio deben descomprimirse dentro del servicio en la carpeta XML.

## Ficheros XSD
En la carpeta [XSD](https://github.com/HerculesCRUE/oai-pmh/blob/master/XSD) se encuentran los esquemas xsd que deben cumplir los XML que devolverá el servicio.

## Configuración del repositorio de XML del servicio
Los XML que sirve el servicio se tienen que ubicar dentro de la carpeta XML, dentro de esta carpeta se crearán N carpetas que representarán los [setspec](https://www.openarchives.org/OAI/openarchivesprotocol.html#Set) y dentro de cada carpeta setspec se ubicarán los ficheros XML, el nombre de cada uno de ellos se utilizará como identificador precedido por el nombre de setspec.

## Configuración en el appsettings.json
    {
      "Logging": {
          "LogLevel": {
                "Default": "Information",
                "Microsoft": "Warning",
                "Microsoft.Hosting.Lifetime":"Information"
          }
      },
      "AllowedHosts": "*",
      "ConfigUrl": "http://herc-as-front-desa.atica.um.es/oai-pmh-xml/OAI_PMH"
    }
 - LogLevel.Default: Nivel de error por defecto
 - LogLevel.Microsoft: Nivel de error para los errores propios de Microsoft
 - LogLevel.Microsoft.Hosting.Lifetime: Nivel de error para los errores de host
 - ConfigUrl: URL donde está lanzada esta aplicación 

## Dependencias

- **docfx.console**: versión 2.53.1
- **Microsoft.AspNetCore.Mvc.Formatters.Json**: versión 2.2.0
- **Microsoft.AspNetCore.Mvc.NewtonsoftJson**: versión 3.0.0
- **EntityFrameworkCore**: versión 3.1.1
- **OaiPmhNet**: versión 0.4.1
- **RestSharp**: versión 106.10.1
- **Swashbuckle.AspNetCore**: versión 5.0.0
- **Swashbuckle.AspNetCore.Annotations**: versión 5.0.0
- **System.ServiceModel.Duplex**: versión 4.4.*
- **System.ServiceModel.Http**: versión 4.4.*
- **System.ServiceModel.NetTcp**: versión 4.4.*
- **System.ServiceModel.Security**: versión 4.4.*

### Configuración e instalación

Las [instrucciones de configuración e instalación](https://github.com/HerculesCRUE/oai-pmh/blob/master/Configuraci%C3%B3n%20e%20Instalaci%C3%B3n.md) son el punto de partida para comenzar a usar los desarrollos de Hércules ASIO
