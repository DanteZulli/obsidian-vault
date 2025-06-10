```table-of-contents
```
## A. Definición de Ideas o Requisitos según el [Estándar IEEE 830](https://www.fdi.ucm.es/profesor/gmendez/docs/is0809/ieee830.pdf)

### A.1. Introducción

#### A.1.1. Propósito

El presente documento tiene como finalidad especificar de manera clara, completa y estructurada los requisitos del sistema "App SUBE", una aplicación destinada a la gestión virtual de tarjetas SUBE utilizadas en el transporte público en Argentina.

Su contenido está dirigido al futuro equipo de desarrollo de software, incluyendo analistas funcionales, diseñadores, programadores, testers y personal de aseguramiento de calidad (QA), con el objetivo de facilitar el diseño, desarrollo, validación y mantenimiento del sistema.  

#### A.1.2. Ámbito del Sistema

El sistema a desarrollar se denominará "App SUBE" y consistirá en una aplicación móvil y web destinada a la gestión digital de tarjetas SUBE, utilizadas en el transporte público de Argentina. 

El sistema permitirá a los usuarios gestionar sus tarjetas SUBE de manera digital, facilitando el acceso a sus funcionalidades principales y reduciendo la necesidad de acudir a terminales físicas. La aplicación servirá como complemento al ecosistema existente del Sistema Único de Boleto Electrónico, integrando funcionalidades tradicionales con nuevas capacidades digitales.

Quedan fuera del alcance de esta versión funcionalidades como el pago directo del transporte, integración con recorridos o localización en tiempo real, edición de datos personales desde la aplicación o transferencia de saldo entre tarjetas, entre otras funcionalidades que podrán ser consideradas en versiones futuras del sistema.

El objetivo principal es brindar una herramienta ágil y centralizada para la gestión de la tarjeta SUBE, facilitando el acceso digital a sus funcionalidades y reduciendo la necesidad de acudir a terminales físicas, sin descuidar a los usuarios que requieran soporte adicional.

#### A.1.3. Definiciones, Acrónimos y Abreviaturas

En esta subsección se definirán todos los términos, acrónimos y abreviaturas utilizadas en la ERS.

- **SUBE / Tarjeta SUBE / Sistema Único de Boleto Electrónico:** Sistema de pago electrónico implementado en Argentina en 2009 para el transporte público. Funciona en colectivos, trenes, subtes, metrotranvías, trolebuses y lanchas, y es utilizado por millones de personas en diversas localidades del país.
  
- **NFC (Near Field Communication):** Tecnología de comunicación inalámbrica de corto alcance que permite el intercambio de datos entre dispositivos cuando están muy cerca el uno del otro. En el contexto de la App SUBE, se refiere a la funcionalidad de dispositivos móviles (teléfonos) que permite validar y acreditar cargas directamente en la tarjeta SUBE sin necesidad de una terminal física.

- **ERS (Especificación de Requisitos de Software):** Documento formal que describe detalladamente los requisitos funcionales y no funcionales de un sistema de software, siguiendo el estándar IEEE 830.

- **API (Application Programming Interface):** Conjunto de definiciones y protocolos que permiten la comunicación entre diferentes componentes de software. En el contexto de la App SUBE, se refiere a las interfaces proporcionadas por el sistema SUBE para acceder a sus funcionalidades.

- **HTTPS/TLS 1.2:** Protocolo de seguridad que proporciona comunicación cifrada entre el cliente y el servidor. HTTPS es la versión segura de HTTP, y TLS 1.2 es la versión específica del protocolo de seguridad utilizada.

- **WebSockets:** Protocolo de comunicación que proporciona un canal de comunicación bidireccional y persistente entre el cliente y el servidor, permitiendo actualizaciones en tiempo real.

#### A.1.4. Referencias

