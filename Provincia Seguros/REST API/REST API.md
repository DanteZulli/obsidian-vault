## **REST API**: 
Ensamble de recursos interconectados.  (Estos recursos son conocidos como el _resource model_).
Gran parte de las buenas prácticas para diseñar REST API's están implícitas en el estándar HTTP. 
Se basa en el estándar CRUD.
- C -> Create -> Post
- R -> Read -> Get
- U -> Update -> Put
- D -> Delete -> 🤔
Usa JS y JSON. Maldito seas Roy Fielding...
Manejan conexiones bidireccionales entre cliente y servidor:

![[REST API Model.png]]
Al momento de enviar la petición al servidor, se envía con el siguiente formato:
- Header: _No me quedó claro_ Pd: más adelante si me quedo claro
- Operation: Operación a realizar (Post, Get, Put, Delete)
- Endpoint: valor a agregar al final de la url para referenciar a una parte específica de nuestros recursos.
- Parameters/Body: En caso de ser necesario, información extra o parámetro a agregar/modificar
**Ejemplo:** https://apicom.com/api/gerencia/v2/usuarios/get/2
Dominio de la API // API // RECURSOS // VERSIÓN // ENDPOINT // OPERACION // PARAMETROS
Esta dirección me devolvería (En teoría) el usuario con ID 2 de la API de Gerencia en su Versión 2
![[IBM Cloud Rest API Resume.png]]
Gran video de ejemplo sencillo sobre REST API: https://www.youtube.com/watch?v=lsMQRaeKNDk

### Vocabulario (Cortita y al pie, por si me olvido)
#### Respecto a Api's Interfaces
- **Application Programming** (Programación de aplicaciones)
Set de funciones y datos para facilitar interacciones entre programas de computadora.
- **Architectural Constraint** (Restricciones de arquitectura)
Limites en el comportamiento de los componentes del sistema (Para reforzar uniformidad y propiedad) ~Voy a profundizar esto más adelante
- **Architectural Style** (Estilo de la arquitectura)
Más que estilo, restricciones de estilo demarcadas de REST API.
- **Cache**
La capacidad de los recursos de guardar representaciones de sus diferentes estados, a modo de cache. Misma funcionalidad que el cache de una compu :P
- **Client-Server (Cliente Servidor)
Conjunto de reglas que definen los conceptos de los dos componentes principales de las relaciones en REST API. El cliente y el servidor, duh.
- **Code-on-demand**
La capacidad de REST de (opcionalmente) permitirle al servidor transferir scripts o ejecutables a sus clientes.
- **Entity body** (Cuerpo de la identidad)
Sección del HTML destinada a mantener o representar el recurso/información en cuestión. Mismo concepto de HTML (Te avisé).
- **Entity Header** (Encabezado de la identidad)
Sección del HTML encargada de comunicar los metadatos sobre un recurso y su representación. Te avise X2
- **Layered System** (Sistema de capas)
Sistema de capas que usa REST para "situarse" en medio del cliente/servidor sin comprometer la interfaz. (Ver diagramita bonito que copie :D)
- **Representation** (Representación)
El estado de un recurso "formatteado" para que sea interpletable y transferible via mensajes entre componentes
#### Respecto a protocolos de transferencia con REST
- Request message (Mensaje de petición)
Mensaje enviado de parte de los clientes para interactuar con un recurso (Via URL) 
~De ahora en adelante, cada que escriba "URL", me estoy refiriendo a que es URI-INDICATED (Un protocolo para representar direcciones web), que NO ES lo mismo que URL pero me queda cómodo.
Más información al respecto: https://en.wikipedia.org/wiki/Uniform_Resource_Identifier
- Resource (Recurso)
Cualquier concepto web que pueda ser referenciado vía URL y manipulado vía Interfaces Uniformes.
- Resource Identifier -> ID del recurso (Universal y único)
#### Respecto a REST API
- Uniform interface (Interfaz uniforme)
Un set de cuatro restricciones REST que estandarizan la comunicación entre los componentes web. A que no adivinas cuáles son?
#### Respecto a IDENTIFIERS (URI)
- Web API
API usada por los clientes para interactuar con un servicio web
- Web Browser
Cliente Web más común
- Web Client
Programa de computadora que sigue el estándar de modelado uniforme de interfaces de REST para aceptar y transferir representaciones con los servidores.
- Web Component
Un cliente, intermediario o servidor que sigue los estándares REST
- Web Resource Modeling
Framework Conceptual del cual se pueden tomar sus ideas para diseñar e implementar REST API's
- Web Server
Un programa que sigue las restricciones de diseñado uniforme de interfaces REST para aceptar y transferir recursos con los clientes.
- Web Service
Un web server programado con lógica específica y reusable.

