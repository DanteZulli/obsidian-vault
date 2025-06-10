```table-of-contents
```
## Definición de Ideas o Requisitos según el estándar IEEE 830

### 1. Introducción

#### 1.1 Propósito

El presente documento tiene como finalidad especificar de manera clara, completa y estructurada los requisitos del sistema “App SUBE”, una aplicación destinada a la gestión virtual de tarjetas SUBE utilizadas en el transporte público en Argentina.

Su contenido está dirigido al futuro equipo de desarrollo de software, incluyendo analistas funcionales, diseñadores, programadores, testers y personal de aseguramiento de calidad (QA), con el objetivo de facilitar el diseño, desarrollo, validación y mantenimiento del sistema.  

#### 1.2 Ámbito del Sistema

El sistema a desarrollar se denominará “App SUBE” y consistirá en una aplicación móvil y web destinada a la gestión digital de tarjetas SUBE, utilizadas en el transporte público de Argentina. 

El sistema permitirá a los usuarios registrarse, asociar una o más tarjetas SUBE a su cuenta, consultar el saldo actualizado, visualizar movimientos recientes (viajes y recargas), realizar recargas virtuales mediante medios de pago digitales, recibir notificaciones relevantes y administrar múltiples tarjetas desde un mismo perfil.

Como complemento, incluirá un mapa interactivo con terminales físicas disponibles para validación presencial, pensado para usuarios cuyos dispositivos no cuenten con tecnología NFC para efectuar las recargas de manera virtual.

Quedan fuera del alcance de esta versión funcionalidades como el pago directo del transporte, integración con recorridos o localización en tiempo real, edición de datos personales desde la aplicación o transferencia de saldo entre tarjetas, entre otras funcionalidades que podrán ser consideradas en versiones futuras del sistema.

El objetivo principal es brindar una herramienta ágil y centralizada para la gestión de la tarjeta SUBE, facilitando el acceso digital a sus funcionalidades y reduciendo la necesidad de acudir a terminales físicas, sin descuidar a los usuarios que requieran soporte adicional.

#### 1.3. Definiciones, Acrónimos y Abreviaturas

En esta subsección se definirán todos los términos, acrónimos y abreviaturas utilizadas en la ERS. Esta sección se completará y actualizará a medida que se incorporen nuevos términos específicos al sistema o al dominio.

- **SUBE / Tarjeta SUBE / Sistema Único de Boleto Electrónico:** Sistema de pago electrónico implementado en Argentina en 2009 para el transporte público. Funciona en colectivos, trenes, subtes, metrotranvías, trolebuses y lanchas, y es utilizado por millones de personas en diversas localidades del país.
  
- **NFC (Near Field Communication):** Tecnología de comunicación inalámbrica de corto alcance que permite el intercambio de datos entre dispositivos cuando están muy cerca el uno del otro. En el contexto de la App SUBE, se refiere a la funcionalidad de dispositivos móviles (teléfonos) que permite validar y acreditar cargas directamente en la tarjeta SUBE sin necesidad de una terminal física.

### 2. Descripción General

#### 2.1 Perspectiva del Producto

La "App SUBE" se concibe como un complemento al ecosistema existente del Sistema Único de Boleto Electrónico (SUBE) en Argentina. No es un sistema independiente, sino que depende directamente de las APIs expuestas por los propietarios originales del sistema SUBE. A través de estas APIs, la aplicación podrá consultar y registrar información esencial para el usuario, como los viajes realizados, las cargas efectuadas y otros movimientos de la tarjeta.

El sistema también interactuará con las distintas plataformas de verificación de cobros y pagos, integrándose con servicios de terceros como procesadores de tarjetas de crédito, débito y billeteras virtuales. Esto permitirá la realización de recargas virtuales y la gestión de transacciones desde la aplicación misma.

La App SUBE busca facilitar a los usuarios el uso, la recarga, la gestión y el seguimiento de sus tarjetas, optimizando la experiencia del usuario con funcionalidades innovadoras como la recarga virtual a través de tecnología NFC, lo cual representa una evolución y una extensión de las capacidades del sistema SUBE tradicional.

#### 2.2. Funciones del Producto

La "App SUBE" proporcionará a los usuarios un conjunto de funcionalidades esenciales para la gestión digital de sus tarjetas SUBE. A grandes rasgos, el sistema permitirá a los usuarios la gestión de cuentas y tarjetas, facilitando el registro y la asociación de múltiples tarjetas SUBE a un único perfil para una administración centralizada. 

