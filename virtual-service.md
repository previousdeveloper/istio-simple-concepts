## <u> Virtual Service </u>

A VirtualService resource in much the same capacity as a traditional Kubernetes Ingress resource, in that a VirtualService resource matches traffic and directs it to a Service resource.

However, a VirtualService resource can be much more specific in the traffic it matches and where that traffic is sent, it will operate on internal as well as external traffic, and offers a lot of additional functionality to manipulate traffic along the way. It can be combine with destination rule to foward traffic specific pod.

### Simple Virtual Service

```yml
kind: VirtualService
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

### Match Specific Path Virtual Service

```yml
kind: VirtualService
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