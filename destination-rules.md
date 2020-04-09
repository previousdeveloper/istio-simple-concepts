## <u> Destination Rule </u>

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

### Circuit Breaker

```yml
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: stage-product-detail-api
spec:
  host: stage-product-detail-api-ds
  trafficPolicy:
    connectionPool:
      http:
        http1MaxPendingRequests: 1
        maxRequestsPerConnection: 1
      tcp:
        maxConnections: 1
    outlierDetection:
      baseEjectionTime: 180.000s
      consecutiveErrors: 1
      interval: 1.000s
      maxEjectionPercent: 100
```

- Maximum Connections: The maximum number of connections to a backend. Any excess connection will be pending in a queue. You can modify this number by changing the maxConnections field.

- Maximum Pending Requests: The maximum number of pending requests to a backend. Any excess pending requests will be denied. You can modify this number by changing the http1MaxPendingRequests field.

- Maximum Requests: The maximum number of requests in a cluster at any given time. You can modify this number by changing the maxRequestsPerConnection field.

https://www.envoyproxy.io/docs/envoy/latest/api-v2/api/v2/cluster/outlier_detection.proto

### Any Host Match

```yml
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: stage-product-detail-api
spec:
  host: "*"
  trafficPolicy:
    loadBalancer:
      simple: LEAST_CONN
  subsets:
  - labels:
      app: product-detail-api
    name: stage
``` 
Apply any inbound host match.
