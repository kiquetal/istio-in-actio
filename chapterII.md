### First steps with Istio.

##### Installation

>  curl -L https://istio.io/downloadIstio | ISTIO_VERSION=1.13.0 sh -

cd istio-1.13.0

bin/istioctl version

##### Control Plane

- APIs for operators to specify desired routing/resilience behavior.
- APIs for the data plane to consume their configuration.
- A service discovery abstraction for the data plane.
- APIs for specifyng usage policies
- Certificate issuance and rotation
- Workload identity assignment
- Unified telemetry collection and reporting
- Service-proxy sidecar injection
- Specification of network boundaries and how to access them.

![control-plane](./images/control-plane.png)

![cert-proxy](./images/cert-proxy.png)

#### Routing Rules

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: catalog
spec:
  host: catalog
  subsets:
    - name: version-v1
      labels:
        version: v1
    - name: version-v2
      labels:
        version: v2
```

#### VirtualService

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: catalog
spec:
  hosts:
    - catalog
  http:
    - route:
      - destination:
          host: catalog
          subset: version-v1
```

`spec.host` is the user-addressable

`spec.gateway` : use Gateway to controll how the traffic flows

---

### Summary

- The sidecar proxy can be injected manually into YAML using
istioctl kube-inject
- In namespaces labeled with `istio-injection=enabled` the proxies
- are automatically injected into `newly` created pods

