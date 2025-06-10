![[Swagger Logo.png]]
Swagger es un programa (de código abierto) para diseñar, construir, documentar y utilizar servicios web RESTful.
Swagger te brinda una URL donde se pueden ver, documentar y hasta PROBAR los endpoints. 
Video de guía: https://www.youtube.com/watch?v=RayDPBYou4I
Un dicho de Swagger: 
	***"Una API es tan buena como su documentación"***
Un ejemplo todo bonito de Swagger:
![[Swagger Example.png]]
# Uso de Swagger
## Al momento de desarrollar
Al crear las API's, las herramientas de SWAGGER se pueden usar para generar AUTOMATICAMENTE un documento de API basado en el código mismo. Como plan B, se puede usar [Swagger Codegen](https://github.com/swagger-api/swagger-codegen) para "desacoplar el código fuente de la API y generar código cliente y servidor directamente desde el diseño o documentación.
## Al momento de Interactuar
Al usar Swagger Codegen, se reduce el código a realizar y automatiza gran parte del código basándose directamente desde el documento de OpenAPI (Siguiendo los estándares recomendados de REST).
## Al momento de Documentar
Al momento de documentar basándose en los estándares de OpenAPI, las herramientas de Swagger permiten PROBAR EL CÓDIGO mientras estas documentándolo/leyendo la documentación (eto' e' una cosa e' loco'). Esto ayuda tanto a quien está documentando el proyecto, como a quien debe utilizarlo (Sobre todo si quien lo va a usar no tiene ni la más pálida idea de que es una API).

Nota del autor: Aunque suene redundante, Swagger esta PERFECTAMENTE documentado, así que no vale la pena detallar mucho acá. Dejo el link a los "Recursos Abiertos de Swagger", que incluyen la documentación de sus herramientas, Artículos relacionados, guías para aprender sobre los estándares de OpenAPI y Swagger mismo, entre otras cosas. [Swagger Resources](https://swagger.io/resources/)
Nota del autor 2:  Los archivos de Swagger se escriben en JavaScript y me re contra requete re 😖😖😖😖😖😖
Nota del autor "La tercera es la vencida": CONSIDERO que si vamos a hablar de SWAGGER, hay que hacer un repaso por [OpenAPI](https://www.openapis.org/).

# EXTRA EXTRA! 🗞️ OpenAPI

"OpenAPI Specification" es un lenguaje de especificación para las API's HTTP que provee sentido ESTANDARIZADO para definir API's (Tanto para uno mismo, como para otros).
EL punto de OpenAPI es ayudarte a descubrir rápidamente como funciona una API, como está configurada, como es su infraestructura, como generar código de cliente para pruebas y como crear casos de testeo para las API's.
OpenApi considera el ciclo de vida de una API como algo así:
![[LIFECYCLE API.png]]
El punto de OpenAPI es estandarizar la manera en la que se debe acompañar este ciclo de vida, ayudando a crear una buena infraestructura, formando así una "experiencia de desarrollador", un buen ambiente de testing y muchas cosas más. Más info en: https://www.openapis.org/what-is-openapi
El punto principal es hacer las API's "aptas para ser consumidas".