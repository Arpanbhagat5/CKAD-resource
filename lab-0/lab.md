Tasks:

- Create an namespace named CKAD
```
kubectl create namespace ckad
```

- Set namespace as ckad
```
kubectl config set-context --current --namespace ckad
```

- Check namespace
```
kubectl get pods
```

- Create a Pod named Pod1 in ckad ns with the image nginx:2.3.5 and expose port 80
```
kubectl run --help
kubectl run pod1 --image=nginx:2.3.5 --port=80 --dry-run=client -o yaml > lab0.yaml
kubectl create -f lab0.yaml
```

- Verify the Pod is running
```
kubectl get pods
NAME   READY   STATUS         RESTARTS   AGE
pod1   0/1     ErrImagePull   0          8s

kubectl describe pod pod1
Events
...
  Warning  Failed     101s (x4 over 3m18s)  kubelet            Failed to pull image "nginx:2.3.5": rpc error: code = Unknown desc = Error response from daemon: manifest for nginx:2.3.5 not found: manifest unknown: manifest unknown
...
```

- If not, fix Retrieve the IP address of the Pod

  Remove version from yaml

  image: nginx:2.3.5  ->  image: nginx


```
get pod -o wide
NAME   READY   STATUS    RESTARTS      AGE   IP           NODE       NOMINATED NODE   READINESS GATES
pod1   1/1     Running   1 (10m ago)   20m   172.17.0.3   minikube   <none>           <none>
```

- Run a temporary pod using busybox, shell into it and run a wget against Pod1
```
run busybox --image=busybox --rm -it -- sh
If you don't see a command prompt, try pressing enter.
/ # wget 172.17.0.3
Connecting to 172.17.0.3 (172.17.0.3:80)
saving to 'index.html'
index.html           100% |**********************************************************************************************************************************|   615  0:00:00 ETA
'index.html' saved
/ #
Session ended, resume using 'kubectl attach busybox -c busybox -i -t' command when the pod is running
pod "busybox" deleted

```
- View the logs of Pod1
```
kubectl logs pod pod1 
...
172.17.0.4 - - [19/Jan/2023:17:19:40 +0000] "GET / HTTP/1.1" 200 615 "-" "Wget" "-"
```
- Delete Pod1 and the namcespace CKAD
```
kubectl delete -f lab0.yaml
kubectl delete namespace ckad
```
