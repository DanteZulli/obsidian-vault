Este documento es el resumen aplicado de todo lo aprendido anteriormente en las prácticas de Docker y Kubernetes. El objetivo es poner en práctica esos conocimientos para construir una base sólida que sirva como punto de partida para pipelines de CI/CD reales en proyectos Java/Node/PHP, etc.

> **Nota Importante**: Acá se documenta en particular el ejemplo de API REST Spring Boot (JAR), ya que es el que está configurado y funcionando en el repo PS-DEVOPS con su pipeline de CI y sus Releases (CD). De todas formas, lo explicado acá es aplicable a todos los demás ejemplos de ese repositorio (e inclusive estos están documentados también).

> **FE DE ERRATAS:**  
> **Aclaración 1**: No olvidar que este ejemplo define un esquema, que no tiene por qué ser inmutable. Los distintos pasos detallados a continuación pueden y deberían ser modificados y readaptados a cada aplicación.  
> **Aclaración 2**: Es posible que alguno de los ejemplos secundarios no esté actualizado con los últimos métodos o mejoras aplicadas. Como son varios y constantemente se les hacen cambios, puede haber alguna diferencia. En caso de duda, **priorizá siempre la metodología aplicada en el ejemplo principal (Spring Boot)**, que es el más completo y mantenido.  
> **Aclaración 3**: Si necesitás una definición más exhaustiva sobre algún tema puntual, fijate si está documentado en otra parte de estas guías o consultá la documentación oficial.

# Diagrama del Proceso Completo

![[diagrama_general.png]]

Este diagrama representa (a gran escala) el flujo general de integración y entrega continua (CI/CD) definido en esta documentación. Abarca todo el proceso desde el push del código fuente a Azure Repos, pasando por la ejecución del pipeline CI en Azure Pipelines, la publicación de artefactos e imágenes Docker, hasta el despliegue de la aplicación en un clúster Kubernetes on-premise, dividido en distintos entornos (Pre-Testing, Testing y Producción).  
A lo largo de esta documentación, lo vamos a ir ampliando.

# Integración Continua (CI) con Azure Pipelines
![[pipeline_ci.png]]

En esta primera parte se detalla exclusivamente el proceso de **Integración Continua (CI)** para una API REST hecha con **Spring Boot**, que se empaqueta como un archivo JAR. Se automatiza todo el ciclo desde la compilación hasta el despliegue en un entorno de pruebas.

## Qué se busca lograr con este Pipeline?

- Automatizar la compilación, testeo y validación del código.
- Construir una imagen Docker con la aplicación.
- Subir la imagen a un registro privado.
- Desplegar automáticamente en un entorno de pre-testing.
- Establecer una estructura base reutilizable y fácil de escalar.

## Desglose del Pipeline
###  Disparadores

```yaml
trigger:
  branches:
    include:
      - main
      - feature/*
      - bugfix/*
  paths:
    include:
      - spring-boot/rest-jar/*
```

En este ejemplo, el pipeline se activa automáticamente cuando hay cambios en los branches indicados (main, feature y bugfix) y dentro del directorio `spring-boot/rest-jar/`. De esa forma, solo se dispara si los cambios son relevantes para esta parte del proyecto.

### Configuración inicial

```yaml
pool:
  name: 'agente-devops-prueba'

variables:
  imageName: 'spring-boot-jar-app'
  imageTag: '$(Build.BuildId)'
  containerRegistry: 'svrpruebas:5000'
  pretestingEnv: 'pre-testing-spring-jar'
  workingDirectory: 'spring-boot/rest-jar'

```

Se define el agente (previamente configurado) donde va a correr el pipeline y algunas variables que se usan en varias etapas: nombre de la imagen, tag, URL del registry privado, nombre del contenedor y directorio de trabajo. Estas variables permiten mantener el código limpio y fácil de modificar, ya que si se necesita cambiar algo, solo hay que hacerlo una vez.
Son una buena práctica para evitar errores y facilitar el mantenimiento del pipeline.

### Etapa 1: Compilación y Control de Calidad (BuildAndQuality)

