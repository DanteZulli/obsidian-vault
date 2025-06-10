# Introducción

Para poder desplegar nuevos cambios en nuestra aplicación, ya sea para nuevas funcionalidades (features) o corrección de errores (bugs), primero necesitamos "testear" y empaquetar el nuevo código en un "artifact". Este artifact luego será desplegado en un entorno final, como producción o pruebas. Todo este proceso se maneja desde Azure DevOps.

# Azure Pipelines

Una **pipeline** es una secuencia de pasos automatizados que se ejecutan para construir, probar, empaquetar y desplegar una aplicación de manera continua. La clave de Azure DevOps es automatizar estos flujos de trabajo para hacerlos más rápidos y eficientes, mejorando la calidad y la velocidad del desarrollo.

En Azure DevOps, las pipelines se dividen principalmente en dos tipos de flujos: **Integración Continua (CI)** y **Despliegue Continuo (CD)**.

## Flujos de Integración Continua (CI)

El flujo de **CI** (Continuous Integration) se enfoca en integrar automáticamente los cambios realizados en el código fuente. Cuando se realizan cambios en el código, la pipeline de CI se encarga de realizar las siguientes tareas de forma automatizada:

1. **Pruebas**: Ejecutar pruebas unitarias, de integración y otras pruebas necesarias para asegurar que el nuevo código no rompa funcionalidades existentes.
    
2. **Compilación y Empaquetado**: Compilar el código fuente y generar los artifacts necesarios para la siguiente fase del proceso (por ejemplo, archivos WAR, JAR, Docker images, etc.).

La principal ventaja de CI es que permite detectar y corregir problemas de manera temprana, minimizando los errores al integrar el nuevo código.

En Azure DevOps, las pipelines de CI se configuran en el apartado **Azure Pipelines**, utilizando **archivos YAML**. Estos archivos definen los pasos a seguir para cada pipeline de integración.

El código de estas pipelines se almacena en el repositorio de **Git**, junto con el código fuente de la aplicación. Esto permite tener versionado y controlado todo el proceso de integración.

### Ejemplo básico de archivo YAML para CI:

```yaml
trigger:
  branches:
    include:
      - main

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: UseJavaToolInstaller@0
  inputs:
    versionSpec: '11'

- task: Maven@3
  inputs:
    mavenPomFile: '$(Build.SourcesDirectory)/pom.xml'
    options: '-DskipTests=true'

- script: |
    echo "Ejecutando pruebas adicionales"
    ./run_additional_tests.sh
  displayName: 'Ejecutar pruebas adicionales'

- task: PublishBuildArtifacts@1
  inputs:
    pathToPublish: '$(Build.ArtifactStagingDirectory)'
    artifactName: 'drop'

```

En este ejemplo básico, el archivo YAML indica que cuando se haga un push a la rama `main`, se ejecutará la pipeline en una máquina virtual con Ubuntu, instalando Java 11,  ejecutando Maven para empaquetar el código y luego unas "pruebas adicionales" por script de bash. Finalmente, los artifacts generados serán publicados para su uso posterior.

###  Steps

Un **step** es una unidad de trabajo dentro de una pipeline en Azure DevOps. Puede ser un **script** o una **task**.
- Scripts: Son comandos personalizados escritos en Bash, PowerShell, etc. Permiten total flexibilidad.
- Tasks: Son acciones predefinidas que realizan tareas comunes (compilar, probar, desplegar). No requieren escribir código, ya que son scripts pre-creados.

### ¿Cómo se gestionan las Tasks?

Azure DevOps tiene un **Marketplace** donde puedes encontrar y añadir tasks predefinidas. También puedes crear tasks personalizadas si las predeterminadas no son suficientes.

## Jobs

Un **job** es un conjunto de pasos (steps) que se ejecutan en un mismo entorno. Los jobs permiten organizar el flujo de trabajo de la pipeline en diferentes bloques, y estos pueden ejecutarse de manera secuencial o en paralelo, dependiendo de cómo los configures.

### ¿Por qué usar Jobs?

1. **Organización**: Dividir la pipeline en diferentes jobs permite gestionar mejor los flujos y agrupar acciones relacionadas (por ejemplo, compilación, pruebas, despliegue).
    
2. **Paralelización**: Los jobs pueden ejecutarse en paralelo, lo que mejora la eficiencia de la pipeline.
    
3. **Ambientes separados**: Cada job puede ejecutarse en un ambiente diferente (por ejemplo, uno en Linux y otro en Windows).

