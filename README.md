Laboratorio: Despliegue de Aplicaciones con Kubernetes en AWS EC2
Este repositorio contiene la resolución del laboratorio de orquestación utilizando una instancia AWS EC2 (Ubuntu), Minikube con driver de Docker y manifiestos YAML.

1. Objetos de Kubernetes Utilizados
Pod (mi-pod-nginx): Unidad mínima de ejecución que contiene el contenedor de Nginx.

Deployment (mi-deployment): Orquestador que garantiza la alta disponibilidad. Se configuró inicialmente con 2 réplicas y luego se escaló a 4.

Service (mi-servicio): Recurso de tipo NodePort que expone la aplicación en el puerto 30007 para permitir el acceso externo.

ConfigMap (mi-config): Almacena variables de entorno no sensibles ("Hola desde Kubernetes en AWS").

Secret (mi-secret): Almacena información sensible (password) de forma codificada en Base64.

2. Comandos Principales Ejecutados
Preparación y Despliegue
Bash
minikube start --driver=docker
kubectl apply -f pod-nginx.yaml
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
Gestión y Seguridad
Bash
kubectl create configmap mi-config --from-literal=mensaje="..."
kubectl create secret generic mi-secret --from-literal=password="..."
kubectl scale deployment mi-deployment --replicas=4
kubectl set image deployment/mi-deployment nginx=nginx:latest
3. Problemas Encontrados y Soluciones
Problema 1: Conexión denegada en localhost.

Contexto: Al intentar curl http://localhost:30007, la conexión fue rechazada.

Solución: Se identificó que al usar Minikube con el driver de Docker, el puerto se expone en la IP del nodo. Se resolvió usando minikube ip para obtener la dirección 192.168.49.2 y realizar el acceso exitoso.

Problema 2: Acceso externo desde el navegador.

Solución: Se configuró el Security Group de AWS para permitir tráfico TCP en el puerto 30007. Adicionalmente, se utilizó kubectl port-forward --address 0.0.0.0 para mapear el servicio a la IP pública de la EC2.

4. Evidencias del Reto
(Aquí debes insertar los enlaces a tus capturas de pantalla)

Pods en ejecución (4 réplicas):

Servicio activo (curl exitoso):

Configuración de AWS:

5. Reflexión Final
¿Por qué usar Deployment en lugar de Pod? Porque el Pod no se reinicia si falla. El Deployment garantiza que el número de réplicas deseado siempre esté funcionando (autosanación).

¿Qué problema resuelve un Service? La volatilidad de las IPs de los Pods. Proporciona un punto de acceso (IP y puerto) estable y balancea la carga entre las réplicas.

¿Cómo se relaciona esto con AWS EC2? EC2 provee la infraestructura (servidor físico/virtual) y Kubernetes gestiona el despliegue lógico y la escalabilidad de las aplicaciones sobre esa infraestructura.

¿Qué pasaría si un Pod falla? El Deployment lo detecta inmediatamente y crea un nuevo Pod para reemplazarlo, manteniendo la disponibilidad del servicio.