La etapa de compilación y control de calidad está destinada a asegurar la calidad del entregable, antes incluso de que este sea integrado en el repositorio, o desplegado en un entorno de pruebas. 
En este caso, se divide en 4 pasos:

- **Instalación de JDK**: Se descarga e instala manualmente JDK 17 (sin depender del sistema), lo que asegura que siempre se use la misma versión.
- **Compilación y pruebas unitarias**: Se compila el código y se ejecutan las pruebas unitarias (Por ejemplo, JUnit o Mockito), que validan cada componente por separado: controllers, services, repositorios, etc.
- **Pruebas de integración**: Simulando un escenario más realista, donde varios componentes trabajan juntos (por ejemplo, la app conectándose a una base de datos o a otro servicio).
- **Análisis de calidad del código**: Se deja como placeholder la integración con **SonarQube**, que permitiría detectar vulnerabilidades, duplicación de código, code smells, etc.

### Etapa 2: Construcción y Publicación de Imagen Docker (BuildAndPublishDocker)

Esta etapa se encarga de construir la imagen docker, según la definición en su respectivo `.Dockerfile` y subirla a la registry privada definida en cuestión.

> NOTA: En este caso, la registry NO tiene seguridad alguna, ni usuario, ni contraseña, ni certificados, ya que es un entorno de pruebas/prácticas destinado solo para su uso en esta documentación.
> Para un entorno productivo se debería de configurar una registry segura, como se encuentra en su documentación pertinente `Gestión de un Registry Privado en Docker` con su respectivo login y certificados.

En este caso, se divide en 2 pasos:

- **Build de la imagen**: Se construye la imagen Docker desde el código fuente y se etiqueta con el número de build y también como `latest`.
- **Push al Registry**: La imagen se sube al registro privado definido en la variable `containerRegistry`. Esto la deja lista para ser desplegada en cualquier entorno que consuma imágenes desde ahí.

### Etapa 3: Despliegue en entorno de pre-testing (Deploy)

El entorno de **pre-testing** —también llamado `desa`— es una instancia de pruebas **automática e intermedia**, pensada para validar cambios luego del build, pero **antes** de pasar a `testing` o `producción`. Este entorno simula condiciones reales y ayuda a detectar errores temprano, sin interferir con el resto del ciclo de vida del software. Ideal para **ajustes finos, validaciones rápidas y verificación de despliegue**.

Esta etapa despliega automáticamente una nueva versión de la aplicación, y la lógica de despliegue está organizada en los siguientes pasos:

- **Limpiar recursos existentes**: Se eliminan los recursos previos en el namespace `desa` para evitar conflictos. Esto incluye la eliminación del deployment y el servicio asociados a la aplicación. Se utiliza el comando `kubectl delete` con la opción `--ignore-not-found` para asegurarse de que no se generen errores si los recursos no existen.
- **Aplicar manifiestos y verificar despliegue**: Se actualizan los manifiestos de Kubernetes (ubicados en la carpeta k8s) con la nueva imagen Docker generada en el paso anterior y luego se aplican los manifiestos utilizando `kubectl apply`. Se verifica que el pod esté en estado `Running` y que solo haya un pod activo, asegurando que el despliegue sea exitoso.
- **Pruebas básicas post-despliegue**: Se realizan pruebas básicas para validar el correcto funcionamiento de la aplicación en el entorno de pre-testing. Esto incluye obtener la IP del nodo donde corre el pod y realizar una llamada a la API para verificar que responde correctamente. Se deja un placeholder para agregar pruebas más exhaustivas en el futuro.
- **Estado final del despliegue**: Se muestra el estado final del despliegue, incluyendo la URL de acceso a la aplicación en el entorno de pre-testing. Esto permite confirmar que la aplicación está correctamente desplegada y accesible.

