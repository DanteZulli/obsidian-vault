
# Entornos (Environments)

- **Objetivo**: Organizar recursos (Kubernetes, VMs, etc.) para gestionar despliegues en etapas (dev, QA, producción).
- **Funcionalidades clave**:
    - Historial de despliegues.
    - Aprobaciones manuales/automáticas.
    - Integración con recursos externos (Azure, clusters).        

## ¿Para qué sirven?

1. **Rastrear despliegues**: Ver qué versión está en cada entorno.
2. **Controlar accesos**: Definir quién aprueba o ejecuta despliegues.
3. **Monitorear recursos**: Estado de clusters, VMs, etc.

## Uso en Pipelines

```yaml
jobs:  
    - deployment: Deploy  
      environment: producción  # Nombre del entorno  
      steps:  
        - script: echo "Desplegando..."  
```
- **Aprobaciones**: Configurar _checks_ en el entorno para pausar el pipeline hasta aprobación.

## Buenas Prácticas

- Nombres claros (`dev`, `prod`).
- Aprobaciones obligatorias para entornos críticos.
- No reutilizar recursos entre etapas.

## Ejemplo

```yaml

stages:  
- stage: DeployProd  
  jobs:  
  - deployment: Prod  
    environment: producción  
    steps:  
      - script: echo "Release en prod!"  

```

# Lanzamientos (Releases)

## ¿Qué son?

- **Objetivo**: Automatizar y gestionar el despliegue de aplicaciones en múltiples etapas (ej: dev, QA, producción) mediante **pipelines de despliegue**.
- **Funcionalidades clave**:
    - Despliegues multi-etapa con aprobaciones manuales.
    - Integración con artefactos de build (ej: paquetes de CI).
    - Rollback automático en caso de fallos.

## ¿Para qué sirven?

1. **Orquestar despliegues complejos**: Secuenciar tareas en entornos ordenados (ej: primero QA, luego producción).
2. **Gestionar aprobaciones**: Validar con equipos específicos antes de avanzar a la siguiente etapa.
3. **Variables por entorno**: Definir configuraciones específicas (ej: URLs, secrets) para cada etapa.
4. **Auditar cambios**: Ver qué versión se desplegó y cuándo.

## Uso en Pipelines

### Crear un Release

1. **Desde Azure DevOps**:  
    `Pipelines` → `Releases` → `New Pipeline` → Seleccionar artefacto (ej: build de CI).
2. **Definir etapas**:  
    Configurar tareas para cada entorno (ej: ejecutar scripts, desplegar en Azure).

```yaml
 stage: DeployQA
  jobs:
  - deployment: QA
    environment: qa
    steps:
      - task: AzureWebApp@1
        inputs:
          appName: "mi-app-qa"

```

## Buenas Prácticas

- **Separar etapas claramente**: `dev` → `staging` → `producción`.
- **Usar gates automáticos**: Validar métricas (ej: pruebas, monitoreo) antes de continuar.
- **Variables sensibles**: Usar _Azure Key Vault_ o variables secretas para contraseñas.
- **Monitorear ejecuciones**: Revisar logs y estado de cada etapa.
- 
## Ejemplo

Pipeline de Release con 3 etapas

```yaml
name: Release-$(Date:yyyyMMdd)

stages:
- stage: DeployDev
  jobs:
  - deployment: Dev
    environment: dev
    steps:
      - script: echo "Desplegando en dev..."

- stage: DeployQA
  dependsOn: DeployDev
  jobs:
  - deployment: QA
    environment: qa
    steps:
      - script: echo "Desplegando en QA..."

- stage: DeployProd
  dependsOn: DeployQA
  jobs:
  - deployment: Prod
    environment: prod
    steps:
      - script: echo "Desplegando en producción..."
```

## Recursos Extra

- [Azure DevOps Releases](https://learn.microsoft.com/es-es/azure/devops/pipelines/release/?view=azure-devops)
- [Environments](https://learn.microsoft.com/es-es/azure/devops/pipelines/process/environments)
- [Entornos Azure DevOps EXPLICADOS](https://www.youtube.com/watch?v=gN4j65w7wIM&t=350s)
- [Why EVERYONE should use Azure DevOps Environments for Kubernetes deployments](https://www.youtube.com/@CoderDave)