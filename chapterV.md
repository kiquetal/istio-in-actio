#### Traffic control

Difference between deployment and release

> Releasing code mean accepting live traffic
> Deployment means when new code is added to the codebase.
>

#### Using flagger

> Relies on prometheous to monitor the application.

#### Installation of flagger.

    helm install flagger flagger/flagger\
    --namespace istio-system \
        --set crd.create=false \
        --set meshProvider=istio \
        --set metricsServer=http://prometheus:9090

```yaml
apiVersion: flagger.app/v1beta1
kind: Canary
metadata:
  name: catalog-release
  namespace: istioinaction
spec:
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: catalog
  progressDeadlineSeconds: 60
  service:
    port: 80
    name: catalog
    targetPort: 8080
    host: catalog
    gateways:
      - mesh
  analysis:
    interval: 30s
    threshold: 5
    maxWeight: 50
    stepWeight: 5
    metrics:
      - name: request-success-rate
        threshold: 99
        interval: 1m
      - name: request-duration
        threshold: 500
        interval: 30s
    
```
#### Test deployments

    while true; do curl  http://catalog.istioinaction.io \
     -H "Host: catalog.istioinaction.io"; sleep 1; done

#### Traffic mirroring

