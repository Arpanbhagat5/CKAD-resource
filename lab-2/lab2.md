Tasks:

- Create a new Secret named db-credentials with the key/value pair
  DB_PASSWORD=speed
```
kubectl create secret generic db-credentials --from-env-file=secrets.txt
secret/db-credentials created

kubectl get secret db-credentials -o yaml
apiVersion: v1
data:
  DB_PASSWORD: c3BlZWQ=
kind: Secret
metadata:
  creationTimestamp: "2023-01-19T18:40:56Z"
  name: db-credentials
  namespace: ckad
  resourceVersion: "522248"
  uid: adc8d4a0-7e3c-4b2a-93f3-fc78a7f6d169
type: Opaque
```


- Create a Pod named sqlsvr that uses the Secret as environment variable named DB_PASSWORD and run the container with the image nginx
```
kubectl run sqlsrvrsecret --image=nginx --dry-run=client -o yaml > lab2.yaml

# add secrets as env using following block
env:
  - name: DB_PASSWORD
    valueFrom:
      secretKeyRef:
        name: db-credentials
        key: DB_PASSWORD
        optional: false # same as default; must exist

kubectl create -f lab2.yaml

kubectl get pod sqlsrvrsecret -o wide
NAME            READY   STATUS    RESTARTS   AGE   IP           NODE       NOMINATED NODE   READINESS GATES
sqlsrvrsecret   1/1     Running   0          22s   172.17.0.3   minikube   <none>           <none>

```

- Shell into the Pod and print out the created environment variables
```
kubectl exec sqlsrvrsecret -it -- env | grep DB
DB_PASSWORD=speed
```
