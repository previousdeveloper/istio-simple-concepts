## <u> Virtual Service </u>

A VirtualService resource in much the same capacity as a traditional Kubernetes Ingress resource, in that a VirtualService resource matches traffic and directs it to a Service resource.

Ho  ver, a VirtualService resource can be much more specific in the traffic it matches and where that traffic is sent, it will operate on internal as well as external traffic, and offers a lot of additional functionality to manipulate traffic along the way. It can be combine with destination rule to foward traffic specific pod.

### Simple Virtual Service

```yml
kind: VirtualService
apiVersion: networking.istio.io/v1beta1
metadata:
  name: stage-product-detail-api
spec:
  hosts:
  - stageproductdetailapi.trendyol.com
  http:
    route:
    - destination:
        host: stage-product-detail-api
        subset: stage
``` 

### HTTPMatchRequest Specific Uri Prefix Virtual Service

```yml
kind: VirtualService
apiVersion: networking.istio.io/v1beta1
metadata:
  name: stage-product-detail-api
spec:
  hosts:
  - stageproductdetailapi.trendyol.com
  http:
  - match:
    - uri:
        prefix: /test
    route:
    - destination:
        host: stage-product-detail-api
        subset: stage

``` 

### HTTPMatchRequest Specific Uri Regex Virtual Service

```yml
kind: VirtualService
apiVersion: networking.istio.io/v1beta1
metadata:
  name: stage-product-detail-api
spec:
  hosts:
  - stageproductdetailapi.trendyol.com
  http:
  - match:
    - uri:
        regex: /test
    route:
    - destination:
        host: stage-product-detail-api
        subset: stage

``` 

### HTTPMatchRequest Specific Header Regex Virtual Service

```yml
kind: VirtualService
apiVersion: networking.istio.io/v1beta1
metadata:
  name: stage-product-detail-api
spec:
  hosts:
  - stageproductdetailapi.trendyol.com
  http:
  - match:
    - headers:
        user-agent: 
            regex: '^.*(trendyol).*$'
    route:
    - destination:
        host: stage-product-detail-api
        subset: stage

``` 

### Timeout Virtual Service

```yml
kind: VirtualService
apiVersion: networking.istio.io/v1beta1
metadata:
  name: stage-product-detail-api
spec:
  hosts:
  - stageproductdetailapi.trendyol.com
  http:
  - match:
    - headers:
        user-agent: 
            regex: '^.*(trendyol).*$'
  - timeout: 0.200s
    route:
    - destination:
        host: stage-product-detail-api
        subset: stage
``` 

It work with if your service in mesh. To control routing for traffic bound to services outside the mesh you need to create service entry and custom virtual service.

### Network Fail Virtual Service (Injecting faults)

```yml
kind: VirtualService
apiVersion: networking.istio.io/v1beta1
metadata:
  name: stage-product-detail-api
spec:
  hosts:
  - stageproductdetailapi.trendyol.com
  http:
  - match:
    - headers:
        user-agent: 
            regex: '^.*(trendyol).*$'
  - timeout: 0.200s
    route:
    - destination:
        host: stage-product-detail-api
        subset: stage
    fault:
      abort:
        percentage:
          value: 50
        httpStatus: 5xx
``` 

### Network Delay Virtual Service (Unresponsive Application Test)

```yml
kind: VirtualService
apiVersion: networking.istio.io/v1beta1
metadata:
  name: stage-product-detail-api
spec:
  hosts:
  - stageproductdetailapi.trendyol.com
  http:
    route:
    - destination:
        host: stage-product-detail-api
        subset: stage
    fault:
      delay:
        percentage:
          value: 80
        fixedDelay: 2s  
``` 

### Url Redirect Virtual Service (Unresponsive Application Test)

```yml
kind: VirtualService
apiVersion: networking.istio.io/v1beta1
metadata:
  name: stage-product-detail-api
spec:
  hosts:
  - stageproductdetailapi.trendyol.com
  http:
    route:
    - destination:
        host: stage-product-detail-api
        subset: stage
    redirect:
      uri: /_monitoring/health
``` 

### Retry When 5xx Error Virtual Service (Unresponsive Application Test)

```yml
kind: VirtualService
apiVersion: networking.istio.io/v1beta1
metadata:
  name: stage-product-detail-api
spec:
  hosts:
  - stageproductdetailapi.trendyol.com
  http:
    route:
    - destination:
        host: stage-product-detail-api
        subset: stage
    retries:
      attempts: 3
      perTryTimeout: "2s"
      retryOn: "5xx"
``` 
Retry any request that resulted in any 500 error code, up to 3 times.


```yml
kind: VirtualService
apiVersion: networking.istio.io/v1beta1
metadata:
  name: gpim-read-service.external-timeout
  namespace: external
spec:
  hosts:
    - gpim-read-service.external.svc.stage-2sfront-idc
  http:
    - route:
        - destination:
            host: gpim-read-service
      timeout: 1.001s

``` 
Retry any request that resulted in any 500 error code, up to 3 times.