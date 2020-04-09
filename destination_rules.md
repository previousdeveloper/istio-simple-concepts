## Destination Rule

Destination rules serve as the single source of truth about which service versions are available to receive traffic from virtual services. You can use these resources to define policies that apply to traffic that is intended for a service after routing has occurred. Destination rules are describing  circuit breaking, load balancing, tls settings..

### Simple Destination Rule

```yml
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: stage-product-detail-api
spec:
  host: stage-product-detail-api
  subsets:
  - labels:
      app: product-detail-api
    name: stage
``` 

### Least Connection Rule

```yml
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: stage-product-detail-api
spec:
  host: stage-product-detail-api
  trafficPolicy:
    loadBalancer:
      simple: LEAST_CONN
  subsets:
  - labels:
      app: product-detail-api
    name: stage
``` 

### Version Based Connection Rule

```yml
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: stage-product-detail-api
spec:
  host: stage-product-detail-api
  trafficPolicy:
    loadBalancer:
      simple: LEAST_CONN
  subsets:
  - labels:
      app: product-detail-api
      version: v1
    name: stage
    trafficPolicy:
      loadBalancer:
        simple: ROUND_ROBIN
``` 