```yaml
trigger:
  branches:
    include:
      - main

pool:
  vmImage: 'ubuntu-latest'

jobs:
- job: Build
  displayName: 'Compilación y pruebas'
  pool:
    vmImage: 'ubuntu-latest'
  steps:
  - task: UseJavaToolInstaller@0
    inputs:
      versionSpec: '11'

  - task: Maven@3
    inputs:
      mavenPomFile: '$(Build.SourcesDirectory)/pom.xml'
      options: '-DskipTests=true'

  - script: |
      echo "Ejecutando pruebas adicionales"
      ./run_additional_tests.sh
    displayName: 'Ejecutar pruebas adicionales'

- job: Publish
  displayName: 'Publicar artefactos'
  dependsOn: Build  # Este job depende del job 'Build'
  pool:
    vmImage: 'ubuntu-latest'
  steps:
  - task: PublishBuildArtifacts@1
    inputs:
      pathToPublish: '$(Build.ArtifactStagingDirectory)'
      artifactName: 'drop'
```

# Flujos de Despliegue Continuo (CD)

El flujo de **CD** (Continuous Deployment) se encarga de automatizar el despliegue de los artifacts generados en CI hacia los entornos objetivo (dev, QA, producción). Azure DevOps permite definir pipelines complejas con estrategias de despliegue controladas, aprobaciones manuales y gestión de ambientes.

## Stages

Los **stages** (etapas) dividen la pipeline en fases lógicas independientes, como `Build`, `Test`, `Deploy to QA`, `Deploy to Prod`. Cada stage puede tener:

- **Aprobaciones**: Requerir validación manual antes de ejecutarse (ej: despliegue a producción).
- **Dependencias**: Ejecutarse solo si stages anteriores fueron exitosos.
- **Entornos específicos**: Vincularse a un ambiente definido en Azure DevOps (para rastreo de despliegues).
## Agentes

Los **agentes** son máquinas (físicas o virtuales) que ejecutan los jobs de la pipeline. Pueden ser:

- **Microsoft-hosted**: Provisionales por Azure (ej: `ubuntu-latest`, `windows-2022`).
- **Self-hosted**: Máquinas propias configuradas por el equipo (útil para entornos restringidos o necesidades específicas).

En CD, es común usar agentes self-hosted para acceder a recursos internos (ej: servidores de producción).

## Deployment Jobs

Un **deployment job** es un tipo especial de job diseñado para despliegues. Ofrece:

- **Historial de despliegues**: Registra qué versión se desplegó en cada ambiente.
- **Estrategias de despliegue**: Define cómo se aplican los cambios (rolling, canary, blue-green).
- **Rollback automático**: En caso de fallo durante la implementación.
# Ejemplo Integrador CI/CD

El siguiente YAML define una pipeline completa con:

- **CI**: Compilación, pruebas y publicación de artifacts.
- **CD**: Despliegue en 3 ambientes (dev, QA, prod) con aprobación manual para producción.

```yaml
trigger:
  branches:
    include:
      - main

stages:
  # --------------------- CI Stage ---------------------
  - stage: Build_Stage
    displayName: "Construir y Publicar Artifact"
    jobs:
      - job: Build_Job
        pool:
          vmImage: 'ubuntu-latest'
        steps:
          - task: UseJavaToolInstaller@0
            inputs:
              versionSpec: '11'

          - task: Maven@3
            inputs:
              mavenPomFile: 'pom.xml'
              goals: 'package'
              options: '-DskipTests=false'

          - task: PublishBuildArtifacts@1
            inputs:
              pathToPublish: '$(Build.ArtifactStagingDirectory)'
              artifactName: 'myapp'

  # --------------------- CD Stages ---------------------
  - stage: Deploy_Dev
    displayName: "Desplegar en Desarrollo"
    dependsOn: Build_Stage
    condition: succeeded('Build_Stage')
    jobs:
      - deployment: Deploy_Dev_Job
        environment: 'dev'  # Ambiente definido en Azure DevOps
        pool:
          vmImage: 'ubuntu-latest'
        strategy:
          runOnce:
            deploy:
              steps:
                - download: current
                  artifact: 'myapp'
                - script: |
                    echo "Desplegando en desarrollo..."
                    ./deploy-script.sh dev
                  displayName: 'Ejecutar script de despliegue'

  - stage: Deploy_QA
    displayName: "Desplegar en QA"
    dependsOn: Deploy_Dev
    condition: succeeded('Deploy_Dev')
    jobs:
      - deployment: Deploy_QA_Job
        environment: 'qa'
        pool:
          name: 'MySelfHostedPool'  # Usa un agente self-hosted
        strategy:
          rolling:
            maxParallel: 2
            preDeploy:
              steps:
                - script: echo "Validando configuración de QA..."
            deploy:
              steps:
                - download: current
                  artifact: 'myapp'
                - script: |
                    echo "Desplegando en QA..."
                    ./deploy-script.sh qa

  - stage: Deploy_Prod
    displayName: "Desplegar en Producción"
    dependsOn: Deploy_QA
    condition: succeeded('Deploy_QA')
    jobs:
      - deployment: Deploy_Prod_Job
        environment: 'prod'
        pool:
          name: 'MySelfHostedPool'
        strategy:
          canary:
            increments: [25, 75]
            preDeploy:
              steps:
                - script: echo "Aprobación manual requerida ✅"
            deploy:
              steps:
                - download: current
                  artifact: 'myapp'
                - script: |
                    echo "Desplegando en producción..."
                    ./deploy-script.sh prod
```