~ Nota: Hay más vocabulario pero gran parte es conocimiento ya aprendido. Quiero basar esto plenamente en REST API, y en cosas que no sepa. El de conceptos con 1/16 de googleada ya salen solos.

## URI FORMAT (super importante)
Nota: Muy similar a las reglas que se siguen en los repos de las distros para compilar, publicar y subir paquetes :P
Acá voy a detallar las reglas de formato URI, SUPER IMPORTANTES para API REST.
Se basa en este "Internet Standard" (RFC 3986): https://datatracker.ietf.org/doc/html/rfc3986
Acá dejo un breve resumen :D
### Forward Slash Separator
El separador (/) indica relación jerárquica entre recursos. Por ejemplo:
api.provinciaseguros/gerencia/usuarios/usuario/id/nombre
QUEDA MÁS QUE CLARO QUE NO SE DEBE USAR "/" EN LAS URI
Nota: las REST API's no esperan ni trabajan con un "Trailing Slash" (Un / al final).
Si bien la existencia o no del mismo puede no variar en nada, semánticamente son DOS RECURSOS DISTINTOS, y en REST serán tratados como tal
En resumen, para rest: 
api.provinciaseguros/gerencia/ != api.provinciaseguros/gerencia
A pesar de que lleven a "la misma página/el mismo lugar"
### Hyphens
Los guiones (-) deben ser usados para mejorar la lectura de la URI
GUION MEDIO NO GUION BAJO (Ese es el underscore no seas pavo)
GUION BAJO = PECADO CAPITAL❌❌❌❌🚩🚩🚩🚩
### Lowercase Letters
SIEMPRE se van a preferir las letras en minúscula para las rutas. Las letras en mayúscula confunden. Así que, todo en minúscula.
### File extensions
Las extensiones de archivo NO DEBEN ser incluidas en las URI.
Si se requiere indicar que tipo de archivo es o como manejarlo, en REST se controla por medio de los MEDIA TYPES (Más adelante hablo de eso). Mientras tanto, quedarse con que no hay que indicar el tipo de archivo. usuario.json = usuario, index.html = index, y así.
Sobre media types: Hay selección de formato vía protocolos estandarizados de https, como también negociación o selección cuando multiples interpretaciones se encuentran disponibles. Así que es importante tener en cuenta todo esto.
## URI Authority Design
Esta sección va a hablar solo de convenciones de diseño al momento de nombrar cualquier cosa en REST
### Consistent SubDomain Names
El sub dominio de la API debe siempre aclarar que es una "api", y debe dejar bien en claro quien es su dueño. Por ejemplo: api.provincia-seguros/gerencia NO -> provincia/gerencia
#### Consistent SubDomain Names for DevPortal
El libro ACLARA que si bien no es necesario tener un dev portal, las REST API'S tienen asociado siempre uno, ya que es parte vital de la estructura interna de las api's. La misma regla que leiste antes aplica también a las url dentro del DevPortal. YA SE que es medio al pedo, porque solo lo accedemos pocas veces, tiene interfaz, y las querys son gestionadas, pero es un ESTÁNDAR, la gracia es RESPETARLO.
### Resource Modeling
El path URI tiene que llevarte directamente a un recurso REST en tu API. Cada recurso esta separado entre separadores (aunque suene redundante) y solo ASI deberá ser.
Por Ejemplo: 
``
```
api.provincia-seguros/gerencia/usuarios/id/nombre
NO
api.provincia-seguros/gerencia/usuariosid/nombre
O
api.provincia-seguros/gerencia/usuarios/usuariosid/nombreapellido
O cualquier cosa rara que se le parezca.
```

Nota en conclusión: Antes de pensar en como diseñar o escribir rutas URI, primero hay que pensar en como se deben escribir siguiendo el modelo de recursos de REST. 
No es difícil, en general si se ve lindo prolijo y ordenado, si sigue una filosofía y se aferra a un estándar esta bien. Ahora si se ve como si estuvieses accediendo a las páginas desde el file manager de windows, ahí si hay cosas que retocar.

