# kubernetes:\shell:container

This is how to get a shell to a specific container within a pod.

``` shell title=""
$ kubectl exec -i -t my-pod --container my-container -- /bin/bash
```
