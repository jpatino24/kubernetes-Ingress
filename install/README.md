# Instalar Ingress Controller

## Pre-requisitos

- Asegúrese de tener acceso a la imagen de ingress controller.
- La instalación a continuación asume que se cuenta con un clúster de Kubernetes previamente configurado.
- Existen varios métodos de instalación. En la siguiente guía veremos el proceso haciendo uso del administrador de aplicaciones de kubernetes [helm](https://helm.sh/docs/).

## 1. Instalación helm.

1. Instalar el cliente de helm.Descargar los binarios de helm y tiller según la plataforma. para mayor detalle revise la [documentación de instalación de helm.](https://helm.sh/docs/using_helm/#installing-helm)

2. Cree una cuenta de servicio para acceso de tiller con RBAC: 

   rbac-config.yaml

   ```yaml
   apiVersion: v1
   kind: ServiceAccount
   metadata:
     name: tiller
     namespace: kube-system
   ---
   apiVersion: rbac.authorization.k8s.io/v1beta1
   kind: ClusterRoleBinding
   metadata:
     name: tiller
   roleRef:
     apiGroup: rbac.authorization.k8s.io
     kind: ClusterRole
     name: cluster-admin
   subjects:
     - kind: ServiceAccount
       name: tiller
       namespace: kube-system
   ```

3. A continuación, aplique la configuración:

   ```bash
   kubectl create -f rbac-config.yaml
   ```

4. Instalar tiller usando la herramienta helm:

   ```bash
   helm init --service-account tiller
   ```

   ```bash
   helm init --upgrade --service-account tiller
   ```

5. Actualizar los repositorios de helm:

   ```bash
   helm repo update
   ```

6. Si su clúster de kubernetes es EKS se debe ejecutar el siguiente comando:

   ```bash
   kubectl -n kube-system patch deployment tiller-deploy -p '{"spec": {"template": {"spec": {"automountServiceAccountToken": true}}}}'
   ```



## 2. Crear el namespace para el ingress controller.

1. Crear el namespace:

   ```bash
   kubectl create namespace ingress-controller
   ```

## 3. Instalar ingress controller con el administrador de aplicaciones de kubernetes helm.

1. Instalar el ingress controller del repositorio stable:

   ```bash
   helm install --namespace ingress-controller --name ingress stable/nginx-ingress --set rbac.create=true --set rbac.createRole=true --set rbac.createClusterRole=true --set controller.stats.enabled=true
   ```

   > Para más información sobre las banderas y versiones del stable/nginx-ingress ver la documentación oficial en [kubeapps hub](https://hub.kubeapps.com/charts/stable/nginx-ingress).

## 4. Crear los registros de ingress de kubernetes.

Se pueden ver algunos ejemplos de ingress en kubernetes-Ingress/examples.

## 5. Crear secretos de kuberneter para almacenar los certificados SSL.

El siguiente ejemplo muestra como crear un secreto para almacenar un certificados ssl que va a ser usado por un ingress controller.

```bash
kubectl create secret tls jenkins-ingress-ssl --key jenkins.key --cert jenkins.crt -n jenkins-example
```

## 6. Ingresar al status page

Para NGINX, puede acceder a la página  [stub_status page](http://nginx.org/en/docs/http/ngx_http_stub_status_module.html):

1. Stub_status está deshabilitado por defecto. Asegúrese de que el argumento de línea de comando controller.stats esté establecido en true.

2. Agregue el argumento controller.stats.service.type establecido en LoadBalancer.
3. Ingrese al balanceador del servicio controller stats.