> Es la etapa más compleja, detallada y es la que merece más atención/comprensión, ya que tiene que ser adaptada a cada caso y entorno.
> Para que este proceso se simplifique, se podrían agregar complementos a k8s, como Kustomize o Helm, que están destinados a facilitar estas situaciones y resolver este tipo de problemas. En esta documentación NO se encuentra cubierto el uso de ningún componente externo o agregado más allá de lo que trae por defecto la instalación de Kubernetes.


### Anexo: Carpeta k8s
La carpeta `k8s` contiene los manifiestos de Kubernetes necesarios para desplegar la aplicación. Estos manifiestos definen los recursos que se crearán en el clúster de Kubernetes, como el deployment y el servicio.
No poseen un formato específico, ya que cada proyecto puede requerir configuraciones diferentes. Se recomienda revisar la documentación de Kubernetes para personalizar según las necesidades del proyecto.

### Anexo: Variables de entorno como secretos en Kubernetes
En este ejemplo, los `deployments.yaml` utilizan variables de entorno como secretos en Kubernetes. Esto permite mantener la configuración sensible (como credenciales de bases de datos o API keys) fuera del código fuente y del repositorio de Azure. 
Para ello, se utilizan los secretos de Kubernetes, que permiten almacenar y gestionar información sensible de forma segura. 
En este caso, se hace referencia a un secreto llamado `spring-boot-pretesting-secrets`, que debe ser creado previamente en el clúster de Kubernetes en cada uno de los namespaces. Este secreto contiene las variables de entorno `DB_USER`, `DB_PASSWORD`, que serán inyectadas en el contenedor de la aplicación al momento del despliegue.

Manteniendo esta configuración, no solo se asegura la seguridad de la información sensible, sino que también se facilita la gestión de configuraciones específicas para cada entorno (desarrollo, testing, producción, etc.) sin necesidad de modificar el código fuente ni de reconstruir la imagen Docker al momento de hacer algún cambio.

Para este ejemplo, se hizo lo siguiente:

1. Se creó un secreto en el clúster de Kubernetes (para los tres namespaces) con el siguiente comando:

```bash
kubectl create secret generic spring-boot-pretesting-secrets \
  --from-literal=DB_USERNAME=usuarioEjemplo \
  --from-literal=DB_PASSWORD=contraseñaSegura123 \
  -n desa # Namespace donde se va a crear el secreto
```

2. Se agrega a los manifiestos de Kubernetes el siguiente bloque, que inyecta las variables de entorno en el contenedor:

```yaml
envFrom:
- secretRef:
  name: spring-boot-pretesting-secrets 
```
3. Se hace referencia a las variables de entorno en el código fuente de la aplicación:
  
```java
  @Value("${DB_USERNAME:defaultUsername}")
  private String dbUsername;

  @Value("${DB_PASSWORD:defaultPassword}")
  private String dbPassword;
```

Y listo! Con estos pasos, se asegura que las variables de entorno sensibles estén protegidas y gestionadas de forma segura en el clúster de Kubernetes. En cada entorno, tendrá sus valores correspondientes, y no será necesario modificar el código fuente ni la imagen Docker al momento de hacer algún cambio.

> **Por qué se definen valores por defecto?** 
> Se definen valores por defecto para evitar que la aplicación falle si no se encuentra la variable de entorno. Esto es útil en entornos de desarrollo o pruebas, donde puede que no se necesiten las credenciales reales, o para la compilación en el proceso de CI Sin embargo, en producción, se espera que las variables de entorno estén correctamente configuradas y los valores por defecto no sean utilizados.

> **Qué pasa si necesito el valor de una variable de entorno en el código fuente al momento de la validación en el proceso de CI?**
> En ese caso, se recomienda el uso de la Azure DevOps Library, donde se pueden definir variables de entorno que se pueden utilizar en el proceso de CI. Estas variables pueden ser definidas como secretos y estarán disponibles durante la ejecución del pipeline. De esta forma, se asegura que las credenciales sensibles no estén expuestas en el código fuente ni en los logs del pipeline.

> **Cómo hago si quiero cambiar el secreto?**
> Para cambiar el secreto, se puede utilizar el siguiente comando:

