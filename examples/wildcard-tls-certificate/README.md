# Wildard TLS Certificate

El certificado wildcard TLS simplifica la configuración de la terminación TLS si necesita usar el mismo certificado en varios recursos de ingreso desde varios namespaces.

Normalmente, el certificado es para un subdominio (Por ejemplo *.example.com), mientras que los hosts en los recursos de ingress incluyen ese subdominio (Por ejemplo foo.example.com, bar.example.com).



## Ejemplo



### Pre-requisitos

1.  Previamente se debe contar con  un cluster de kubernetes desplegado que puedes alguna de las siguientes opciones:
   - [Minukube](https://kubernetes.io/docs/setup/learning-environment/minikube/)
   - [Kops](https://github.com/kubernetes/kops)
   - [Kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)
   - [Docker para Mac](https://docs.docker.com/docker-for-mac/#kubernetes)
   - [Kubernetes IN Docker](https://github.com/kubernetes-sigs/kind)
   - [EKS](https://docs.aws.amazon.com/es_es/eks/latest/userguide/create-cluster.html)
   - [AKS](https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough)
   - [GKE](https://cloud.google.com/kubernetes-engine/docs/)
   - [Openshift](https://access.redhat.com/documentation/en-us/openshift_container_platform/4.1/html/installing/index)

2. Contar con los repositorios de [helm](https://helm.sh/docs/using_helm/) para el despliegue de la Imagenes de Kubernetes.

3. Inicie el Ingress Crontroller con el argumento de linea de comandos wildcard-tls-secret establecido en un secreto TLS con un wildcard cert/key. 

   ##### Ejemplo:

   ```bash
   helm install --namespace nginx-ingress --name ingress stable/nginx-ingress --set rbac.create=true --set rbac.createRole=true --set rbac.createClusterRole=true --set wildcard-tls-secret=nginx-ingress/nginx-ingress-ssl
   ```

> **Nota**: El ingress controller solo admite un wildcard TLS secreto.



### Configurando terminación TLS

En el siguiente ejemplo, configuramos la terminación TLS para dos recursos de Ingress para los hosts jenkins.example-devops.com y sonar.example-devops.com

jenkins-ingress del namespace jenkins-new:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
 name: jenkins-ingress
 namespace: jenkins-new
 annotations:
   kubernetes.io/ingress.class: "nginx"
spec:
 tls:
 - hosts:
   - jenkins.example-devops.com
 rules:
 - host: jenkins.example-devops.com
   http:
     paths:
     - path: /
       backend:
         serviceName: jenkins
         servicePort: 8080
```

sonar-ingress del namespace sonar-new:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
 name: sonar-ingress
 namespace: sonar-new
 annotations:
   kubernetes.io/ingress.class: "nginx"
spec:
 tls:
 - hosts:
   - sonar.example-devops.com
 rules:
 - host: sonar.example-devops.com
   http:
     paths:
     - path: /
       backend:
         serviceName: sonarqube-sonarqube
         servicePort: 9000
```

Debido a que no hacemos referencia a ningún secreto TLS en la sección tls (no hay un campo secretName) en ambos recursos de Ingress, NGINX usará el secreto de wildcard especificado en el argumento de la línea de comando -wildcard-tls-secret.