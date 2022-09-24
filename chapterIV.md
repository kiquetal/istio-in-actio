#### Traffic ingress

> Ingress refers to traffic that originates outisde the network
> and is inteded for an endpoint within the network. 

![virtual-ip-reverse-proxy](./images/virtual-ip-reverse-proxy.png)

Gateway example

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: catalog-gateway
spec:
    selector:
      istio: ingressgateway
    servers:
      - port:
          number: 80
          name: http
          protocol: HTTP
        hosts:
          - "webapp.istioinaction.io"
```

#### Check gateway and routing

    istioctl -n istio-system proxy-config \
    listener deploy/istio-ingressgateway 

#### Http traffic with TLS.

#### Create tls secrets

    kubectl create secret tls \
        --namespace istio-system \
        coolstore-gateway-certs \
        --key tls.key \
        --cert tls.crt

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: coolstore-gateway
spec:
  selector:
    istio: ingressgateway
  servers:
    - port:
        number: 443
        name: https
        protocol: HTTPS
      tls:
        mode: SIMPLE
        credentialName: coolstore-gateway-certs
      hosts:
        - "webapp.istioinaction.io"
```

