# kubernetes-coredns

![image](https://github.com/alperen-selcuk/kubernetes-coredns/assets/78741582/c6c232b6-2133-4937-8fa8-746418b0fb5b)


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
  devopsdude.forward.server: |-
    alperen.com:53 {
      errors
      forward . 10.200.12.10 <different dns server ip>
  }
```

with this configuration all x.alperen.com DNS go to 10.200.12.10 DNS server

## override host

you can add custom DNS record.

```
apiVersion: v1
kind: Configmap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  devopsdude.override: |-
    hosts {
      10.10.10.10 web.alperen.com
      23.23.23.23 test.alperen.com
    }
```

with this configuration you can add multiple host records.

## both configuration

you can use both config same configmap

```
apiVersion: v1
kind: Configmap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  devopsdude.forward.server: |-
    alperen.com:53 {
      errors
      forward . 10.200.12.10 <different dns server ip>
  }
  devopsdude.override: |-
    hosts {
      10.10.10.10 web.alperen.com
      23.23.23.23 test.alperen.com
    }
```

this custom configuration need to mention on coredns Corefile. this Corefile also a configmap on kube-system



```
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns
  namespace: kube-system
data:
  Corefile: |
    .:53 {
        errors
        ready
        health {
          lameduck 5s
        }
        kubernetes cluster.local in-addr.arpa ip6.arpa {
          pods insecure
          fallthrough in-addr.arpa ip6.arpa
          ttl 30
        }
        prometheus :9153
        forward . /etc/resolv.conf
        cache 30
        loop
        reload
        loadbalance
        import custom/*.override
    }
    import custom/*.server
```

this Corfile like this. you need to import with wildcard to catch our custom records.