También ofrecerá la capacidad de consultar el saldo actualizado de las tarjetas y visualizar un historial de movimientos detallado que incluya viajes y recargas. 

Además, el sistema soportará la realización de recargas de saldo mediante diversos medios de pago digitales y enviará alertas y notificaciones relevantes al usuario relacionadas con el estado de sus tarjetas y transacciones. Finalmente, incluirá una funcionalidad para la localización de terminales físicas de validación de carga.

#### 2.3. Características de los Usuarios

La aplicación "App SUBE" está pensada para un público amplio y diverso, compuesto principalmente por ciudadanos argentinos que utilizan el transporte público y cuentan con una tarjeta SUBE. Se parte del supuesto de que los usuarios ya están familiarizados con el uso tradicional de la tarjeta en su formato físico.

Se asume que el usuario promedio tiene conocimientos básicos o intermedios en el uso de smartphones y aplicaciones móviles: puede navegar interfaces simples y entender mensajes o notificaciones, aunque no necesariamente posee experiencia técnica avanzada.

Aunque la aplicación está diseñada para ser intuitiva y fácil de usar, se reconoce que algunos usuarios pueden no estar habituados a ciertas funciones digitales, como la recarga virtual o la validación por NFC. Por eso, la interfaz y los mensajes deben ser claros y guiados, minimizando cualquier posible confusión.

#### 2.4 Restricciones

El desarrollo de la "App SUBE" estará condicionado por diversas restricciones que afectarán tanto el diseño como la implementación del sistema.

Una de las limitaciones principales es la dependencia de las APIs proporcionadas por los responsables del sistema SUBE. Esto significa que la funcionalidad de la aplicación estará sujeta a las capacidades, limitaciones y criterios de integración definidos por dichas interfaces.

Además, la arquitectura del sistema deberá ser multiplataforma, asegurando compatibilidad y buen rendimiento en dispositivos Android e iOS, así como en navegadores web modernos. Esto requiere el uso de tecnologías y prácticas de desarrollo que permitan mantener una experiencia de usuario coherente y eficiente en distintos entornos.

Otras restricciones relevantes incluyen el cumplimiento estricto de las normativas vigentes en Argentina en materia de seguridad y privacidad de datos, especialmente en lo que respecta al tratamiento de información personal y datos sensibles de carácter transaccional.

El sistema también deberá integrarse con múltiples pasarelas de pago y servicios financieros digitales, lo cual impone requisitos técnicos específicos en cuanto a protocolos de comunicación y compatibilidad con terceros.

Por otro lado, las capacidades del hardware móvil, particularmente la disponibilidad de tecnología NFC para funciones como la recarga virtual, representan una limitación funcional a considerar, dado que no todos los dispositivos soportan esta característica.

Finalmente, las políticas operativas y comerciales establecidas dentro del ecosistema SUBE pueden imponer restricciones adicionales sobre los flujos de trabajo, procesos o decisiones funcionales que la aplicación debe respetar.

#### 2.5. Suposiciones y Dependencias

El funcionamiento y la evolución de la "App SUBE" están sujetos a una serie de suposiciones y dependen de factores externos que, de modificarse, podrían impactar directamente los requisitos del sistema. 

Un pilar fundamental es la suposición de la disponibilidad y estabilidad de las APIs provistas por el sistema SUBE original; cualquier interrupción o cambio en estas interfaces requerirá actualizaciones y adaptaciones en la aplicación, ya que esta opera como un complemento directo a dicho servicio. 

Asimismo, se asume una continuidad en la adopción y funcionalidad de la tecnología NFC en los dispositivos móviles; una modificación o migración significativa en este estándar de comunicación implicaría la necesidad de revisar y actualizar los mecanismos de recarga virtual implementados en la aplicación.

En lo que respecta a la normativa, se presupone que no habrá cambios regulatorios que afecten la naturaleza fundamental de la tarjeta SUBE y su funcionamiento como medio de pago para el transporte público, dado que la aplicación se enfoca meramente en la gestión y transformación digital del uso de la tarjeta existente. 

Las actualizaciones de los sistemas operativos Android e iOS no deberían generar dependencias críticas imprevistas, siempre y cuando se mantengan las herramientas y entornos de desarrollo actualizados para asegurar la compatibilidad. 

Finalmente, se asume que los usuarios dispondrán de una conexión a internet estable al momento de realizar operaciones críticas como recargas o consultas de saldo en tiempo real. Sin embargo, para la visualización de datos históricos de viajes o movimientos, una conexión inicial para sincronizar la información será suficiente, permitiendo un acceso posterior sin conectividad constante para la información ya descargada.

