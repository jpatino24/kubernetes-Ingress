# Host multiples en Ingress Controller

El ejemplo del ingress a continuación le indica al equilibrador de carga de respaldo que enrute las solicitudes según el encabezado del Host:

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: name-virtual-host-ingress
spec:
  rules:
  - host: foo.bar.com
    http:
      paths:
      - backend:
          serviceName: service1
          servicePort: 80
  - host: bar.foo.com
    http:
      paths:
      - backend:
          serviceName: service2
          servicePort: 80
```

El siguiente recurso de ingreso enrutará el tráfico solicitado first.bar.coma service1, second.foo.coma service2, y cualquier tráfico a la dirección IP sin un nombre de host definido en la solicitud (es decir, sin que se presente un encabezado de solicitud) service3:

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: name-virtual-host-ingress
spec:
  rules:
  - host: first.bar.com
    http:
      paths:
      - backend:
          serviceName: service1
          servicePort: 80
  - host: second.foo.com
    http:
      paths:
      - backend:
          serviceName: service2
          servicePort: 80
  - http:
      paths:
      - backend:
          serviceName: service3
          servicePort: 80
```