```bash
kubectl create secret generic spring-boot-pretesting-secrets \
  --from-literal=DB_USERNAME=nuevoUsuario \
  --from-literal=DB_PASSWORD=nuevaContraseña \
  -n desa # Namespace donde se va a crear el secreto
```
> Esto actualizará el secreto existente con los nuevos valores. No es necesario eliminar el secreto anterior, ya que Kubernetes lo reemplazará automáticamente. Sin embargo, es importante tener en cuenta que si la aplicación ya está en ejecución, es posible que necesite reiniciarse para que los nuevos valores de las variables de entorno sean aplicados.

### Anexo: ContinueOnError y notificaciones por mail

En este ejemplo, se utiliza la opción `continueOnError` en varios pasos del pipeline. Si se define en true, el pipeline continuará su ejecución incluso si el paso falla, pero si se define en false, el pipeline se detendrá y no continuará con los pasos siguientes. Cualquiera sea la opción elegida, al final del Azure DevOps enviará un correo electrónico con el resultado de la ejecución  (Succeded, PartiallySucceeded o Failed) explicando el motivo del error si lo hubiere, y el paso donde ocurrió. 

# Despliegue Continuo (CD) con Azure DevOps Releases
![[pipeline_cd.png]]

En esta sección se detalla el proceso de **Despliegue Continuo (CD)** usando la funcionalidad de **Releases** en Azure DevOps. A diferencia del pipeline de CI, que se encarga de construir y testear el código, acá se orquesta el despliegue de la aplicación ya empaquetada y probada (imagen Docker) en los entornos de **testing** y **producción**.

El objetivo es lograr una entrega controlada y progresiva del software, integrando aprobaciones manuales con mínima intervención humana, pero con pasos bien definidos para asegurar calidad y trazabilidad.

## Flujo general del Release Pipeline

1. **Disparo del pipeline**: Se lanza automáticamente cuando la build de CI genera un nuevo artefacto (imagen Docker). También puede lanzarse manualmente para situaciones puntuales.
2. **Selección del entorno destino**: Se definen ambos entornos (testing y producción), cada uno con sus configuraciones propias.
3. **Despliegue de la imagen Docker**: Se toma la imagen generada y publicada en el registry privado y se despliega en el entorno correspondiente mediante `kubectl` y los manifiestos correspondientes de Kubernetes (Todos creados y subidos en el Artifact).
## Características destacadas

### Reutilización de artefactos
El pipeline de CD **no vuelve a compilar nada**. Solo trabaja con los artefactos (en este caso, la imagen Docker) generados por el pipeline de CI. Esto garantiza que lo que se prueba en desarrollo es exactamente lo mismo que se va a producción.

### Aprobaciones manuales
Cada entorno puede configurarse con **aprobaciones manuales obligatorias**. Esto es útil, por ejemplo, para evitar que se despliegue algo a producción sin revisión previa de un líder técnico o de QA. Azure DevOps permite definir grupos o usuarios responsables de aprobar manualmente el pase entre entornos.

### Validaciones post-despliegue
Después del despliegue, se pueden ejecutar scripts personalizados para verificar que la aplicación esté corriendo como se espera. Por ejemplo: hacer un `curl` a la API para verificar respuesta 200 OK, chequear logs, consumo de CPU, etc. Esto permite detectar errores antes de que lleguen a un entorno más crítico.

### Separación por entorno
Cada entorno tiene su propia configuración: namespace, secretos, variables, y parámetros de despliegue. Esto permite mantener entornos **aislados y seguros**, donde se puede probar una funcionalidad sin afectar a otras instancias del sistema.

### Notificaciones
Azure DevOps envía automáticamente notificaciones por mail a los involucrados (QA, Devs, Líderes, etc.) en caso de errores o finalización del despliegue, o necesidad de aprobación.

### Seguridad de secretos
El manejo de secretos se hace usando Kubernetes Secrets, como se explicó en la parte de CI, pero en este caso los valores varían por entorno. Cada namespace tiene su propio secreto, lo que permite usar credenciales distintas en cada etapa del pipeline, sin compartir datos sensibles.