## Explicación del Ejemplo

### 1. **Stages**

- **Build_Stage**: Etapa de CI que compila el código y publica el artifact.
- **Deploy_Dev**, **Deploy_QA**, **Deploy_Prod**: Etapas de CD con dependencias secuenciales (`dependsOn`).

### 2. **Agentes**

- **Microsoft-hosted**: Usado en `Build_Stage` y `Deploy_Dev`.
- **Self-hosted**: Usado en QA y Prod (`MySelfHostedPool`), típico para acceder a recursos internos.

### 3. **Deployment Jobs**

- **Estrategias**:
    - `runOnce` (Dev): Despliegue estándar.
    - `rolling` (QA): Actualiza instancias en grupos (útil para minimizar downtime).
    - `canary` (Prod): Liberación gradual (25% → 75% → 100%) con aprobaciones manuales.

### 4. **Ambientes y Aprobaciones**

- Los ambientes `dev`, `qa`, `prod` deben estar preconfigurados en Azure DevOps.
- En `prod`, la estrategia `canary` requiere aprobación manual entre incrementos (configurable en la UI de Azure DevOps).

# Resumen

El ejemplo presentado es una guía **genérica** para ilustrar cómo estructurar un flujo CI/CD en Azure DevOps. Sin embargo, cada proyecto tiene necesidades únicas, por lo que es fundamental:

1. **Adaptar el YAML a cada contexto**:
    
    - Definir las **tasks** específicas según el stack tecnológico (ej: `npm` para JavaScript, `maven` para Java, `docker` para contenedores).
    - Ajustar **stages**, **jobs** y estrategias de despliegue según los ambientes de la organización (ej: agregar pre-producción, staging).
        
2. **Configurar correctamente los entornos y agentes**:
    
    - Usar **agentes self-hosted** para ambientes restringidos (ej: acceso a bases de datos internas).
    - Definir **checks y aprobaciones** en los ambientes críticos (como producción).
        
3. **Validar las necesidades del proyecto**:
    
    - Incluir pasos adicionales como escaneos de seguridad, SonarQube, notificaciones por correo/Teams, o integración con herramientas de monitoreo.
    - Considerar estrategias de **rollback** automático en caso de fallos.
        

> ⚠️ **Importante**:
> 
> - Este flujo asume que los **entornos** (dev, QA, prod) ya están configurados en Azure DevOps.
> - Las pipelines complejas pueden requerir **variables secretas** (ej: contraseñas, tokens) almacenadas en Azure Key Vault, o en los Secrets de Kubernetes

**Nota Final**:  
Es importante recordar que la automatización CI/CD es un proceso iterativo. Se comienza con un flujo básico, se valida su funcionamiento, y luego agrega complejidad gradualmente.

# Recursos Extra

Para profundizar en Azure Pipelines, consulta:

1. **Documentación Oficial**:
    - [Azure Pipelines YAML Schema](https://learn.microsoft.com/en-us/azure/devops/pipelines/yaml-schema/)
    - [Estrategias de despliegue](https://learn.microsoft.com/en-us/azure/devops/pipelines/process/deployment-jobs?view=azure-devops)
    - [¿Qué es Azure Pipelines?](https://learn.microsoft.com/en-us/previous-versions/azure/devops/pipelines/get-started/what-is-azure-pipelines)
2. **Fundamentos de DevOps**:
    - [¿Qué es DevOps?](https://learn.microsoft.com/en-us/devops/what-is-devops)
3. **Guías Prácticas**:
    - [Crear tu primera pipeline CI/CD](https://learn.microsoft.com/en-us/training/modules/create-release-pipeline/) (Microsoft Learn).
    - [Integración con Docker/Kubernetes](https://learn.microsoft.com/en-us/azure/devops/pipelines/ecosystems/containers/build-image?view=azure-devops).
4. **Videos:**
	- https://www.youtube.com/watch?v=4BibQ69MD8c&t=244s
	- https://www.youtube.com/watch?v=fnuWc92A75o