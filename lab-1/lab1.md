Tasks:

- Create an namespace named CKAD
```
kubectl create namespace ckad
```

- Set namespace as ckad
```
kubectl config set-context --current --namespace ckad
```

- Create a new file named config.txt with the following environment variables as key/value pairs 
  DB URL-localhost:1433 and DB USERNAME-speed
```
DB_URL=localhost:1433
DB_USERNAME=speed
```

- Create a new configmap named db-config
```
kubectl create configmap db-config --from-file=config.txt  // creates with key=filename and value as file content
kubectl create configmap db-config --from-env-file=config.txt  // creates with keys as present in file

kubectl get configmap db-config -o yaml

apiVersion: v1
data:
  DB_URL: localhost:1433
  DB_USERNAME: speed
kind: ConfigMap
metadata:
  creationTimestamp: "2023-01-19T18:20:15Z"
  name: db-config
  namespace: ckad
  resourceVersion: "521366"
  uid: a4ff440b-e60c-45f4-9462-6025ccfadcd5
```

- Create a pod named sqlsvr that uses the environment variables from the configmap and runs a container using image of nginx
```
# get base yaml
kubectl run sqlsrvr --image=nginx --dry-run=client -o yaml > lab1.yaml

# add following to base yaml >> under container
envFrom:
    - configMapRef:
        name: db-config

kubectl create -f lab1.yaml

kubectl get pod sqlsrvr -o wide
NAME      READY   STATUS    RESTARTS   AGE     IP           NODE       NOMINATED NODE   READINESS GATES
sqlsrvr   1/1     Running   0          2m31s   172.17.0.3   minikube   <none>           <none>

```

- Shell into the pod and display the created environment variables.
```
kubectl exec sqlsrvr -it -- sh
printenv

OR

kubectl exec sqlsrvr -it -- env | grep DB
DB_URL=localhost:1433
DB_USERNAME=speed

```