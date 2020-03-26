Deploys a k8s pod with a `/debug` directory that can be used to copy the contents of the cloudbuild workspace folder for debugging.

Entry point will force delete the `/debug` folder after the sleep to clear out any sensitive information.

Manual clean as per instructions below.

## Create namespace
Update namespace accordingly
```
kubectl create namespace andy-debug --dry-run -o yaml > namespace.yaml
kubectl apply -f namespace.yaml
```

## Deploy
```
kubectl apply -f debug_pod.yaml -n andy-debug
```

## Exec 
```
kubectl exec --namespace=andy-debug -it $(kubectl get pod -l "app=debug-pod" --namespace=andy-debug -o jsonpath='{.items[0].metadata.name}') -- /bin/bash
```

## Clean up
```
kubectl delete namespaces andy-debug

# to do
use pod only manifest to see if pod self destructs after sleep (vs deployment & replicaset)
```

### Cloud Build
Add this step to copy workspace to /debug folder.  Update pod name accordingly.
```
# DEBUG
- name: gcr.io/cloud-builders/gcloud
  id: debug
  entrypoint: sh
  args:
    - '-c'
    - 'kubectl cp /workspace andy-debug/debug-pod-697dff64b-r4cn9:/debug'
```