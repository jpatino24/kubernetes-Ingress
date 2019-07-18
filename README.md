# NGINX Ingress Controller Kubernetes

Este repo contiene algunos ejemplos de objetos de kubernetes Ingress para el despliegue de un Ingress Controller en un clúster de kubernetes.

### ¿Qué es un Ingress?

Ingress es un recurso de Kubernetes que le permite configurar un equilibrador de carga HTTP para aplicaciones que se ejecutan en Kubernetes, representado por uno o más Servicios. Dicho equilibrador de carga es necesario para entregar esas aplicaciones a clientes fuera del clúster Kubernetes.

El recurso Ingress soporta las siguientes características:

- **Enrutamiento basado en contenido**:
  - *Enrutamiento basado en host*. Por ejemplo, enrutar las solicitudes con el encabezado de host foo.example.com a un grupo de servicios y el encabezado de host bar.example.com a otro grupo.
  - *Enrutamiento basado en ruta*. Por ejemplo, enrutar las solicitudes con el URI que comienza con /serviceA al servicio A y las solicitudes con el URI que comienza con /serviceB al servicio B.
- **Terminación TLS/SSL**: Para cada nombre de host, como foo.example.com.

Consulte la [Guía del usuario de Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) para obtener más información sobre el recurso de Ingress.

### ¿Qué es un Ingress Controller?

El controlador de Ingress es una aplicación que se ejecuta en un clúster y configura un equilibrador de carga HTTP de acuerdo con los recursos de Ingress. El equilibrador de carga puede ser un equilibrador de carga de software que se ejecute en el clúster o un equilibrador de carga de hardware o en la nube que se ejecute externamente. Diferentes equilibradores de carga requieren diferentes implementaciones de controladores de ingreso.

> **Nota:**En el caso de NGINX, el controlador Ingress se implementa en un pod junto con el equilibrador de carga.

### NGINX Ingress Controller

El controlador NGINX Ingress funciona con NGINX y NGINX Plus y es compatible con las características estándar de Ingress: enrutamiento basado en contenido y terminación TLS/SSL.

Además, varias características de NGINX y NGINX Plus están disponibles como extensiones del recurso Ingress a través de anotaciones y el recurso ConfigMap. Además de HTTP, el controlador NGINX Ingress admite aplicaciones de equilibrio de carga Websocket, gRPC, TCP y UDP.