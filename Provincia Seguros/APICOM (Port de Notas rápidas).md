# Investigación API'S Compartidas - Estrategia tecnológica - Notas[](https://dev.azure.com/pseguros/Estrategia%20Tecnol%C3%B3gica/_wiki/wikis/Estrategia-Tecnol%C3%B3gica.wiki?wikiVersion=GBwikiMaster&pagePath=/Investigaci%C3%B3n%20API%27s%20Compartidas%20%252D%20APICOM&pageId=2658&_a=edit&anchor=investigaci%C3%B3n-api%27s-compartidas---estrategia-tecnol%C3%B3gica---notas)

## Disclaimer:[](https://dev.azure.com/pseguros/Estrategia%20Tecnol%C3%B3gica/_wiki/wikis/Estrategia-Tecnol%C3%B3gica.wiki?wikiVersion=GBwikiMaster&pagePath=/Investigaci%C3%B3n%20API%27s%20Compartidas%20%252D%20APICOM&pageId=2658&_a=edit&anchor=disclaimer%3A)

#### (THIS ~~SOFTWARE~~ _WIKI_ IS PROVIDED “AS IS”, WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED)[](https://dev.azure.com/pseguros/Estrategia%20Tecnol%C3%B3gica/_wiki/wikis/Estrategia-Tecnol%C3%B3gica.wiki?wikiVersion=GBwikiMaster&pagePath=/Investigaci%C3%B3n%20API%27s%20Compartidas%20%252D%20APICOM&pageId=2658&_a=edit&anchor=(this-~~software~~-_wiki_-is-provided-%E2%80%9Cas-is%E2%80%9D%2C-without-warranty-of-any-kind%2C-express-or-implied))

Las notas de esta investigación no siguen ningún formato ni estilo. Las voy subiendo o modificando a medida que voy avanzando y no las organicé (Perdón). Queda pendiente una reformulación y una buena emprolijada. Mientras tanto, queda usted advertido, no me hago responsable del mal de ojo de nadie. 😛  
Es una mezcla de Spanglish, posee vínculos a documentación o páginas extra, videos de youtube y otras cosas de por medio. Cualquier cosa se puede editar, me pueden dejar un mensaje. Se aceptan sugerencias ❤️  
_~Dante Zulli_

# Modelo ER del proyecto (Sujeto a cambios):[](https://dev.azure.com/pseguros/Estrategia%20Tecnol%C3%B3gica/_wiki/wikis/Estrategia-Tecnol%C3%B3gica.wiki?wikiVersion=GBwikiMaster&pagePath=/Investigaci%C3%B3n%20API%27s%20Compartidas%20%252D%20APICOM&pageId=2658&_a=edit&anchor=modelo-er-del-proyecto-(sujeto-a-cambios)%3A)