El desarrollo de la "App SUBE" se basa en la documentación oficial del Sistema Único de Boleto Electrónico, disponible en el [portal del gobierno argentino](https://www.argentina.gob.ar/sube). Asimismo, se consideran las normativas vigentes en materia de protección de datos personales ([Ley N° 25.326](https://www.argentina.gob.ar/sites/default/files/arg_ley25326.pdf)).

Para el diseño de la interfaz de usuario, se han tomado como referencia las guías oficiales de diseño de las principales plataformas móviles: las [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/) de Apple para iOS y las [Material Design Guidelines](https://m2.material.io/design/guidelines-overview) de Google para Android. Estas guías proporcionan estándares y mejores prácticas para crear experiencias de usuario consistentes y accesibles en cada plataforma.

#### A.1.5. Visión General del Documento

Este documento de Especificación de Requisitos Software (ERS) está estructurado en cuatro secciones principales que guían al lector desde una visión general hasta los detalles específicos de implementación. La primera sección introduce el propósito y contexto del sistema, mientras que la segunda proporciona una descripción general de sus características y limitaciones. La tercera sección detalla los requisitos específicos que el sistema debe cumplir, y finalmente, los apéndices contienen información complementaria relevante para el desarrollo.

### A.2. Descripción General

#### A.2.1. Perspectiva del Producto

La "App SUBE" se concibe como un complemento al ecosistema existente del Sistema Único de Boleto Electrónico (SUBE) en Argentina. No es un sistema independiente, sino que depende directamente de las APIs expuestas por el gobierno de la nación (los propietarios originales del sistema SUBE). A través de estas APIs, la aplicación podrá consultar y registrar información esencial para el usuario, como los viajes realizados, las cargas efectuadas y otros movimientos de la tarjeta.

El sistema también interactuará con las distintas plataformas de verificación de cobros y pagos, integrándose con servicios de terceros como procesadores de tarjetas de crédito, débito y billeteras virtuales. Esto permitirá la realización de recargas virtuales y la gestión de transacciones desde la aplicación misma.

La App SUBE busca facilitar a los usuarios el uso, la recarga, la gestión y el seguimiento de sus tarjetas, optimizando la experiencia del usuario con funcionalidades innovadoras como la recarga virtual a través de tecnología NFC, lo cual representa una evolución y una extensión de las capacidades del sistema SUBE tradicional.

#### A.2.2. Funciones del Producto

La "App SUBE" proporcionará a los usuarios un conjunto de funcionalidades esenciales para la gestión digital de sus tarjetas SUBE. A grandes rasgos, el sistema permitirá a los usuarios la gestión de cuentas y tarjetas, facilitando el registro y la asociación de múltiples tarjetas SUBE a un único perfil para una administración centralizada. 

También ofrecerá la capacidad de consultar el saldo actualizado de las tarjetas y visualizar un historial de movimientos detallado que incluya viajes y recargas. 

Además, el sistema soportará la realización de recargas de saldo mediante diversos medios de pago digitales y enviará alertas y notificaciones relevantes al usuario relacionadas con el estado de sus tarjetas y transacciones. Finalmente, incluirá una funcionalidad para la localización de terminales físicas de validación de carga.

#### A.2.3. Características de los Usuarios

La aplicación "App SUBE" está pensada para un público amplio y diverso, compuesto principalmente por ciudadanos argentinos que utilizan el transporte público y cuentan con una tarjeta SUBE. Se parte del supuesto de que los usuarios ya están familiarizados con el uso tradicional de la tarjeta en su formato físico.

Se asume que el usuario promedio tiene conocimientos básicos o intermedios en el uso de smartphones y aplicaciones móviles: puede navegar interfaces simples y entender mensajes o notificaciones, aunque no necesariamente posee experiencia técnica avanzada.

Aunque la aplicación está diseñada para ser intuitiva y fácil de usar, se reconoce que algunos usuarios pueden no estar habituados a ciertas funciones digitales, como la recarga virtual o la validación por NFC. Por eso, la interfaz y los mensajes deben ser claros y guiados, minimizando cualquier posible confusión.

#### A.2.4. Restricciones

El desarrollo de la "App SUBE" estará condicionado por las siguientes restricciones:

**Políticas y Regulaciones:**
- Cumplimiento estricto de las normativas vigentes en Argentina en materia de seguridad y privacidad de datos
- Adherencia a las políticas operativas y comerciales establecidas dentro del ecosistema SUBE

**Interfaces y Dependencias:**
- Dependencia de las APIs proporcionadas por los responsables del sistema SUBE
- Integración obligatoria con múltiples pasarelas de pago y servicios financieros digitales
- Necesidad de interoperabilidad con el sistema SUBE existente

**Limitaciones Técnicas:**
- Restricciones impuestas por las capacidades del hardware móvil (especialmente en relación a la tecnología NFC)
- Requisitos de accesibilidad multiplataforma para distintos dispositivos

**Criticalidad:**
- El sistema debe mantener altos estándares de disponibilidad y confiabilidad debido a su uso en el transporte público

#### A.2.5. Suposiciones y Dependencias

El funcionamiento y la evolución de la "App SUBE" están sujetos a una serie de suposiciones y dependen de factores externos que, de modificarse, podrían impactar directamente los requisitos del sistema. 

Un pilar fundamental es la suposición de la disponibilidad y estabilidad de las APIs provistas por el sistema SUBE original; cualquier interrupción o cambio en estas interfaces requerirá actualizaciones y adaptaciones en la aplicación, ya que esta opera como un complemento directo a dicho servicio. 

Asimismo, se asume una continuidad en la adopción y funcionalidad de la tecnología NFC en los dispositivos móviles; una modificación o migración significativa en este estándar de comunicación implicaría la necesidad de revisar y actualizar los mecanismos de recarga virtual implementados en la aplicación.

En lo que respecta a la normativa, se presupone que no habrá cambios regulatorios que afecten la naturaleza fundamental de la tarjeta SUBE y su funcionamiento como medio de pago para el transporte público, dado que la aplicación se enfoca meramente en la gestión y transformación digital del uso de la tarjeta existente. 

Las actualizaciones de los sistemas operativos Android e iOS no deberían generar dependencias críticas imprevistas, siempre y cuando se mantengan las herramientas y entornos de desarrollo actualizados para asegurar la compatibilidad. 

Finalmente, se asume que los usuarios dispondrán de una conexión a internet estable al momento de realizar operaciones críticas como recargas o consultas de saldo en tiempo real. Sin embargo, para la visualización de datos históricos de viajes o movimientos, una conexión inicial para sincronizar la información será suficiente, permitiendo un acceso posterior sin conectividad constante para la información ya descargada.

#### A.2.6. Requisitos Futuros

Aunque la versión inicial de la "App SUBE" se enfoca en la gestión centralizada y digital de las tarjetas SUBE, se han identificado diversas funcionalidades que, si bien quedan fuera del alcance de esta entrega, se contemplan para ser implementadas en futuras iteraciones del sistema. 

Entre los requisitos proyectados para próximas versiones se incluye la posibilidad de realizar el pago directo del transporte público a través de la aplicación. También se considera la integración con servicios de ruteo y localización en tiempo real, que permitiría a los usuarios planificar sus viajes y seguir el transporte en vivo.

Adicionalmente, se prevé la inclusión de funcionalidades para la edición de datos personales directamente desde la aplicación, ofreciendo mayor autonomía al usuario en la gestión de su perfil. 

Otra mejora planificada es la capacidad de transferir saldo entre diferentes tarjetas SUBE asociadas a la misma cuenta, lo que brindaría mayor flexibilidad en la administración familiar o personal de los recursos. 

Estas funcionalidades futuras serán evaluadas y priorizadas en función de la evolución de las necesidades de los usuarios y las capacidades del ecosistema SUBE.

### A.3. Requisitos Específicos

#### A.3.1. Interfaces Externas

La aplicación deberá proporcionar una interfaz gráfica intuitiva y accesible que siga las guías de diseño establecidas para cada plataforma. La interfaz debe ser compatible con tecnologías de asistencia y proporcionar una experiencia de usuario consistente y predecible.

En cuanto a las interfaces con otros sistemas, la aplicación se integrará con el sistema central de SUBE mediante APIs REST, permitiendo la sincronización de datos y operaciones. También se establecerán conexiones con pasarelas de pago como Mercado Pago, Ualá, etc. y se integrarán servicios de geolocalización como Google Maps para el mapa de terminales. Para mantener a los usuarios informados, se implementará un sistema de notificaciones push.

Las comunicaciones del sistema deberán realizarse de manera segura utilizando HTTPS/TLS 1.2 o superior. Se implementará soporte para el protocolo NFC para las recargas virtuales, y se utilizarán WebSockets para mantener actualizaciones en tiempo real de la información relevante.

#### A.3.2. Funciones

Esta subsección detalla las funcionalidades que el sistema "App SUBE" deberá ofrecer a los usuarios, organizadas principalmente por **objetivos** que el sistema persigue y, cuando aplica, por **tipo de usuario** para detallar las funcionalidades específicas que le conciernen. Esta organización permite una comprensión clara de los servicios que el sistema provee y cómo cada grupo de usuarios interactúa con ellos.

##### A.3.2.1. Por Objetivos

Las funciones del sistema se agrupan según los principales objetivos que la "App SUBE" busca cumplir para el usuario:

- **Objetivo 1: Gestión de Cuentas y Tarjetas**
    
    - **Registro de Usuarios:** El sistema deberá permitir a un **nuevo usuario** crear una cuenta en la aplicación. Esto incluye la recolección de información personal esencial como nombre completo, DNI, dirección de correo electrónico y la creación de una contraseña segura.
    - **Asociación de Tarjetas SUBE:** El sistema deberá permitir al **usuario registrado** asociar una o más tarjetas SUBE a su cuenta personal. Esto se realizará mediante el ingreso del número de identificación de la tarjeta, lo que habilitará su gestión centralizada desde la aplicación.
    - **Gestión de Múltiples Tarjetas:** El sistema deberá permitir al **usuario registrado** administrar múltiples tarjetas SUBE asociadas a su cuenta. Esto incluye la visualización de un listado de todas las tarjetas vinculadas y la selección de cada una para acceder a sus detalles y operaciones.
- **Objetivo 2: Consulta de Información de Tarjeta**
    
    - **Consulta de Saldo en Tiempo Real:** El sistema deberá permitir al **usuario registrado** consultar el saldo disponible en tiempo real de cada tarjeta SUBE asociada a su cuenta.
    - **Visualización de Historial de Movimientos:** El sistema deberá permitir al **usuario registrado** acceder a un historial detallado de todos los movimientos de cada tarjeta asociada, incluyendo:
        - **Viajes realizados:** Fecha, hora, línea de transporte, monto del viaje.
        - **Recargas efectuadas:** Fecha, hora, monto recargado, medio de pago utilizado.
- **Objetivo 3: Realización de Recargas Virtuales**
    
    - **Inicio de Recarga:** El sistema deberá permitir al **usuario registrado** iniciar el proceso de recarga de saldo para cualquiera de sus tarjetas SUBE asociadas.
    - **Selección de Medio de Pago:** El sistema deberá ofrecer al **usuario registrado** diversas opciones de medios de pago digitales para la recarga, incluyendo tarjetas de crédito, tarjetas de débito y billeteras virtuales.
    - **Acreditación de Recarga vía NFC:** El sistema deberá permitir al **usuario registrado** acreditar la recarga de saldo directamente en la tarjeta SUBE física utilizando la tecnología NFC de su dispositivo móvil.
    - **Confirmación de Transacción:** El sistema deberá proporcionar al **usuario registrado** una confirmación visual y por notificación de la finalización exitosa de la recarga.
- **Objetivo 4: Recepción de Alertas y Notificaciones**
    
    - **Notificación de Confirmación de Recarga:** El sistema deberá enviar notificaciones automáticas al **usuario registrado** tras la confirmación exitosa de una recarga de saldo.
    - **Notificación de Saldo Bajo:** El sistema deberá enviar notificaciones automáticas al **usuario registrado** cuando el saldo de una tarjeta asociada sea inferior a un umbral predefinido (configurable por el usuario).
    - **Notificación de Asociación de Nueva Tarjeta:** El sistema deberá notificar al **usuario registrado** cuando una nueva tarjeta SUBE haya sido asociada exitosamente a su cuenta.
- **Objetivo 5: Localización de Puntos de Interés**
    
    - **Localización de Terminales Físicas de Validación:** El sistema deberá permitir al **usuario registrado** localizar en un mapa las terminales físicas de validación de carga y recarga de SUBE más cercanas a su ubicación actual o a una ubicación específica.

##### A.3.2.2. Por Tipo de Usuario

Para el sistema "App SUBE", se identifica un único tipo principal de usuario: el **Usuario Final del Transporte Público**. Las funcionalidades que le conciernen, con un enfoque particular en la interfaz de usuario (UI), la accesibilidad y la experiencia general, son las siguientes:

- **Usuario Final del Transporte Público:**
    - **Interfaz de Usuario Intuitiva y Consistente:** Para todas las funcionalidades, la aplicación deberá adherirse estrictamente a las guías de diseño de las plataformas: **Material Design para Android** y **Human Interface Guidelines para iOS**. Esto asegura una experiencia de usuario familiar, coherente y fácil de aprender, minimizando la curva de aprendizaje para usuarios con diferentes niveles de habilidad digital.
    - **Accesibilidad Universal:** La interfaz y las funcionalidades deberán ser accesibles para usuarios con diversas capacidades, siguiendo las pautas de accesibilidad de Google (para Material Design) y Apple (para Human Interface Guidelines). Esto incluye soporte para:
        - **Lectores de pantalla (VoiceOver en iOS, TalkBack en Android):** Todos los elementos interactivos, textos e imágenes significativas deberán tener etiquetas y descripciones apropiadas.
        - **Tamaño de fuente dinámico:** El texto deberá ajustarse a las preferencias de tamaño de fuente del sistema del usuario para facilitar la lectura.
        - **Contraste de color adecuado:** Se deberán usar combinaciones de colores que aseguren un contraste suficiente para personas con deficiencias visuales.
        - **Navegación por teclado y controles externos:** Los elementos interactivos deberán ser accesibles y operables mediante teclado o dispositivos de asistencia.
    - **Registro y Gestión de Perfil Simplificado:** El proceso de creación de una cuenta y la vinculación de tarjetas SUBE deberán ser guiados, con pasos claros y **retroalimentación visual y textual inmediata** sobre el éxito o error de cada acción, en línea con los principios de usabilidad de ambas guías de diseño.
    - **Personalización de Notificaciones:** El usuario deberá poder acceder fácilmente a una sección de configuración donde pueda gestionar sus preferencias de notificación (activar/desactivar, establecer umbrales de saldo bajo), con una interfaz clara y autoexplicativa.
    - **Claridad en la Información de Movimientos:** El historial de viajes y recargas deberá presentarse de forma legible y comprensible, utilizando elementos visuales que faciliten la rápida interpretación de los datos. La **disposición de la información** deberá seguir patrones de lectura comunes y jerarquías visuales definidas por las guías de diseño.
    - **Flujo de Recarga Sencillo y Guiado:** El proceso de recarga virtual, incluyendo la interacción NFC, deberá ser intuitivo y ofrecer instrucciones paso a paso claras, con mensajes de error descriptivos en caso de problemas. La **interacción con el hardware NFC** deberá ser guiada visualmente, indicando al usuario cómo y cuándo acercar la tarjeta.
    - **Retroalimentación Consistente:** En todas las interacciones, el sistema deberá proporcionar una **retroalimentación visual y auditiva consistente** para confirmar acciones, indicar estados de carga o señalar errores, lo que contribuye a una experiencia de usuario predecible y tranquilizadora.

#### A.3.3. Requisitos de Rendimiento

El sistema deberá responder a las operaciones críticas, tales como consultas de saldo y recargas, en un tiempo menor a 2 segundos bajo condiciones normales de conectividad. Se espera que el sistema pueda atender al menos 10.000 usuarios simultáneamente sin degradación perceptible del servicio.

#### A.3.4. Restricciones de Diseño

El desarrollo del sistema deberá considerar una arquitectura multiplataforma compatible con Android, iOS y navegadores web modernos. El backend deberá estar diseñado de manera modular para facilitar su mantenimiento y futuras ampliaciones. Se recomienda utilizar tecnologías estándares y ampliamente soportadas.

#### A.3.5. Atributos del Sistema

- **Disponibilidad:** El sistema deberá estar disponible 24/7 con un tiempo de actividad (uptime) mensual superior al 99%.
- **Seguridad:** La información sensible, como contraseñas y datos personales, deberá ser almacenada utilizando técnicas de cifrado seguras (por ejemplo, hashing con salt para contraseñas). Las comunicaciones deberán realizarse bajo protocolo HTTPS.
- **Usabilidad:** La aplicación deberá contar con una interfaz intuitiva, accesible para personas con dificultades visuales, y optimizada para dispositivos móviles.
- **Escalabilidad:** El diseño del sistema debe permitir incorporar nuevas funcionalidades sin afectar el rendimiento general.

#### A.3.6. Otros Requisitos

La aplicación debe estar disponible en todas las provincias de Argentina, considerando las diferentes zonas horarias del país y manejando correctamente los formatos de fecha y hora locales. Esto implica una cuidadosa consideración de la localización y regionalización del sistema.

En términos de certificación, la aplicación deberá cumplir con los estándares de seguridad establecidos por Google Play Store y Apple App Store, así como obtener las certificaciones necesarias para operar como aplicación financiera (por los cobros a través de pasarelas de pago). Esto incluye el cumplimiento de normativas de accesibilidad específicas para cada plataforma.

La documentación del sistema deberá ser completa y accesible, incluyendo una guía de usuario detallada, documentación técnica para desarrolladores y manuales de procedimientos para el equipo de soporte. Esta documentación será fundamental para garantizar el mantenimiento y la evolución del sistema a largo plazo.

### A.4. Apéndices

**Lenguaje de Programación**

Para el desarrollo de la "App SUBE", se establece como una restricción fundamental la utilización de lenguajes de programación y frameworks que soporten la implementación multiplataforma. Esta directriz es esencial para asegurar la compatibilidad del sistema tanto en dispositivos móviles (Android e iOS) como en entornos web modernos, minimizando la duplicación de esfuerzos de desarrollo y facilitando el mantenimiento a largo plazo. 

Por lo tanto, se priorizará el uso de tecnologías que permitan escribir una única base de código que pueda desplegarse eficientemente en todas las plataformas objetivo.

A modo de ejemplo, se recomienda la consideración de lenguajes y entornos como TypeScript con frameworks como React Native o Flutter para el desarrollo de las aplicaciones móviles y web, dada su capacidad para generar código nativo a partir de una única base de código. 

## B. Análisis, Definición de Casos de Uso y Estimación por Puntos de Caso de Uso

### B.1. Casos de Uso Identificados

En base a los requisitos funcionales (definidos previamente en la ERS del primer punto), identificamos los siguientes 5 casos de uso principales:

#### B.1.1. Caso de Uso CU01 – Registro de Usuario

- **Objetivos asociados**:
    - Permitir que nuevos usuarios accedan al sistema App SUBE.
    - Gestionar una cuenta personal asociada a una o varias tarjetas SUBE.
- **Requisitos asociados**:
    - RF01: El sistema deberá permitir crear una cuenta mediante un formulario de registro.
    - RF02: El sistema deberá enviar un correo de confirmación al registrar una cuenta.
- **Descripción**:  
    Caso de uso **concreto**. Permite a un ciudadano crear una cuenta en la App SUBE ingresando sus datos personales. Incluye validaciones y verificación por correo electrónico.
- **Precondición**:
    - El usuario cuenta con acceso a internet.
    - El usuario no posee una cuenta registrada con ese correo electrónico.
- **Secuencia normal**:
    1. El usuario accede a la aplicación y selecciona "Registrarse".
    2. El sistema muestra un formulario de registro.
    3. El usuario completa nombre, DNI, correo electrónico y contraseña.
    4. El sistema valida los datos ingresados.
    5. El sistema crea la cuenta en estado "pendiente de verificación".
    6. El sistema envía un correo de confirmación con enlace de activación.
    7. El usuario accede al correo y confirma su cuenta mediante el enlace.
    8. El sistema activa la cuenta y permite iniciar sesión.
- **Postcondición**:
    - El usuario tiene una cuenta activa y puede iniciar sesión en la aplicación.
- **Excepciones**:
    - E1: El correo ya está registrado → se muestra mensaje de error.
    - E2: Error de validación en los datos ingresados → se solicita corrección.
    - E3: Falla en el envío de correo → se informa y ofrece reintento.
    - E4: El usuario no confirma el correo → la cuenta permanece inactiva.
- **Importancia**: Alta
- **Urgencia**: Alta
- **Comentarios**:
    - El correo de confirmación es esencial para evitar registros fraudulentos.
    - Puede contemplarse en el futuro el registro con autenticación social (Google, Facebook, etc).

#### B.1.2. Caso de Uso CU02 – Asociar Tarjeta SUBE

- **Objetivos asociados**:
    - Permitir al usuario gestionar una o más tarjetas SUBE desde la aplicación.
- **Requisitos asociados**:
    - RF03: El sistema deberá permitir asociar una tarjeta SUBE a una cuenta de usuario.
    - RF04: El sistema deberá verificar la validez de la tarjeta con el sistema central SUBE.
- **Descripción**:  
    Caso de uso **concreto**. Permite al usuario registrado vincular una tarjeta SUBE existente a su cuenta para su gestión.
- **Precondición**:
    - El usuario ha iniciado sesión en la App SUBE.
    - El número de tarjeta SUBE ingresado no está previamente asociado a otro usuario.
- **Secuencia normal**:
    1. El usuario accede a la sección "Mis tarjetas" y selecciona "Asociar nueva tarjeta".
    2. El sistema solicita el número de tarjeta SUBE.
    3. El usuario ingresa el número y confirma.
    4. El sistema valida el formato y verifica con la API del sistema SUBE.
    5. Si es válido, la tarjeta queda asociada a la cuenta del usuario.
    6. El sistema muestra un mensaje de éxito y la tarjeta aparece en el listado del usuario.
- **Postcondición**:
    - La tarjeta queda vinculada a la cuenta del usuario y lista para futuras operaciones.
- **Excepciones**:
    - E1: La tarjeta ya está asociada a otra cuenta → se muestra mensaje de error.
    - E2: El número ingresado es inválido → se solicita corrección.
    - E3: Error en la API del sistema SUBE → se informa que no se pudo validar en ese momento.
- **Importancia**: Alta
- **Urgencia**: Alta
- **Comentarios**:
    - Se podría permitir en el futuro agregar un alias a cada tarjeta para facilitar su identificación.

#### B.1.3. Caso de Uso CU03 – Consultar Saldo
  
- **Objetivos asociados**:
    - Permitir al usuario consultar el saldo actual de sus tarjetas SUBE.
- **Requisitos asociados**:
    - RF05: El sistema deberá permitir la consulta de saldo en tiempo real.
    - RF06: El sistema deberá comunicarse con la API del sistema SUBE para obtener esta información.
- **Descripción**:  
    Caso de uso **concreto**. Permite visualizar el saldo actualizado de una tarjeta asociada.
- **Precondición**:
    - El usuario ha iniciado sesión y tiene al menos una tarjeta asociada.
    - El sistema SUBE está disponible.
- **Secuencia normal**:
    1. El usuario accede al menú de tarjetas asociadas.
    2. Selecciona una tarjeta.
    3. El sistema consulta en tiempo real el saldo mediante API al sistema SUBE.
    4. El sistema muestra el saldo actualizado en pantalla.
- **Postcondición**:
    - El usuario visualiza el saldo actualizado de la tarjeta seleccionada.
- **Excepciones**:
    - E1: No hay conexión a internet → se informa al usuario.
    - E2: El sistema SUBE no responde → se notifica y se invita a reintentar.
- **Importancia**: Alta
- **Urgencia**: Alta
- **Comentarios**:
    - Puede ofrecerse una vista offline con el último saldo consultado si se guarda localmente (mejora futura).

#### B.1.4. Caso de Uso CU04 – Consultar Últimos Movimientos

- **Objetivos asociados**:    
    - Permitir al usuario visualizar el historial de viajes y recargas realizados con su tarjeta SUBE.
- **Requisitos asociados**:
    - RF07: El sistema deberá permitir consultar los movimientos de una tarjeta.
    - RF08: El sistema deberá mostrar fecha, tipo de movimiento, monto, y otros datos relevantes.
- **Descripción**:  
    Caso de uso **concreto**. Muestra al usuario un historial detallado de transacciones (viajes, recargas).
- **Precondición**:
    - El usuario ha iniciado sesión y tiene al menos una tarjeta asociada.
- **Secuencia normal**:
    1. El usuario accede a la sección "Historial de movimientos".
    2. Selecciona una tarjeta de la lista.
    3. El sistema realiza una consulta al sistema SUBE para obtener el historial.
    4. El sistema muestra una lista cronológica de movimientos con detalles por fila (tipo, monto, fecha).
- **Postcondición**:
    - El usuario visualiza correctamente el historial de operaciones de la tarjeta.
- **Excepciones**:
    - E1: El sistema SUBE no responde → se informa al usuario.
    - E2: No hay movimientos disponibles → se muestra mensaje informativo.
- **Importancia**: Media
- **Urgencia**: Alta
- **Comentarios**:
    - Podría agregarse una opción para exportar el historial como PDF o Excel en futuras versiones.

#### B.1.5. Caso de Uso CU05 – Recargar Saldo

- **Objetivos asociados**:    
    - Permitir que el usuario recargue virtualmente su tarjeta SUBE.
- **Requisitos asociados**:
    - RF09: El sistema deberá permitir recargas usando diversos medios de pago.
    - RF10: El sistema deberá integrar pasarelas de pago y soporte NFC para acreditar la carga.
- **Descripción**:  
    Caso de uso **concreto**. Permite seleccionar un medio de pago, realizar la recarga y acreditarla en la tarjeta.
- **Precondición**:
    - El usuario ha iniciado sesión.
    - Tiene al menos una tarjeta asociada.
    - Dispone de conexión a internet.
- **Secuencia normal**:
    1. El usuario accede a la sección "Recargar".
    2. Selecciona una tarjeta.
    3. Ingresa el monto deseado.
    4. Elige el medio de pago (tarjeta, billetera virtual).
    5. El sistema redirige o comunica con la pasarela de pago correspondiente.
    6. El usuario completa los datos de pago y confirma.
    7. El sistema recibe confirmación del pago.
    8. Se muestra mensaje de éxito.
    9. Si el usuario tiene NFC disponible, se le solicita acercar la tarjeta para acreditación.
- **Postcondición**:
    - La tarjeta ha sido recargada y la operación ha sido confirmada.
- **Excepciones**:
    - E1: Pago rechazado por la pasarela → se informa y se ofrece reintento.
    - E2: Error de conexión con la pasarela → se informa y se sugiere volver a intentar.
    - E3: Falla en la acreditación NFC → se sugiere acudir a una terminal SUBE o reintentar.
- **Importancia**: Crítica
- **Urgencia**: Alta
- **Comentarios**:
    - Es uno de los pilares de valor agregado frente a las terminales físicas.
    - Debe contemplar múltiples pasarelas de pago para disponibilidad constante.
### B.2. Actores y Acciones Involucradas (en cada caso de uso)

| Caso de Uso               | Actores Involucrados                                                        | Acciones del Actor                                                                                                                                                                                                                                                                                                                                                        |
| ------------------------- | --------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Registro de Usuario**   | Usuario, Servidor de Aplicación, Servidor de Correo                         | - **Usuario**: Completa formulario con datos personales (nombre, DNI, correo, contraseña).<br>- **Servidor de Aplicación**: Valida datos, registra al usuario en base de datos.<br>- **Servidor de Correo**: Envía mail de confirmación con enlace de activación.                                                                                                         |
| **Asociar Tarjeta SUBE**  | Usuario, Sistema SUBE, Servidor de Aplicación                               | - **Usuario**: Ingresa número de tarjeta SUBE desde su perfil.<br>- **Servidor de Aplicación**: Valida formato del número, verifica si ya está asociada.<br>- **Sistema SUBE**: Confirma autenticidad de la tarjeta y permite asociación.                                                                                                                                 |
| **Consultar Saldo**       | Usuario, Sistema SUBE, Servidor de Aplicación                               | - **Usuario**: Selecciona una tarjeta y solicita ver el saldo actual.<br>- **Servidor de Aplicación**: Envía solicitud de consulta a Sistema SUBE.<br>- **Sistema SUBE**: Devuelve el saldo actualizado de la tarjeta.<br>- **Servidor de Aplicación**: Presenta el resultado al usuario.                                                                                 |
| **Consultar Movimientos** | Usuario, Sistema SUBE, Servidor de Aplicación                               | - **Usuario**: Selecciona una tarjeta y solicita historial.<br>- **Servidor de Aplicación**: Solicita movimientos a Sistema SUBE.<br>- **Sistema SUBE**: Responde con historial (viajes, recargas).<br>- **Servidor de Aplicación**: Muestra los datos en la interfaz.                                                                                                    |
| **Recargar Saldo**        | Usuario, Pasarela de Pago, Sistema SUBE, Servidor de Aplicación, Módulo NFC | - **Usuario**: Elige tarjeta, monto y medio de pago, confirma.<br>- **Servidor de Aplicación**: Inicia transacción, coordina con pasarela de pago.<br>- **Pasarela de Pago**: Procesa el pago (tarjeta, billetera, etc).<br>- **Sistema SUBE**: Acredita saldo.<br>- **Módulo NFC**: En caso de ser requerido, permite validar la carga acercando la tarjeta al teléfono. |
### B.3. Diagramas de Casos de Uso

> **Nota:** Todos los diagramas fueron elaborados utilizando [PlantUML](https://www.plantuml.com). En caso de que las imágenes no se puedan ver con claridad, se adjunta el enlace a la página con la definición completa del diagrama.

#### B.3.1. Diagrama CU01 – Registro de Usuario 
[Link](https://www.plantuml.com/plantuml/uml/RP5FQlCm4CNtEiNWVU3BdzL724b3wBuXtG_In1qgIsP6JbjAJzKZz6AL7GL4suNXpRpV4q_oLXDA6ZflF9qJf0Z2xLC2nq8sSGn6dpdqADW1sXJbcDuy0RwGnewCcNRGd7H0uTW0AXHziuua5tPPEO9z5A69Al8lQDTxjcZvwpECP9vxOyOs65fFqEGPZgUxGmFl1c1GigXvVK-QninuX1qe9BossV-hgGTgMPEWQAPYXZ55XPv4Oq1FMZBpElE8dXrAOJcqGeektjX5pHx2XJFQ8VkfI0ndbkvwcy8lQtvd4rzGIkCSqPoYuD5ImLVcmvZofZ2RRQORvQ_eEVo7zPfU4mMtsPYyACPYCZXOFpYQd4Lnbh-SPN5MFnnp_Kktd1cVlfnUwNg_ggjEyvQ26ph_3G00)
![[diagrama_cu01.png]]
#### B.3.2. Diagrama CU02 – Asociar Tarjeta SUBE
[Link](https://www.plantuml.com/plantuml/uml/RP9DR_8m383l_XKMTwGNokaLGh1fcdP6x6wbXdbh4-Iux6FQhzfjL_xOaYwWxkDGoVRps8sZBAEHMjSsqF3Mq0AgxEuCQr5s9i53V12_9wKMoLdGjJqtZFJ8CRGCqDTmj8aTgOGHKiGIdz1Qed5BkDvSNFKy1sV8Uf0wHJNZQj-88oV7Tv-zbEy1yYd8xnh6KShBa5T0x28xYgbypT5oJnvXfFTi5B_ESVblwDtudN8aHN_yQ5d3m2_wPAZVKYDrihT1Mx8_xEatcrMsuf9_o7tyWkmn3ZOlJTMmQHM34zBJN2Hq7Vlp3sRm1b0k4yVZHRzG-aeymVyud_EJiQyN2qY50gOz4E-QhkQUJ0kfVf6gaDaF0lcooxX1N47UvPGCm0nWoRxEx-WJ)
![[diagrama_cu02.png]]
#### B.3.3. Diagrama CU03 – Consultar Saldo
[Link](https://www.plantuml.com/plantuml/uml/RP7TIaCn3CVlynHXzmEttSaOcyCBBmJXi0S8RPpLddOaENueFfMFu8lP7kiSuaNXd_poyITTgv7Oq4U8V6je6INixmnz47OMSW9z2Eb0GZsIiooTlKH6UcBDFGECEPpiT209UOAasFGFwe8QzuJTxk9ov5KS8SjZy4LvniqX1aSkV7waMbVY0q1rGMaV6ISbRaFU075GTgGblSr9XsXKHoX5tqnipqwBEexiNCY918taderQsUo_MOARcolq755FtBUE-Md7TLOJak_DzK83nV1AHnSBU0Te5y7fT3KQAw_f6PxZSidFniclLb0I3Sn749ABW-UHp1jP_25GxzAQeDey1hzW0R0k6-etVm40)
![[diagrama_cu03.png]]
#### B.3.4. Diagrama CU04 – Consultar Últimos Movimientos
[Link](https://www.plantuml.com/plantuml/uml/RP9DRXin38NtEWNXlO7wPrKOXbkZowABWG_0QXYRZaOoH8uJ8yYfSeHSBDH43iP95WDG-juZ7uNPY6BMlWikq9s29iYyFoYqdCahf-ZadkC9CtQ0NbDkz188y84aTUJSS0UJdVIOEKq01MfzHGsBKeVGx7x_ANmeFY3bCxTMjGI_Je4zUdvzYKLdvvDp9GN6lOsSsBasUN80lP17iUjjYj87nTAYIsVkcA8cUO-o_J6MDXJ8UriACoZc8obMsUn6bYm8cm0EB1QJCNpVV3vs_Vkl5EdM8sQJqVxLjnZx_YRHRBE2wR1DPTAPyKW25yYcoNkypbkwP-Vgyy9qkXxsiw_MC_W9gnKzMevslJOmgs0-08u-z2qDP5x9uWjPLBByH5nv_cfoPPVhOGIMpcriTlcRtW00)
![[diagrama_cu04.png]]
#### B.3.5. Diagrama CU05 – Recargar Saldo
[Link](https://www.plantuml.com/plantuml/uml/PLBDZjCm4BxFKunwWiDARDkze5MrosgvWIgYvJwoXs1mx6Zi01NYgVe8VJ4ynec2UeWodk_7yzb-I9aaJuC7pryoveZY-gyPhHCssSK0wRiB8maDI2P7wVB9Cz9FJd5WWDh3pKkQI5pS82LizGmTAP6m9xICH-ehHuk5qBcKUI3iNjuzArYB6VnmETl9H_pu_aan_IywbX_EHb7Znz4xGyPTpa5fPJq2Q08AlMVSb7Np_ms8Ks93gRG_bR_qf0w9l6tpFxrPSphsR8oBWGGpoJVEX2SSOiWp_NPDV_RSEy61hOlgEr8_yxPhtb6YuKHI2MXYmB4TL6Flrkn78yKma_oR4paiisYi9jcl9P_9Eri4DQ5ke6U7h-9Oi_ZNJNC7Vm3QbU7DpQ76Br-hR_4jtj_phyp17WvG6WtOLi05uoVB5TasP7U5x1golqBs3RdxVnlG-sfke6zbMMZQsGqqKDC3F1IflkE_)
![[diagrama_cu05.png]]

### B.4. Estimación por Puntos de Caso de Uso (UCP)

| Complejidad | N° Transacciones | Puntos |
| ----------- | ---------------- | ------ |
| Simple      | <=3              | 5      |
| Promedio    | 4 a 7            | 10     |
| Complejo    | > 7              | 15     |
#### Paso 1: Calcular Puntos de Caso de Uso No Ajustados (UUCP)
**1.1 Clasificación y peso de los Casos de Uso**

| Caso de Uso                  | N° Transacciones | Clasificación | Peso |
| ---------------------------- | ---------------- | ------------- | ---- |
| CU01 – Registro de Usuario   | 8                | Complejo      | 15   |
| CU02 – Asociar Tarjeta SUBE  | 6                | Promedio      | 10   |
| CU03 – Consultar Saldo       | 4                | Promedio      | 10   |
| CU04 – Consultar Movimientos | 5                | Promedio      | 10   |
| CU05 – Recargar Saldo        | 9                | Complejo      | 15   |
**Total UUCW = 15 + 10 + 10 + 10 + 15 = 60**

 **1.2 Clasificación y peso de los Actores**
 
|Actor|Tipo de Interfaz|Clasificación|Peso|
|---|---|---|---|
|Usuario|GUI|Complejo|3|
|Servidor de Aplicación|API o sistema interno|Promedio|2|
|Servidor de Correo|Sistema externo (estándar)|Simple|1|
|Sistema SUBE|API externa|Promedio|2|
|Pasarela de Pago|API externa|Promedio|2|
|Módulo NFC|Dispositivo físico/sensor|Promedio|2|
**Total UAW = 3 + 2 + 1 + 2 + 2 + 2 = 12**

**1.3 Puntos de Caso de Uso No Ajustados (UUCP)**
UUCP = UUCW+UAW
UUCP = 60+12
UUCP = 72

#### Paso 2: Ajuste por Complejidad Técnica (TCF)

13 factores técnicos, valorados de 0 a 5. Valoramos razonablemente según el sistema SUBE y una app móvil/financiera moderna:

|#|Factor Técnico|Peso|Valoración|Impacto|
|---|---|---|---|---|
|1|Sistema distribuido|2|4|8|
|2|Tiempo de respuesta / rendimiento|1|4|4|
|3|Alta eficiencia requerida|1|3|3|
|4|Procesamiento interno complejo|1|2|2|
|5|Reusabilidad del código|1|3|3|
|6|Instalaciones fáciles|0.5|2|1|
|7|Portabilidad|2|4|8|
|8|Uso intensivo de la interfaz|1|5|5|
|9|Componentes de entrada complejos|0.5|2|1|
|10|Uso de archivos internos|1|1|1|
|11|Seguridad|1|5|5|
|12|Acceso directo de terceros a interfaces|1|3|3|
|13|Capacidades especiales (NFC)|1|4|4|
**TFactor = Suma Impacto = 58**
TCF = 0.6+(0.01×58)
TCF = 0.6+0.58
TCF = 1.18

#### Paso 3: Ajuste por Complejidad Ambiental (EF)
8 factores ambientales, valorados de 0 a 5. Evaluamos según un equipo universitario con experiencia moderada y proyecto bien definido.

| #   | Factor Ambiental                               | Peso | Valoración | Impacto |
| --- | ---------------------------------------------- | ---- | ---------- | ------- |
| 1   | Familiaridad con el proceso de desarrollo      | 1.5  | 3          | 4.5     |
| 2   | Experiencia con aplicación similar             | 0.5  | 3          | 1.5     |
| 3   | Experiencia con la plataforma                  | 1    | 3          | 3       |
| 4   | Motivación del equipo                          | 1    | 5          | 5       |
| 5   | Estabilidad de requisitos                      | 2    | 4          | 8       |
| 6   | Parte del equipo está capacitada               | 1    | 4          | 4       |
| 7   | Dificultad técnica del lenguaje                | 0.5  | 3          | 1.5     |
| 8   | Disponibilidad de entornos de prueba adecuados | 1    | 2          | 2       |
**EFactor = 4.5 + 1.5 + 3 + 5 + 8 + 4 + 1.5 + 2 = 29.5**
EF = 1.4+(−0.03×EFactor)
EF = 1.4−0.03×29.5
EF = 1.4−0.885
EF = 0.515​

#### Paso 4: Cálculo Final de Puntos de Caso de Uso Ajustados (UCP)
UCP = UUCP×TCF×EF
UCP = 72×1.18×0.515
UCP = 43.74

## C. Historias de Usuario

### C.1. HU 1: Registro de Usuario

**Título:** Registro de Usuario  
**Descripción:**  
i. Como un/a nuevo/a usuario/a,  
ii. Quiero registrarme en la aplicación con mis datos personales,  
iii. Para poder acceder a las funcionalidades de gestión de tarjetas SUBE.

**Criterios de Aceptación:**

1. El sistema debe permitir ingresar nombre completo, DNI, correo electrónico y contraseña.
2. El sistema debe validar que los campos estén completos y con formato correcto.
3. El sistema debe enviar un correo de confirmación con enlace para activar la cuenta.
4. El sistema debe activar la cuenta una vez confirmada por el usuario.

**Notas adicionales:**

- La contraseña debe tener al menos 8 caracteres, incluir una mayúscula, una minúscula y un número.
- No se puede registrar más de una cuenta con el mismo DNI o correo.
- La activación es obligatoria para poder iniciar sesión.

### C.2. HU 2: Asociar Tarjeta SUBE

**Título:** Asociar Tarjeta SUBE  
**Descripción:**  
i. Como un/a usuario/a registrado/a,  
ii. Quiero asociar una tarjeta SUBE a mi cuenta,  
iii. Para poder visualizar su saldo y movimientos desde la app.

**Criterios de Aceptación:**

1. El sistema debe permitir ingresar el número completo de tarjeta SUBE (16 dígitos).
2. El sistema debe validar el formato del número y verificar que no esté asociada a otra cuenta.
3. El sistema debe confirmar la asociación con un mensaje visual y/o correo electrónico.

**Notas adicionales:**

- Un usuario puede asociar hasta 5 tarjetas distintas.
- Las tarjetas asociadas se muestran en una lista de gestión personal.
- El sistema debe consultar la API oficial del sistema SUBE para validar la tarjeta.

### C.3. HU 3: Consultar Saldo

**Título:** Consultar Saldo  
**Descripción:**  
i. Como un/a usuario/a registrado/a con tarjeta asociada,  
ii. Quiero ver el saldo disponible en mi tarjeta SUBE,  
iii. Para saber si necesito realizar una recarga antes de viajar.

**Criterios de Aceptación:**

1. El sistema debe mostrar el saldo actualizado en tiempo real de cada tarjeta asociada.
2. El sistema debe indicar fecha y hora de la última actualización.
3. El saldo debe visualizarse en menos de 2 segundos bajo condiciones normales de conectividad.

**Notas adicionales:**

- El sistema obtiene el saldo en tiempo real a través de la API del sistema SUBE.
- En caso de múltiples tarjetas, todas deben estar visibles en la interfaz.
- Puede ofrecerse almacenamiento temporal del último saldo en cache (mejora futura).

### C.4. HU 4: Consultar Últimos Movimientos

**Título:** Consultar Últimos Movimientos  
**Descripción:**  
i. Como un/a usuario/a registrado/a,  
ii. Quiero ver los movimientos (viajes y recargas) de mi tarjeta SUBE,  
iii. Para poder llevar un control de mis gastos de transporte.

**Criterios de Aceptación:**

1. El sistema debe mostrar al menos los últimos 20 movimientos.
2. Debe incluir tipo de operación, fecha, monto y línea de transporte (si aplica).
3. El usuario debe poder filtrar los movimientos por tipo y rango de fechas.

**Notas adicionales:**

- El historial puede tener una demora de hasta 24 horas en reflejar nuevas operaciones.
- El sistema consume esta información desde la API del sistema SUBE.
- Puede incluirse opción para exportar a PDF o Excel en versiones futuras.

### C.5. HU 5: Recargar Saldo

**Título:** Recargar Saldo  
**Descripción:**  
i. Como un/a usuario/a registrado/a con tarjeta asociada,  
ii. Quiero realizar una recarga virtual utilizando medios de pago digitales,  
iii. Para aumentar el saldo de mi tarjeta sin acudir a una terminal física.

**Criterios de Aceptación:**

1. El sistema debe permitir seleccionar tarjeta, ingresar monto, elegir medio de pago y confirmar.
2. Debe validarse el monto mínimo y máximo permitido.
3. El usuario debe recibir confirmación visual y vía correo electrónico.
4. El saldo debe estar disponible para acreditación en las siguientes 24 horas.

**Notas adicionales:**

- La app se integra con pasarelas de pago como MercadoPago, Ualá y tarjetas bancarias.
- El sistema debe cumplir con los estándares PCI-DSS para transacciones seguras.
- Si el dispositivo cuenta con NFC, debe ofrecer la validación inmediata desde el celular.

## D. Estimación de Esfuerzo

### D.1. Estimación de Esfuerzo con Planning Poker

**HU1 – Registro de Usuario**  
Cartas elegidas: Ariel (2), Nahuel (2), Dante (2)  
Todos estuvieron de acuerdo en usar esta historia como referencia base por su complejidad intermedia (validación de datos, almacenamiento, correo de confirmación).  
**Resultado:** 2 puntos

**HU2 – Asociar Tarjeta SUBE**  
Cartas elegidas: Ariel (2), Nahuel (1), Dante (2)  
Aunque Nahuel consideró que era más simple que el registro, durante la discusión se acordó que la integración con el sistema SUBE justifica un esfuerzo similar.  
**Resultado:** 2 puntos

**HU3 – Consultar Saldo**  
Cartas elegidas: Ariel (1), Nahuel (1), Dante (1)  
Todos coincidieron en que es la historia más simple. Sólo implica mostrar información consumida desde una API.  
**Resultado:** 1 punto

**HU4 – Consultar Últimos Movimientos**  
Cartas elegidas: Ariel (3), Nahuel (2), Dante (3)  
La discusión giró en torno a la mayor carga lógica de esta historia (paginado, filtros, presentación estructurada). Se decidió tomar la opción más alta.  
**Resultado:** 3 puntos

**HU5 – Recargar Saldo**  
Cartas elegidas: Ariel (5), Nahuel (5), Dante (8)  
Aunque Dante propuso 8 por la complejidad del NFC y la seguridad de pagos, Ariel y Nahuel sugirieron que 5 es más apropiado para esta release inicial. Se optó por mantener el valor más frecuente para evitar sobreestimar.
**Resultado:** 5 puntos

### D.2. Tabla Final - Estimación de Esfuerzo

| Historia de Usuario                 | Esfuerzo Estimado | Complejidad Relativa                                            |
| ----------------------------------- | ----------------- | --------------------------------------------------------------- |
| HU1 – Registro de Usuario           | **2**             | Historia base. Validaciones + correo.                           |
| HU2 – Asociar Tarjeta SUBE          | **2**             | Requiere validación de API externa.                             |
| HU3 – Consultar Saldo               | **1**             | Interacción simple, sin lógica compleja.                        |
| HU4 – Consultar Últimos Movimientos | **3**             | Listado, filtros, interacción más extensa con API.              |
| HU5 – Recargar Saldo                | **5**             | Multipasos, integración con pagos, validaciones, NFC. Compleja. |

## E. Diagramas de Pert y Gantt

### E.1. Principales Eventos del Proyecto

1. Relevamiento de requisitos
2. Diseño de arquitectura del sistema
3. Diseño de interfaz de usuario (UX)
4. Desarrollo: Registro de usuario
5. Desarrollo: Asociación de tarjeta
6. Desarrollo: Consulta de saldo y movimientos
7. Desarrollo: Recarga de saldo
8. Desarrollo: Integración de notificaciones
9. Pruebas funcionales y de seguridad
10. Despliegue en entorno productivo

### E.2. Tabla de Tareas

| N°  | Tarea                                       | Duración (días) | Predecesores |
| --- | ------------------------------------------- | --------------- | ------------ |
| 1   | Relevamiento de requisitos                  | 3               | -            |
| 2   | Diseño de arquitectura del sistema          | 2               | 1            |
| 3   | Diseño de interfaz de usuario (UX)          | 3               | 1            |
| 4   | Desarrollo: Registro de usuario             | 4               | 2, 3         |
| 5   | Desarrollo: Asociación de tarjeta           | 4               | 2, 3         |
| 6   | Desarrollo: Consulta de saldo y movimientos | 4               | 2, 3         |
| 7   | Desarrollo: Recarga de saldo                | 5               | 5, 6         |
| 8   | Desarrollo: Integración de notificaciones   | 2               | 7            |
| 9   | Pruebas funcionales y de seguridad          | 5               | 7, 8         |
| 10  | Despliegue en entorno productivo            | 1               | 9            |

### E.3. Diagrama Gantt
![[diagrama_gantt.png]]

### E.4. Diagrama Pert
![[diagrama_pert.drawio.png]]