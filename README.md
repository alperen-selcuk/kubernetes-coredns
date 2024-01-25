# kubernetes-coredns

you can manipulate coredns with configmap.

coredns is the default dns solution for kubernetes cluster. all DNS request firstly going to throuh this resource.  first you need create custom configmap


## forward domain zone

you can send all queries for domain.

```
apiVersion: v1
kind: Configmap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  devopsdude.forward: |-
    devops.alperen.com:53 {
      errors
      forward . 10.232.3.4
  }
```

## override host

you can add custom DNS record on coredns

```
apiVersion: v1
kind: Configmap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  devopsdude.override: |-
    hosts {
      10.10.10.10 web.devopsdude.com
    }
```
