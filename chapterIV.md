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

#### Exposing TCP traffic.

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: echo-tcp-gateway
spec:
	selector:
		istio: ingressgateway
	servers:
	-	hosts:
			- "*"
	  port:
			number: 31400
			name: tcp-echo
			protocol: TCP
		
```
#### Obtain the tcp port from the istio-ingressgateway

> kubectl get svc -n istio-system istio-ingressgateway \
	-o jsonpath='{.spec.ports[?(@.name=="tcp")]}'

Then we need the VirtualService

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
	name: tcp-echo-vs-from-gtw
spec:
	hosts:
		- "*"
	gateways:
		- echo-tcp-gateway
	tcp:
		- match:
				- port: 31400
		  route:
				- destination:
						host: echo
						port:
							number: 8080
```

### Using sni 

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
	name: sni-pass-through-gateway
spec:
	selector:
		istio: ingressgateway
	servers:
	- port:
			number: 31400
			name: tcp-sni
			protocol: TLS
	  tls:
			mode: PASSTHROUGH
	  hosts:
			- "simple-sni=1.istioinaction.io"

```