![modelo_er.drawio.svg](https://dev.azure.com/pseguros/4777c230-4156-4514-bf57-853964c8fbfb/_apis/git/repositories/203e1d78-a23b-48f8-9fff-48f0fcf7bfaf/Items?path=/.attachments/modelo_er.drawio-96362ae9-cf50-4b8e-93f3-4fd0abe34a29.svg&download=false&resolveLfs=true&%24format=octetStream&api-version=5.0-preview.1&sanitize=true&versionDescriptor.version=wikiMaster)  
En PNG (El vector no se muestra bien, va, el PNG menos xd):  
![modelo_er.drawio.png](https://dev.azure.com/pseguros/4777c230-4156-4514-bf57-853964c8fbfb/_apis/git/repositories/203e1d78-a23b-48f8-9fff-48f0fcf7bfaf/Items?path=/.attachments/modelo_er.drawio-3c6b543e-0bf1-4543-83bc-026cb423f5d0.png&download=false&resolveLfs=true&%24format=octetStream&api-version=5.0-preview.1&sanitize=true&versionDescriptor.version=wikiMaster)

Apis Compartidas

#### Developer Portal:[](https://dev.azure.com/pseguros/Estrategia%20Tecnol%C3%B3gica/_wiki/wikis/Estrategia-Tecnol%C3%B3gica.wiki?wikiVersion=GBwikiMaster&pagePath=/Investigaci%C3%B3n%20API%27s%20Compartidas%20%252D%20APICOM&pageId=2658&_a=edit&anchor=developer-portal%3A)

Centrales de información que contienen productos APIS, documentación y microservicios.  
Son repositorios externos de información y recursos para desarrolladores que utilicen las API's de una o varias compañias. Los dev portals suelen estar acompañados de foros y espacios donde pueden testear API's y otros recursos.  
Extisten portales internos y externos.  
Centralizados.  
Existen 3 tipos de portales, y cada uno se centra en distintas características: catálogos de servicios, herramientas para generación de proyectos y herramientas personalizadas.  
Existen varios developer portals en internet. Son de libre visualización, pero para obtener acceso de prueba o producción, se requiere un token.  
No solo se utilizan para compartir API's.

- BACKSTAGE:  
    es una buena herramienta para crear developer portals. (Es open source y gratuita y tiene mucha documentacion, además de video tutoriales y contenido en la red)  
    [https://backstage.io/](https://backstage.io/)   
    [https://github.com/backstage/backstage#getting-started](https://github.com/backstage/backstage#getting-started)   
    Breve definición:  
    Backstage is an open platform for building developer portals. Powered by a centralized software catalog, Backstage restores order to your microservices and infrastructure and enables your product teams to ship high-quality code quickly without compromising autonomy.  
    Backstage unifies all your infrastructure tooling, services, and documentation to create a streamlined development environment from end to end.  
    Fue creada por Spotify y donada a Cloud Native Computing Foundation  
    [https://www.youtube.com/watch?v=85TQEpNCaU0](https://www.youtube.com/watch?v=85TQEpNCaU0)   
    Backstage apunta a crear un ecosistema y manejar los repositorios o apis de manera comoda.  
    Permite configurar relaciones, links, componentes, subcomponentes, documentacion, entre otras cosas.  
    Posee extensiones (plugins) para configurar el portal :3  
    Usa Ts, React, Kubernetes, entre otras tecnologías.
    
- PORT:  
    No es open source, no es COMPLETAMENTE GRATIS. Tiene un plan gratuito que cuenta con todas las funciones importantes para una empresa. (Extepto SSO(? Posible "Single Sing-On" Investigar.)  
    (Puede llegar ser limitado)  
    "No code solution" Interfaz para manejar las necesidades de estrategia e ingenieria de la empresa/organizacion.  
    Sirve para manejar microservicios, Clusters, repositorios, paquetes, virtualizacion.  
    Port tiene una muy buena y comoda interfaz. Te permite ver el Log de auditorias, la documentacion, manejar scorecards, y otras cosas de la API dentro de Overview y Dashboard.  
    Se pueden crear paquetes, servicios, Entornos de desarrollo, recursos cloud, etc etc.  
    Se puede manejar Syntaxis a seguir, sincronizar paquetes, dentro de otras funcionalidades.
    

NOTAS DEL AUTOR: Dicen que Port > Backstage, pero no creo que puedan superar que Backstage sea Open Source 🐃🐃🐃🐃  
Ambos pueden manejar templates para lenguajes y frameworks 😄  
Poseen versionado de API's (Obvio pero no tan obvio)

NOMBRE POSIBLE: APICOM  
Separación de Apis críticas.  
Se podrá codear a mano algo así ? (Seguro que sí) Conviene? ...

#### DEVPORTAL AWARDS 2022 - FIRST GALA (RECORDING) -[](https://dev.azure.com/pseguros/Estrategia%20Tecnol%C3%B3gica/_wiki/wikis/Estrategia-Tecnol%C3%B3gica.wiki?wikiVersion=GBwikiMaster&pagePath=/Investigaci%C3%B3n%20API%27s%20Compartidas%20%252D%20APICOM&pageId=2658&_a=edit&anchor=devportal-awards-2022---first-gala-(recording)--)

##### Algunos de los ganadores de premios de DevPortals de 2022 como inspiracion (A REVISAR)[](https://dev.azure.com/pseguros/Estrategia%20Tecnol%C3%B3gica/_wiki/wikis/Estrategia-Tecnol%C3%B3gica.wiki?wikiVersion=GBwikiMaster&pagePath=/Investigaci%C3%B3n%20API%27s%20Compartidas%20%252D%20APICOM&pageId=2658&_a=edit&anchor=algunos-de-los-ganadores-de-premios-de-devportals-de-2022-como-inspiracion-(a-revisar))

*Chase Developer  
*ABN Amro Developer Portal  
*Fiserv Developer Studio  
*Visa Developer Center

Conexión (Principio de idea para el diagrama final):  
API Portal (DevPortal)

Documentation,  
Registration,  
Analysis,  
Community,  
Productivity

Architects and Developers  
(Internal and Third-Party)

Preguntas para hacerle a Mariano:  
Como se manejan las API's actualmente?  
(Muestra de ejemplo si puede ser)  
Por dónde son accesibles?  
Estándares?

#### Definiciones coloquiales[](https://dev.azure.com/pseguros/Estrategia%20Tecnol%C3%B3gica/_wiki/wikis/Estrategia-Tecnol%C3%B3gica.wiki?wikiVersion=GBwikiMaster&pagePath=/Investigaci%C3%B3n%20API%27s%20Compartidas%20%252D%20APICOM&pageId=2658&_a=edit&anchor=definiciones-coloquiales)

API MANAGMENT PLATFORM:  
Compuesta por:

- Intercambiador de APIs: Componente cuya principal función es la de habilitar la interconexión entre los servicios y los consumidores, a través de las APIs publicadas en él.
- Gestor de APIs: Componente cuya principal responsabilidad es la de ofrecer a los proveedores capacidades de alta configuración y publicación de sus APIs en el componente API Gateway.
- Portal de APIs (GateWay): Componente dedicado a recopilar toda la información necesaria para los consumidores sobre las APIs publicadas en el API Gateway.

Más definiciones:

- API Gateway: Routing requests, data composition, protocol transformations, security authentication and authorization (state-of-the-art authentication).
- Developer Portal: Self-Service hub to browse access and share api documentation
- LifeCycle Manager: SystemHealty, build, testing, managment or retire apis, version control.

Nota: hay más definiciones y subtipos de cada uno de estos "componentes", como manejo de métricas (Resource manager), etc.

**BA DATA** tiene un DevPortal con datasets, API's abiertas al consumo, con links a donde conectarse y mandar las querys. Para revisar, es de público uso.

Notes: Charla con Mariano

- Falta revisar arquitectura (No se tiene certeza siquiera de si hay una)
- Fragmentada funcionalmente entre Jar's (Una para Sinienstros, otra para Cobranzas, etc)
- Hay Api Man (NO DECIR EN VOZ ALTA)
- Hay una dependencia commons alojada en un servidor
- Jar y War por ejemplo
- Apps productivas de negocio y apps de apoyo
- Multitecnologias
- Concentrador de Apis
- Cada uno tiene que desarrollar su app por su propia cuenta
- Comencemos por Apis comunes
- Nube interna
- Api Rest
- Post get y patch bajo apicom.gerencias/v1 (Ejemplo)
- Repasar API REST CONCEPTOS

***FORO INTERNO IMPORTANTE  
Aguante backstage, ya tiene esta opción

Hay que armar dos servidores físicos (Virt):  
**APPS - BD**  
APPS: Acá van todas las apps que desarrollemos, migremos, etc >>>>> Docker >>>> SV APPS con su propio ENDPOINT  
DB con MySql y MongoDB  
Los dos SV serían NUESTROS (Estrategia tecnológica)

-Profundizar API REST:  
Con Ejemplos reales, profundizar, full teoria, y después pasar al marco teórico. Prácticas  [[REST API]]
Uso exhaustivo de postman  
Un eslabón del proyecto principal.