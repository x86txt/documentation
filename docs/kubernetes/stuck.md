# kubernetes:stuck:help

If you have k8s resources stuck in a 'Terminaing' state, these commands may help.

``` shell title="find stuck resources"
$ kubectl api-resources --verbs=list --namespaced -o name \
  xargs -n 1 kubectl get --show-kind --ignore-not-found -n <namespace>
```

``` shell title="force delete resource (namespace)"
$ kubectl delete ns --grace-period=0 --force --namespace prometheus
```

``` shell title="force delete resource (pods)"
$ kubectl delete pod --grace-period=0 --force --namespace <namespace> \
  pod1 pod2 pod3 pod4 pod5
```