#### 2.6 Requisitos Futuros

Aunque la versión inicial de la "App SUBE" se enfoca en la gestión centralizada y digital de las tarjetas SUBE, se han identificado diversas funcionalidades que, si bien quedan fuera del alcance de esta entrega, se contemplan para ser implementadas en futuras iteraciones del sistema. 

Entre los requisitos proyectados para próximas versiones se incluye la posibilidad de realizar el pago directo del transporte público a través de la aplicación. También se considera la integración con servicios de ruteo y localización en tiempo real, que permitiría a los usuarios planificar sus viajes y seguir el transporte en vivo.

Adicionalmente, se prevé la inclusión de funcionalidades para la edición de datos personales directamente desde la aplicación, ofreciendo mayor autonomía al usuario en la gestión de su perfil. 

Otra mejora planificada es la capacidad de transferir saldo entre diferentes tarjetas SUBE asociadas a la misma cuenta, lo que brindaría mayor flexibilidad en la administración familiar o personal de los recursos. 

Estas funcionalidades futuras serán evaluadas y priorizadas en función de la evolución de las necesidades de los usuarios y las capacidades del ecosistema SUBE.
### 3. Requisitos Específicos

#### 3.2 Funciones

El sistema permitirá a los usuarios registrarse ingresando información personal como nombre, DNI, correo electrónico y una contraseña segura. Una vez registrado, el usuario podrá asociar una o más tarjetas SUBE a su cuenta, ingresando el número correspondiente a cada una. A través de la interfaz, el usuario podrá consultar el saldo disponible en tiempo real de cada tarjeta asociada y acceder al historial de movimientos, incluyendo viajes realizados y recargas efectuadas.

El sistema también habilitará la posibilidad de realizar recargas virtuales mediante distintos medios de pago digitales (como tarjetas de crédito, débito o billeteras virtuales). Además, se enviarán notificaciones automáticas al usuario ante eventos como la confirmación de recargas, asociación de nuevas tarjetas o cuando el saldo sea inferior a un umbral configurable.

La aplicación permitirá gestionar múltiples tarjetas desde una misma cuenta, facilitando el control de tarjetas de familiares u otras personas autorizadas por el usuario.

#### 3.3 Requisitos de Rendimiento

El sistema deberá responder a las operaciones críticas, tales como consultas de saldo y recargas, en un tiempo menor a 2 segundos bajo condiciones normales de conectividad. Se espera que el sistema pueda atender al menos 10.000 usuarios simultáneamente sin degradación perceptible del servicio.

#### 3.4 Restricciones de Diseño

El desarrollo del sistema deberá considerar una arquitectura multiplataforma compatible con Android, iOS y navegadores web modernos. El backend deberá estar diseñado de manera modular para facilitar su mantenimiento y futuras ampliaciones. Se recomienda utilizar tecnologías estándares y ampliamente soportadas.

#### 3.5 Atributos del Sistema

- **Disponibilidad:** El sistema deberá estar disponible 24/7 con un tiempo de actividad (uptime) mensual superior al 99%.
- **Seguridad:** La información sensible, como contraseñas y datos personales, deberá ser almacenada utilizando técnicas de cifrado seguras (por ejemplo, hashing con salt para contraseñas). Las comunicaciones deberán realizarse bajo protocolo HTTPS.
- **Usabilidad:** La aplicación deberá contar con una interfaz intuitiva, accesible para personas con dificultades visuales, y optimizada para dispositivos móviles.
- **Escalabilidad:** El diseño del sistema debe permitir incorporar nuevas funcionalidades sin afectar el rendimiento general.

### 4. Apéndices

#### 4.1. Restricción de Lenguaje de Programación

Para el desarrollo de la "App SUBE", se establece como una restricción fundamental la utilización de lenguajes de programación y frameworks que soporten la implementación multiplataforma. Esta directriz es esencial para asegurar la compatibilidad del sistema tanto en dispositivos móviles (Android e iOS) como en entornos web modernos, minimizando la duplicación de esfuerzos de desarrollo y facilitando el mantenimiento a largo plazo. 

Por lo tanto, se priorizará el uso de tecnologías que permitan escribir una única base de código que pueda desplegarse eficientemente en todas las plataformas objetivo.

A modo de ejemplo, se recomienda la consideración de lenguajes y entornos como TypeScript con frameworks como React Native o Flutter para el desarrollo de las aplicaciones móviles y web, dada su capacidad para generar código nativo a partir de una única base de código. 