# REESTRUCTURACIÓN DE LA INVESTIGACIÓN SOBRE API REST
Nuevos objetivos:
Hay que investigar sobre
- Documentación
- Definición
- Comparaciones (Con otras metodologías)
- Buenas Prácticas
- Anatomía Uri
## Documentación
Para documentar podemos utilizar el servicio/librería de [[SWAGGER]]. <- Tiene su propio doc en esta documentación
## Comparaciones
Alternativas a REST API's (Resumen corto)
### GraphQL:
Pros:
- Más rápido y eficiente que REST (Ya que solo manejan los datos que necesitan)
- Se puede especificar el dato requerido, y este tiene un punto de referencia exacto (Se hace solo una solicitud), a diferencia de REST que requiere varias solicitudes y tiene múltiples endpoints
Contras:
- No es bueno para cuando se requiere una estricta validación de datos
- No es bueno para proyectos con una gran cantidad de clientes/usuarios/etc.
### gRPC:
Pros:
- Permite a los desarrolladores definir interfaces de servicio y generar código de cliente y servidor en múltiples lenguajes de programación.
- No ve obliga a programar en JavaScript
- Maneja control de búfer y serialización de datos === Ideal para ámbitos donde importa el rendimiento
- Rápido y eficiente entre servicios
Contras:
- No es escalable
- En aplicaciones de gran tamaño, puede surgir una sobrecarga, y caput.
### WebSockets
Pros:
- La comunicación es BIDIRECCIONAL a diferencia de Rest que se basa en solicitud/respuestas
- El tiempo de respuesta es casi nulo
Contras: 
- No apta para manipulación de datos compleja
- No escalable. (Ninguna de las alternativas a REST presentan una estructura escalable, nisiquiera reconocida por los desarrolladores mismos. RARO)
### MQTT
Pros: 
- Ligero
- Open Source
- Casi ni usa ancho de banda
- Usa protocolo pub/sub [PARADIGMA DE PUBLICACIÓN/SUBSCRIPCIÓN](https://www.pubnub.com/guides/everything-you-need-to-know-about-pub-sub/)
- Ideal para optimizar el uso de recursos
Contras:
- No es lo ideal para manipular datos
- No esta pensado para lo que necesitamos, pero es una muy buena herramienta para proyectos IOT
### Otros proyectos
Hay miles de alternativas más, dejo los nombres de algunos que encontré por si se requiere profundizar en la investigación.
- Arquitectura EDA
- FALCOR
- Funciones PUB/NUB
- SOAP (Aunque en base sea casi como REST)
- RPC (Remote Procedure Call)
Etc, etc...

## Más Comparaciones (La venganza de las abreviaturas parecidas)
Para aprender a diferenciar y comprender más el mundo de las RESTful API's, voy a definir los siguientes conceptos; URL, URI y URN.
-URL es un LOCALIZADOR DE RECURSOS UNIFORME. Te informa el "como y donde" de "algo".
-URN es un NOMBRE DE RECURSO UNIFORME. Es un nombre simple y único para cada "algo".
-URI es un IDENTIFICADOR DE RECURSOS UNIFORME. Es tanto URL como URN.
Entonces, para tener en cuenta:
1) Cada URL y URN es URI porque URI no es más que un SUPERSET de estos dos. Dejo un diagramita para los lentos de reacción como yo :P
![[URI URN Y URL DIAGRAMA.png]]
2) Las URL incluyen protocolo (ftp://, http://, https://) junto a la localización del recurso.
3) URN es el nombre que cada recurso tiene, y no es ambiguo.
Ahora queda más claro que no se pueden marcar diferencias entre estos, ya que son todos cosas distintas :D
![[Otro diagrama de URL URI Y URN.png]]
Más info al respecto en: https://www.java67.com/2013/01/difference-between-url-uri-and-urn.html

# Definición, Buenas prácticas y Anatomía URI 
Respecto a estos 3 temas. Ya fueron documentados (En gran parte) a lo largo de este documento. Igualmente voy a dejar unos links a recursos extra a modo de "Bibliografía complementaria" que pienso está más que sobrada en información. 
- [Buenas prácticas de REST API's según freeCodeCamp](https://www.freecodecamp.org/news/rest-api-best-practices-rest-endpoint-design-examples/)
- Un diagrama sintáctico de como deben escribirse las URI. [Explicación de cada segmento](https://es.wikipedia.org/wiki/Identificador_de_recursos_uniforme)

![[Diagrama Sintáctico URI.png]]

** procede a preparar la presentación  
