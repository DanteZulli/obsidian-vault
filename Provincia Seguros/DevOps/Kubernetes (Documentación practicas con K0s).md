# Introducción

Este documento resume los conceptos clave aprendidos en las clases prácticas de los videos de YouTube relacionados con Kubernetes [1](https://www.youtube.com/watch?v=gmFSmzAWcig) [2](https://www.youtube.com/watch?v=gPALJhVYMz0), utilizando `k0s` como [distribución liviana y autónoma](https://k0sproject.io/). Se documenta la instalación y configuración que se realizó en `svrpruebas`, junto con ejemplos prácticos.

> NOTA: La documentación cuenta con ejemplos concisos y funcionales, y descripciones acotadas. Para una mayor profundidad, recomiendo leer la documentación oficial de Kubernetes (listada al pie de este documento) que es bastante expeditiva.

> PD: Se utiliza k0s porque es la recomendación del tutorial, ya que es liviano y apto para entornos productivos, pero también podría haberse hecho con k3s, minikube, o cualquier otro.

> PD 2: Recomiendo el uso de [warp.dev](https://www.warp.dev/) para facilitar el proceso de debugging y solución de errores. El tier gratuito es mas que suficiente para seguir esta guía.
# Kubernetes básico
## POD's

Un **Pod** es la unidad mínima de despliegue en Kubernetes. Todos los contenedores dentro de un Pod comparten la misma red y almacenamiento. Un Pod siempre se ejecuta en un único nodo. 

> NOTA: Un Pod no es ideal para aplicaciones escalables, ya que no se reprograman automáticamente si fallan (para eso se usa un Deployment)

El siguiente YAML define un Pod llamado `web1`, que contiene dos contenedores: `front1`, basado en Apache HTTP Server (`httpd`), y `back1`, basado en Ubuntu, ejecutando un bucle infinito que imprime 'hello' cada 10 segundos.

```yaml
# app-prueba.yml
apiVersion: v1
kind: Pod 
metadata:
  name: web1
  labels:
    app: web
spec:
  containers: 
    - name: front1
      image: httpd:2.4
      ports:
        - containerPort: 80
    - name: back1
      image: ubuntu:noble
      command: ["/bin/sh", "-c"]
      args:
        - while true; do echo hello; sleep 10; done
```

> NOTA: Es recomendable siempre definir versiones concretas de los contenedores, ya que Kubernetes no almacena imágenes con etiquetas flotantes.

```sh
# Crear el yaml
nano app-prueba.yml

# Aplicar el yaml
sudo k0s kubectl apply -f app-prueba.yml

# Verificar si el Pod está corriendo
sudo k0s kubectl get pods

# Ver logs del contenedor 'back1' (Una vez que se haya creado)
kubectl logs -f pod/web1 -c back1


#### Algunos comandos extra

# Ver informacion del pod
sudo k0s kubectl describe pod web1

# Ver consumo de recursos del pod
sudo k0s kubectl top pod web1

# Ejecutar comandos dentro de un contenedor del pod
# -it: Interactivo (Para que no se cierre luego de ejecutar)
sudo k0s kubectl exec web1 -it -c front1 -- /bin/bash

# Copiar ficheros del local al pod
sudo k0s kubectl cp /home/origen pod:/destino -c front1

# Exponer un puerto a traves de port forwarding
sudo k0s kubectl port-forward --address 0.0.0.0 pod/web1 8090:80
# Para probar si esto funciona (desde el servidor)
curl 127.0.0.1:8090

# Eliminar el pod
sudo k0s kubectl delete -f app-prueba.yml
```

## Namespaces

Un **namespace** es un mecanismo de aislamiento lógico dentro de un clúster, que permite organizar y segmentar recursos como pods, servicios y configuraciones. Se utilizan principalmente para separar diferentes entornos (por ejemplo, desarrollo, testing y producción) o equipos dentro de un mismo clúster.

El siguiente YAML define un Pod llamado `web2`, que contiene un contenedor: `front2`, basado en nginx (`httpd`).

```yaml
# app-prueba2.yml
apiVersion: v1
kind: Pod 
metadata:
  name: web2
  labels:
    app: web
spec:
  containers: 
    - name: front2
      image: nginx:1.27.4
      ports:
        - containerPort: 80
```

> NOTA: No voy a volver a documentar el proceso de creación del POD, para no volverlo redundante. Se detalla solo lo relacionado a namespaces.

```sh
# Obtener todos los namespaces
sudo k0s kubectl get namespaces

# Crear un nuevo namespace
sudo k0s kubectl create namespace desa

# Desplegar una app en un namespace especifico
sudo k0s kubectl apply -f app-prueba2.yml --namespace=desa

# Ver todos los elementos de un namespace
sudo k0s kubectl get all --namespace=desa

# Configurar un namespace por defecto en donde realizar todas las operaciones
# (Para ahorrarse poner --namespace)
sudo k0s kubectl config set-context --current --namespace=desa

```

## Servicios

Un **servicio (Service)** es un recurso que expone una aplicación en ejecución dentro del clúster y proporciona un punto de acceso estable para los pods, incluso si estos cambian de dirección IP debido a reinicios o escalado.

Existen **tres tipos principales de servicios**; ClusterIP (dentro del cluster), NodePort (dentro y fuera del cluster) y LoadBalancer (para servicios cloud con IP Externa).

Los servicios nos otorgan una IP para acceder a nuestros servicios, balanceo de carga y acceso a servicio via DNS.


```yaml
# app-prueba.yml
apiVersion: v1
kind: Pod 
metadata:
  name: web1
  labels:
    app: web
spec:
  containers: 
    - name: front1
      image: httpd:2.4
      ports:
        - containerPort: 80
    - name: back1
      image: ubuntu:noble
      command: ["/bin/sh", "-c"]
      args:
        - while true; do echo hello; sleep 10; done
---
apiVersion: v1
kind: Service
metadata:
  name: service1
spec:
  type: NodePort
  selector: # Las etiquetas son la manera que tienen los servicios de apuntar a un pod o a un determinado grupo de pods
    app: web
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30000 # Ojo porque algunos puertos estan bloqueados por politicas
```

```sh
# Contexto: Luego de editar el app-prueba.yml y realizar el apply
[dante@svrpruebas practicas-k8s]$ sudo k0s kubectl get all # Revisamos si el servicio esta corriendo
NAME       READY   STATUS    RESTARTS   AGE
pod/web1   2/2     Running   0          19h

NAME                 TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
service/kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP        20h
service/service1     NodePort    10.111.65.73   <none>        80:30000/TCP   12m

# Ahora sabemos que desde fuera del cluster, podemos acceder a nuestro apache en http://svrpruebas:30000/
# Si no fuera el caso, podemos probar cambiando de puerto, y redesplegando.
```

## Deployments

Los POD's no son escalables, y no se auto-reestablecen / recuperan en caso de fallo.
Para esto existen los deployments.

Un deployment no es mas que una descripción de un POD que el mismo deployment va a gestionar, haciéndolo escalable a demanda y restaurable ante fallos.

```yaml
# app-prueba.yml
apiVersion: apps/v1 # Cambia la API de Kubernetes
kind: Deployment # Ya no es un POD
metadata:
  name: deployment1
  labels:
    app: web
spec:
  replicas: 2 # Cantidad de replicas del mismo despliegue
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers: 
        - name: front1
          image: httpd:2.4
          ports:
            - containerPort: 80
        - name: back1
          image: ubuntu:noble
          command: ["/bin/sh", "-c"]
          args:
            - while true; do echo hello; sleep 10; done
---
apiVersion: v1
kind: Service
metadata:
  name: service1
spec:
  type: NodePort
  selector:
    app: web
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30000

```

```sh
# Contexto: Luego de editar el app-prueba.yml y realizar el apply

# Revisamos los dos despliegues de nuestro Pod, la definicion del Deployment, y el replicaset que se asegura de que siempre se mantenga el mismo numero de replicas corriendo.
[dante@svrpruebas practicas-k8s]$ sudo k0s kubectl get all
NAME                               READY   STATUS    RESTARTS   AGE
pod/deployment1-7bc665f888-2p9hv   2/2     Running   0          28s
pod/deployment1-7bc665f888-9wwgv   2/2     Running   0          28s

NAME                 TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
service/kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP        21h
service/service1     NodePort    10.109.204.2   <none>        80:30000/TCP   28s

NAME                          READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/deployment1   2/2     2            2           28s

NAME                                     DESIRED   CURRENT   READY   AGE
replicaset.apps/deployment1-7bc665f888   2         2         2       28s
```

```sh
# Escalar Deployment (tambien se puede usar para "des-escalar")
sudo k0s kubectl scale --replicas 5 deployment/deployment1
```

## Statefulsets

Los Deployments por naturaleza no pueden tener "estado". Cada vez que se autogenera un nuevo Pod, este toma un nombre aleatorio, imposibilitando así saber cual es el primero, el segundo, el tercero, etc. Y cuando se decide escalar para arriba o para abajo, el Deployment no maneja los Pods en orden.
Servicios como Elasticsearch, o bases de datos (PostgreSQL, MySQL, MariaDB, etc.) requieren de un orden definido para poder escalarse y operarse.

Un **Statefulset** es un recurso que se usa para gestionar aplicaciones con estado, asegurando que cada pod tenga una identidad única y persistente, incluso si es eliminado o reiniciado. Es ideal para bases de datos y aplicaciones distribuidas que requieren almacenamiento persistente y un orden específico de despliegue y escalado.

```yaml
# app-statefulset.yml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  ports:
    - port: 80
      name: web
  clusterIP: None
  selector:
    app: nginx
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: nginx-statefulset
  labels:
    app: nginx
spec:
  serviceName: nginx-service # El nombre del servicio declarado
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:1.27.4
          ports:
          - containerPort: 80
```


```sh
# Contexto: Luego de crear el app-statefulset.yml y realizar el apply

# Podemos ver como el statefulset define los nombres con valor numerico autoincremental
[dante@svrpruebas practicas-k8s]$ sudo k0s kubectl get all
NAME                      READY   STATUS    RESTARTS   AGE
pod/nginx-statefulset-0   1/1     Running   0          91s
pod/nginx-statefulset-1   1/1     Running   0          90s
pod/nginx-statefulset-2   1/1     Running   0          89s

NAME                    TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes      ClusterIP   10.96.0.1    <none>        443/TCP   21h
service/nginx-service   ClusterIP   None         <none>        80/TCP    91s

NAME                                 READY   AGE
statefulset.apps/nginx-statefulset   3/3     91s
```

#  Ejemplo Integrador 1

## Resumen contenido Kubernetes básico

En este apartado se describe el proceso (a groso modo) de cómo seria desplegar una aplicación Java en Kubernetes, apoyándonos en los conceptos básicos definidos en esta documentación.  Si bien falta comprender algunos conceptos, y agregarle ciertas cosas para que sea considerado "apto para producción", con lo ya aprendido podemos plantear un supuesto "escenario real" para ya ir reforzando lo aprendido.

Para este ejemplo, vamos a desplegar **PS-PES** en dos namespaces (`testing` y `produccion`), utilizando puertos expuestos diferentes a traves de `NodePort`. Se utilizarán los recursos de Kubernetes como `Deployments`, `Services`, para gestionar la aplicación en un entorno de alta disponibilidad.

### Supuestos

1. Tenemos una aplicación Java **PS-PES** que expone el puerto 8080.
2. Queremos desplegarla en dos entornos: `testing` y `produccion`.
3. Los puertos expuestos deben ser diferentes para cada entorno.
4. Queremos implementar un balanceo de carga para manejar picos de tráfico y escalar automáticamente las réplicas de la aplicación.
### Paso 1: Crear los Namespaces

Primero, creamos los dos namespaces en Kubernetes donde se desplegarán las aplicaciones. Esto nos permite aislar los recursos de cada entorno.

```sh
sudo k0s kubectl create namespace testing 
sudo k0s kubectl create namespace produccion
```

### Paso 2: Desplegar la Aplicación en el Namespace "testing"

Creamos un archivo de despliegue (deployment) para la aplicación Java en el namespace `testing`.  Configuramos el contenedor para usar el puerto 8080 y una réplica.

```yaml
# ps-pes-deployment-testing.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ps-pes-testing
  namespace: testing # Nos ahorra configurar en namespace o aclararlo con --namespace=
spec:
  replicas: 2
  selector:
    matchLabels:
      app: ps-pes
  template:
    metadata:
      labels:
        app: ps-pes
    spec:
      containers:
      - name: ps-pes
        image: mi-imagen-ps-pes:latest # Nos falta definir el origen de la imagen, para este ejemplo, suponamos que esta en la registry de dockerhub
        ports:
        - containerPort: 8080

```

Desplegamos el archivo en Kubernetes:

```bash
sudo k0s kubectl apply -f ps-pes-deployment-testing.yaml
```

### Paso 3: Exponer el Servicio en el Namespace "testing" usando `NodePort`

Exponemos el servicio en el namespace `testing` utilizando un servicio de tipo `NodePort`, para que el puerto de la aplicación sea accesible desde fuera del clúster a través de los nodos.

```yaml
# ps-pes-service-testing.yaml
apiVersion: v1
kind: Service
metadata:
  name: ps-pes-service-testing
  namespace: testing
spec:
  selector:
    app: ps-pes
  ports:
    - protocol: TCP
      port: 80        # Puerto expuesto dentro del clúster
      targetPort: 8080 # Puerto dentro del contenedor
      nodePort: 30001  # Puerto que se expondrá en los nodos
  type: NodePort
```

> NOTA: Si bien en los ejemplos de la documentación hacíamos todo en un mismo yaml, no es necesario, ya que los selectores se encargan de referenciar por tags a los recursos en cuestión. En este paso a paso, lo hacemos todo por separado.

Aplicamos el servicio:

```bash
sudo k0s kubectl apply -f ps-pes-service-testing.yaml
```

Ahora la aplicación en el namespace `testing` estará disponible en cualquier nodo del clúster en el puerto `30001`.

### Paso 4: Desplegar la Aplicación en el Namespace "produccion"

Repetimos el proceso para el namespace `produccion`, con una configuración similar pero usando el puerto 8081 para diferenciarlo.

```yaml
# ps-pes-deployment-produccion.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ps-pes-produccion
  namespace: produccion
spec:
  replicas: 3
  selector:
    matchLabels:
      app: ps-pes
  template:
    metadata:
      labels:
        app: ps-pes
    spec:
      containers:
      - name: ps-pes
        image: mi-imagen-ps-pes:latest
        ports:
        - containerPort: 8081

```

Desplegamos el archivo en Kubernetes:

```bash
sudo k0s kubectl apply -f ps-pes-deployment-produccion.yaml
```

### Paso 5: Exponer el Servicio en el Namespace "produccion" usando `NodePort`

Exponemos la aplicación `ps-pes` en el namespace `produccion` usando `NodePort`, pero en el puerto `30002` para diferenciarlo del entorno `testing`.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: ps-pes-service-produccion
  namespace: produccion
spec:
  selector:
    app: ps-pes
  ports:
    - protocol: TCP
      port: 80        # Puerto expuesto dentro del clúster
      targetPort: 8081 # Puerto dentro del contenedor
      nodePort: 30002  # Puerto que se expondrá en los nodos
  type: NodePort
```

Aplicamos el servicio:

```bash
sudo k0s kubectl apply -f ps-pes-service-produccion.yaml
```

### Resumen

Con estos pasos, desplegamos la aplicación `ps-pes` en dos entornos diferentes dentro de Kubernetes utilizando `NodePort`. La aplicación en el namespace `testing` está disponible en el puerto `30001`, mientras que la aplicación en el namespace `produccion` está disponible en el puerto `30002`. Ambos servicios son accesibles desde fuera del clúster a través de cualquier nodo del clúster y su puerto asignado, y ambos servicios poseen dos replicas en sus deployments para balancear su carga.

# Kubernetes avanzado

## Volúmenes

Un volumen es una ruta del contenedor (pero fuera del mismo) en donde los datos se van a persistir (Una carpeta en el host, una carpeta de red, algún tipo de servicio de almacenamiento en la nube, etc.). Los volúmenes son la solución que nos proveen los contenedores para tener persistencia de ficheros

```yaml
# volume.yaml
apiVersion: v1  
kind: Pod  
metadata:  
  name: test-pd  
  labels:  
    app: test-app  
  
spec:  
  containers:  
  - image: nginx  
    name: test-container  
    volumeMounts:  
    - mountPath: "/usr/share/nginx/html"  
      name: host-volume  
      
# Hay varios tipos de volumenes, pero para este ejemplo, vamos a usar un volumen montado en el host (un directorio del server).
  volumes:  
  - name: host-volume  
    hostPath:  
      path: /home/docker/html # Asegurense de que el directorio exista en la maquina en cuestion.
      type: Directory  
---  
apiVersion: v1  
kind: Service  
metadata:  
  name: nginx1  
spec:  
  type: NodePort  
  selector:  
    app: test-app  
  ports:  
  - protocol: TCP  
    port: 80  
    targetPort: 80
    nodePort: 30000
```

> NOTA IMPORTANTE: A partir de este punto en la documentación, para facilitar el manejo de k0s desde la terminal, genere un alias en mi `.bashrc` para `sudo k0s kubectl` y genere un archivo dentro del `sudoers.d`, así cada vez que quería usar k0s, en lugar de utilizar el comando antes mencionado, solo fuera necesario escribir `kubectl`, sin contraseñas ni nada extra. Si ven que los comandos cambiaron, tengan en consideración eso.

```bash
# Se da por sentado que ya se sabe generar los manifiestos .yaml y aplicarlos con k0s ya que se vio en el apartado de 'Kubernetes Basico'

# Con la configuracion del volumen, nginx va a servir todo el contenido montado en el directorio local '/home/docker/html', asi que es necesario generar un html ahi.

sudo bash -c 'echo "<html><body><h1>Welcome to Nginx!</h1><p>This is a test page served from a Kubernetes volume.</p></body></html>" > /home/docker/html/index.html'

# Ahora podemos ver la pagina desde http://svrpruebas:30000 :D
```

### PersistentVolume y PersistentVolumeClaim (Volúmenes persistidos en el cluster)

Un **PersistentVolume** es un recurso de almacenamiento independiente del ciclo de vida de un pod. Permite a los pods acceder a almacenamiento persistente, gestionado por el clúster (o mejor dicho, por el administrador del mismo), a través de un **PersistentVolumeClaim***. Los PV pueden estar respaldados por discos locales, NFS, almacenamiento en la nube, entre otros, al igual que los volúmenes normales.

```yaml
# persistent-volume.yaml

# El volumen en cuestion a persistir, definido de forma similar a un volumen normal
apiVersion: v1  
kind: PersistentVolume 
metadata:  
  name: pv0002  
spec:  
  capacity:  
    storage: 0.2Gi
  accessModes:  
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle  
  storageClassName: standard  
  hostPath:  
      path: /home/docker/html  
      type: Directory  
---  
# La solicitud de persistencia del volumen antes declarado. Se encarga de solicitarle al cluster el espacio, y los recursos necesarios.
kind: PersistentVolumeClaim  
apiVersion: v1  
metadata:  
  name: claim-1  
spec:  
  accessModes:  
    - ReadWriteOnce 
  storageClassName: standard  
  resources:  
    requests:  
      storage: 0.2Gi  
---  
apiVersion: v1  
kind: Pod  
metadata:  
  name: task-pv-pod  
  labels:  
    app: test-app  
  
spec:  
  volumes:  
    - name: task-pv-storage  
      persistentVolumeClaim:  
        claimName: claim-1  
  containers:  
    - name: task-pv-container  
      image: nginx  
      ports:  
        - containerPort: 80  
          name: "http-server"  
      volumeMounts:  
        - mountPath: "/usr/share/nginx/html"  
          name: task-pv-storage  
---  
apiVersion: v1  
kind: Service  
metadata:  
  name: nginx1  
spec:  
  type: NodePort  
  selector:  
    app: test-app  
  ports:  
  - protocol: TCP  
    port: 80  
    targetPort: 80
```

```bash

# Luego podemos ver en que puerto se monto (Si no le definimos un nodePort al service)
kubectl get all
NAME              READY   STATUS    RESTARTS   AGE
pod/task-pv-pod   1/1     Running   0          31m

NAME                 TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
service/kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP        45h
service/nginx1       NodePort    10.110.42.91   <none>        80:30433/TCP   31m

# Y si en el capitulo anterior (Capitulo 'Volúmenes') creamos correctamente el html en '/home/docker/html', el curl a 30433 (o el puerto que se haya asignado) nos deberia de devolver algo asi

curl http://localhost:30433
<html><body><h1>Welcome to Nginx!</h1><p>This is a test page served from a Kubernetes volume.</p></body></html>

# La diferencia es que ahora, esta persistido en el cluster, y con recursos asignados/medidos.
```

## Ingress (Routing)

Un **Ingress** es un recurso que gestiona el acceso externo a los servicios del clúster, generalmente HTTP/HTTPS. Define reglas para el enrutamiento del tráfico y suele requerir un **Ingress Controller** para funcionar. Permite configurar hosts, rutas, TLS y balanceo de carga de manera centralizada.

Antes de comenzar a definir nuestros manifiestos, tenemos que asegurarnos de que el "Ingress Controller" este instalado en nuestra instancia local de k0s/k8s.
Para hacerlo, podemos verificar lo siguiente:

```bash

# Primero, revisamos si tenemos pods de ingress-nginx corriendo (Ingress usa nginx como backend para el routing)
[dante@svrpruebas practicas-k8s]$ kubectl get pods -A | grep ingress
ingress-nginx   ingress-nginx-admission-create-kf2s6        0/1     Completed   0          2m20s
ingress-nginx   ingress-nginx-admission-patch-sjddz         0/1     Completed   0          2m20s
ingress-nginx   ingress-nginx-controller-66cb9865b5-xgcnq   1/1     Running     0          2m20s

# Si NO vemos un output similar al de arriba, tenemos que aplicarlo. Hay varias formas, pero en nuestro caso, lo vamos a hacer por manifests.
[dante@svrpruebas practicas-k8s]$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.8.2/deploy/static/provider/cloud/deploy.yaml

namespace/ingress-nginx created
serviceaccount/ingress-nginx created
serviceaccount/ingress-nginx-admission created
role.rbac.authorization.k8s.io/ingress-nginx created
role.rbac.authorization.k8s.io/ingress-nginx-admission created
clusterrole.rbac.authorization.k8s.io/ingress-nginx created
clusterrole.rbac.authorization.k8s.io/ingress-nginx-admission created
rolebinding.rbac.authorization.k8s.io/ingress-nginx created
rolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
configmap/ingress-nginx-controller created
service/ingress-nginx-controller created
service/ingress-nginx-controller-admission created
deployment.apps/ingress-nginx-controller created
job.batch/ingress-nginx-admission-create created
job.batch/ingress-nginx-admission-patch created
ingressclass.networking.k8s.io/nginx created
validatingwebhookconfiguration.admissionregistration.k8s.io/ingress-nginx-admission created

# Y listo, podemos continuar con el despliegue
```

```yaml
# ingress.yaml

# Un par de Pod's para el ejemplo y sus servicios en cuestion
apiVersion: v1  
kind: Pod  
metadata:  
  name: test-pd1  
  labels:  
    app: test-nginx  
  
spec:  
  containers:  
  - image: nginx  
    name: test-container1  
---  
apiVersion: v1  
kind: Pod  
metadata:  
  name: test-pd2  
  labels:  
    app: test-apache  
  
spec:  
  containers:  
  - image: httpd  
    name: test-container2  
---  
apiVersion: v1  
kind: Service  
metadata:  
  name: nginx1  
spec:  
  type: NodePort  
  selector:  
    app: test-nginx  
  ports:  
  - protocol: TCP  
    port: 80  
    targetPort: 80  
---  
apiVersion: v1  
kind: Service  
metadata:  
  name: apache1  
spec:  
  type: NodePort  
  selector:  
    app: test-apache  
  ports:  
  - protocol: TCP  
    port: 80  
    targetPort: 80  
---  
# El Ingress en cuestion
apiVersion: networking.k8s.io/v1  # La API cambia
kind: Ingress  
metadata:  
  name: ingress1
  namespace: default
spec:  
  # Dentro de las reglas, define las rutas.
  # PD: Aca podriamos definir diferentes hosts dentro de nuestro cluster. 
  ingressClassName: nginx
  rules:  
  - http:  
      paths:
      - backend:
          service:
            name: nginx1
            port:
              number: 80
        path: /nginx
        pathType: Prefix
      - backend:
          service:
            name: apache1
            port:
              number: 80
        path: /apache
        pathType: Prefix
```

```bash
# Aplicamos el manifiesto
[dante@svrpruebas practicas-k8s]$ kubectl apply -f ingress.yaml
pod/test-pd1 created
pod/test-pd2 created
service/nginx1 configured
service/apache1 created
ingress.networking.k8s.io/ingress1 created

# Ahora podemos obtener los ingress. En este caso, tenemos un solo ingress, valido para todos los hosts, 
[dante@svrpruebas practicas-k8s]$ kubectl get ingress
NAME       CLASS    HOSTS   ADDRESS   PORTS   AGE
ingress1   <none>   *                 80      28s

# Si pedimos informacion acerca de este ingress, podemos ver que esta exponiendo dos puertos 80, de dos contenedores diferentes, pero bajo la misma ip, y enrutados a traves de los path's correspondientes
[dante@svrpruebas practicas-k8s]$ kubectl describe ingress/ingress1
Name:             ingress1
Labels:           <none>
Namespace:        default
Address:
Ingress Class:    nginx
Default backend:  <default>
Rules:
  Host        Path  Backends
  ----        ----  --------
  *
              /nginx    nginx1:80 (10.244.0.16:80)
              /apache   apache1:80 (10.244.0.17:80)
Annotations:  <none>
Events:
  Type    Reason  Age    From                      Message
  ----    ------  ----   ----                      -------
  Normal  Sync    5m10s  nginx-ingress-controller  Scheduled for sync

# Ahora deberiamos de poder acceder tanto a http://svrpruebas:PUERTO/nginx y a http://svrpruebas:PUERTO/apache en donde puerto es el nodePort del service o el autogestionado por k8s
# PD: Si da 404 es normal, ya que no definimos ningun recurso html en esas rutas para que nuestros backend los consuman.
```

# Ejemplo Integrador 2

## Escenario: Aplicación web con persistencia y enrutamiento avanzado

En este ejemplo, desplegaremos una aplicación web simple que permite guardar mensajes en un archivo persistente. Utilizaremos los siguientes recursos avanzados de Kubernetes:

1. **PersistentVolume/PersistentVolumeClaim**: Para almacenar los mensajes de forma persistente.
    
2. **Deployment**: Para gestionar la aplicación web.
    
3. **Ingress**: Para enrutar el tráfico externo hacia nuestro servicio.

### Paso 1: Crear PersistentVolume y PersistentVolumeClaim

Definimos un volumen persistente en el nodo y su correspondiente reclamo:

```yaml
# persistent-storage.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: app-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: manual
  hostPath:
    path: "/mnt/app-data"
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: app-pvc
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

Aplicar la configuración:

```bash
kubectl apply -f persistent-storage.yaml
```

### Paso 2: Desplegar la aplicación web

Aplicación PHP que lee/escribe en `/var/www/data/messages.txt`:

```yaml
# app-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
    spec:
      containers:
      - name: php-container
        image: php:8.2-apache
        ports:
        - containerPort: 80
        volumeMounts:
        - name: app-storage
          mountPath: "/var/www/data"
      volumes:
      - name: app-storage
        persistentVolumeClaim:
          claimName: app-pvc
---
apiVersion: v1
kind: Service
metadata:
  name: web-service
spec:
  selector:
    app: web-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

Aplicar la configuración:

```bash
kubectl apply -f app-deployment.yaml
```

### Paso 3: Configurar Ingress

Crear regla de enrutamiento para acceder mediante `/app`:

```yaml
# app-ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  ingressClassName: nginx
  rules:
  - http:
      paths:
      - path: /app(/|$)(.*)
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
```

Aplicar la configuración:

```bash
kubectl apply -f app-ingress.yaml
```

### Paso 4: Preparar la aplicación

Crear archivo PHP en el volumen persistente:

```bash
sudo mkdir -p /mnt/app-data
sudo bash -c 'echo "<?php 
file_put_contents('/var/www/data/messages.txt', date('Y-m-d H:i:s') . ': ' . $_POST['message'] . PHP_EOL, FILE_APPEND);
header('Location: /'); 
?>" > /mnt/app-data/index.php'
```

Acceder al pod para configurar Apache:

```bash
kubectl exec -it <POD_NAME> -- bash
# Dentro del contenedor:
chmod 777 /var/www/data
mv /var/www/html/index.html /var/www/html/index-orig.html
ln -s /var/www/data/index.php /var/www/html/index.php
```

### Verificación y pruebas

Obtener dirección del Ingress:

```bash
kubectl get ingress app-ingress
```

Acceder a la aplicación:

```bash
kubectl exec -it <POD_NAME> -- cat /var/www/data/messages.txt
```

Destruir y recrear el deployment para ver persistencia:

```bash
kubectl delete deployment web-app
kubectl apply -f app-deployment.yaml
```

### Características clave demostradas

1. **Persistencia de datos**: Los mensajes sobreviven a reinicios/recreaciones de pods
2. **Escalabilidad horizontal**: El deployment mantiene 2 réplicas activas
3. **Enrutamiento avanzado**: El ingress maneja rutas complejas y reescritura de URLs
4. **Almacenamiento declarativo**: El PVC abstrae la implementación subyacente del almacenamiento

> NOTA:  Este ejemplo usa hostPath para simplificar la demostración. En entornos productivos se deben usar soluciones de almacenamiento en red como NFS o cloud storage (EBS, Persistent Disk).

> NOTA 2: Hasta aca se cubren los tutoriales de Kubernetes de [NullSafe Architect](https://www.youtube.com/@NullSafeArchitect) (En mayor medida). El resto del contenido de aca en adelante, es basado en la documentación oficial, foros, etc., pero con el mismo formato que lo ya revisado anteriormente (Para no perder el hilo del documento).

# Seguridad en Kubernetes

## Secrets

Los **Secrets** son objetos que almacenan información sensible como contraseñas, tokens OAuth o claves SSH de forma segura. Evitan exponer datos confidenciales en los manifiestos YAML o imágenes de contenedor.

```yaml
# secret-demo.yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-creds
type: Opaque
data:
  username: YWRtaW4=  # admin en base64
  password: cEBzc3dvcmQxMjM=  # p@ssword123 en base64
```

**Uso en un Pod mediante variables de entorno:**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-env-pod
spec:
  containers:
  - name: test-container
    image: nginx
    env:
    - name: DB_USER
      valueFrom:
        secretKeyRef:
          name: db-creds
          key: username
    - name: DB_PASS
      valueFrom:
        secretKeyRef:
          name: db-creds
          key: password
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-vol-pod
spec:
  containers:
  - name: test-container
    image: nginx
    volumeMounts:
    - name: creds-volume
      mountPath: "/etc/secrets"
      readOnly: true
  volumes:
  - name: creds-volume
    secret:
      secretName: db-creds
```

**Comandos útiles:**

```bash
# Crear secret desde archivo
kubectl create secret generic db-creds \
  --from-file=username=./user.txt \
  --from-file=password=./pass.txt

# Ver secrets
kubectl get secrets

# Inspeccionar secret (solo metadatos)
kubectl describe secret db-creds

# Decodificar valores
echo "YWRtaW4=" | base64 --decode
```

## Variables de Entorno

Las variables de entorno permiten configurar aplicaciones sin modificar su código. Para datos sensibles, deben usarse junto con Secrets.

### Uso seguro de variables

```yaml
# env-demo.yaml
apiVersion: v1
kind: Pod
metadata:
  name: env-demo
spec:
  containers:
  - name: test-container
    image: nginx
    env:
    - name: DEMO_GREETING
      value: "Hello from the environment"
    - name: DEMO_SECRET
      valueFrom:
        secretKeyRef:
          name: db-creds
          key: password
```

# Ejemplo Integrador 2.1

Extendiendo el Ejemplo Integrador 2 con seguridad:

**Agregar Secret para credenciales DB:**
 
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  db-password: cEBzc3dvcmQxMjM=
```

**Modificar Deployment para usar el Secret:**

```yaml
env:
- name: DB_PASSWORD
  valueFrom:
    secretKeyRef:
      name: db-secret
      key: db-password
```

**Consejo de seguridad:** Siempre validar configuraciones con:

```bash
kubectl apply --dry-run=server -f config.yaml
kubectl diff -f config.yaml
```

# Recursos extra (Para profundizar o acompañar):

La documentación de Kubernetes:

1. Pods https://kubernetes.io/docs/concepts/workloads/pods/
2. Namespaces https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
3. Services https://kubernetes.io/docs/concepts/services-networking/service/
4. Deployments  https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
5.  StatefulSets https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
6. Volumes https://kubernetes.io/es/docs/concepts/storage/volumes/
7. PersistentVolumes https://kubernetes.io/es/docs/concepts/storage/persistent-volumes/
8. Ingress https://kubernetes.io/docs/concepts/services-networking/ingress/
9. Ingress Controllers https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/
10. Secrets https://kubernetes.io/docs/concepts/configuration/secret/

El canal de [NullSafe Architect](https://www.youtube.com/@NullSafeArchitect)

1. [Tutorial Kubernetes I](https://www.youtube.com/watch?v=gmFSmzAWcig)
2. [Tutorial Kubernetes II](https://www.youtube.com/watch?v=gPALJhVYMz0)

El canal de [PeladoNerd](https://www.youtube.com/@PeladoNerd)

1. [Kubernetes de Novato a Pro](https://www.youtube.com/watch?v=DCoBcpOA7W4)

La documentación de k0s

1. https://docs.k0sproject.io/stable/

warp.dev (Terminal inteligente con IA)

1. https://www.warp.dev/