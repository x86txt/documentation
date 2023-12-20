# kubernetes:secrets:copy

Since we cannot access secrets across namespaces, here is a quick way to copy a secret to a new namespace. This is especially handy for TLS certificates.

``` shell title="copy k8s secrets from one namespace to another"
$ kubectl get secret \<secret_name\> -n \<source namespace\> -o yaml \
  | sed s/"namespace: <source namespace>"/"namespace: <destination_namespace>"/\
  | kubectl apply -n dmz -f -
```