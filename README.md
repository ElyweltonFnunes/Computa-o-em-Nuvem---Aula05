# Computa-o-em-Nuvem---Aula05
Atividade prática — Kubernetes


root@controlplane:~$ ^[[200~kubectl version --client
kubectl: command not found
root@controlplane:~$ ~^[[200~kubectl version --client
~kubectl: command not found
root@controlplane:~$ 
root@controlplane:~$ kubectl get nodes
NAME           STATUS   ROLES           AGE   VERSION
controlplane   Ready    control-plane   9d    v1.37.1
node01         Ready    <none>          9d    v1.37.1
root@controlplane:~$ ~kubectl version --client

kubectl get nodes
~kubectl: command not found
NAME           STATUS   ROLES           AGE   VERSION
controlplane   Ready    control-plane   9d    v1.37.1
node01         Ready    <none>          9d    v1.37.1
root@controlplane:~$ kubectl create deployment web-aula5 --image=nginx:alpine

kubectl get deployments
deployment.apps/web-aula5 created
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
web-aula5   0/1     0            0           0s
root@controlplane:~$ kubectl get pods

kubectl get pods -o wide
NAME                         READY   STATUS    RESTARTS   AGE
web-aula5-779c6f9566-vfcf8   1/1     Running   0          43s
NAME                         READY   STATUS    RESTARTS   AGE   IP             NODE     NOMINATED NODE   READINESS GATES
web-aula5-779c6f9566-vfcf8   1/1     Running   0          43s   192.168.1.43   node01   <none>           <none>
root@controlplane:~$ kubectl describe pod $(kubectl get pods -o jsonpath='{.items[0].metadata.name}')
Name:             web-aula5-779c6f9566-vfcf8
Namespace:        default
Priority:         0
Service Account:  default
Node:             node01/172.30.2.2
Start Time:       Wed, 14 Oct 2026 23:16:18 +0000
Labels:           app=web-aula5
                  pod-template-hash=779c6f9566
Annotations:      <none>
Status:           Running
IP:               192.168.1.43
IPs:
  IP:           192.168.1.43
Controlled By:  ReplicaSet/web-aula5-779c6f9566
Containers:
  nginx:
    Container ID:   containerd://73bd9d624149c90f2e8d07345126d279a426db729b932febe4f679330d2d890b
    Image:          nginx:alpine
    Image ID:       docker.io/library/nginx@sha256:72aeb3308ef6cb3118242fb16389518923916dfa206e32d1a8377acc328b9870
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Wed, 14 Oct 2026 23:16:26 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-lsxtr (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  kube-api-access-lsxtr:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  59s   default-scheduler  Successfully assigned default/web-aula5-779c6f9566-vfcf8 to node01
  Normal  Pulling    55s   kubelet            spec.containers{nginx}: Pulling image "nginx:alpine"
  Normal  Pulled     51s   kubelet            spec.containers{nginx}: Successfully pulled image "nginx:alpine" in 4.385s (4.385s including waiting). Image size: 28826677 bytes.
  Normal  Created    51s   kubelet            spec.containers{nginx}: Container created
  Normal  Started    51s   kubelet            spec.containers{nginx}: Container started
root@controlplane:~$ kubectl get deployment,replicaset,pods
NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/web-aula5   1/1     1            1           93s

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/web-aula5-779c6f9566   1         1         1       93s

NAME                             READY   STATUS    RESTARTS   AGE
pod/web-aula5-779c6f9566-vfcf8   1/1     Running   0          93s
root@controlplane:~$ kubectl get pods

kubectl delete pod $(kubectl get pods -o jsonpath='{.items[0].metadata.name}')

kubectl get pods
NAME                         READY   STATUS    RESTARTS   AGE
web-aula5-779c6f9566-vfcf8   1/1     Running   0          2m
pod "web-aula5-779c6f9566-vfcf8" deleted from default namespace
NAME                         READY   STATUS    RESTARTS   AGE
web-aula5-779c6f9566-f44vm   1/1     Running   0          0s
root@controlplane:~$ kubectl expose deployment web-aula5 --port=80 --target-port=80 --name=web-service

kubectl get service web-service
service/web-service exposed
NAME          TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
web-service   ClusterIP   10.107.167.134   <none>        80/TCP    0s
root@controlplane:~$ ^[[200~kubectl run teste --image=curlimages/curl --rm -it --restart=Never -- \
>   curl http://web-service
kubectl: command not found
root@controlplane:~$ ~kubectl run teste --image=curlimages/curl --rm -it --restart=Never -- \
  curl http://web-service
~kubectl: command not found
root@controlplane:~$ kubectl run teste --image=curlimages/curl --rm -it --restart=Never -- \
  curl http://web-service
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, nginx is successfully installed and working.
Further configuration is required for the web server, reverse proxy, 
API gateway, load balancer, content cache, or other features.</p>

<p>For online documentation and support please refer to
<a href="https://nginx.org/">nginx.org</a>.<br/>
To engage with the community please visit
<a href="https://community.nginx.org/">community.nginx.org</a>.<br/>
For enterprise grade support, professional services, additional 
security features and capabilities please refer to
<a href="https://f5.com/nginx">f5.com/nginx</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
All commands and output from this session will be recorded in container logs, including credentials and sensitive information passed through the command prompt.
If you don't see a command prompt, try pressing enter.
Session ended, resume using 'kubectl attach teste -c teste -n default -i -t' command
pod "teste" deleted from default namespace
root@controlplane:~$ ^C
root@controlplane:~$ ^C
root@controlplane:~$ ^C
root@controlplane:~$ ^C
root@controlplane:~$ kubectl scale deployment web-aula5 --replicas=5
deployment.apps/web-aula5 scaled
root@controlplane:~$ kubectl get pods
NAME                         READY   STATUS    RESTARTS   AGE
web-aula5-779c6f9566-2wbjp   1/1     Running   0          55s
web-aula5-779c6f9566-f44vm   1/1     Running   0          6m52s
web-aula5-779c6f9566-n6l8d   1/1     Running   0          55s
web-aula5-779c6f9566-rchtq   1/1     Running   0          55s
web-aula5-779c6f9566-v6zh8   1/1     Running   0          55s
root@controlplane:~$ kubectl get pods

kubectl delete pod $(kubectl get pods -o jsonpath='{.items[0].metadata.name}')

kubectl get pods
^C
root@controlplane:~$ ^C
root@controlplane:~$ ^C
root@controlplane:~$ kubectl get pods
NAME                         READY   STATUS    RESTARTS   AGE
web-aula5-779c6f9566-2wbjp   1/1     Running   0          6m19s
web-aula5-779c6f9566-f44vm   1/1     Running   0          12m
web-aula5-779c6f9566-n6l8d   1/1     Running   0          6m19s
web-aula5-779c6f9566-rchtq   1/1     Running   0          6m19s
web-aula5-779c6f9566-v6zh8   1/1     Running   0          6m19s
root@controlplane:~$ kubectl delete pod $(kubectl get pods -o jsonpath='{.items[0].metadata.name}')
pod "web-aula5-779c6f9566-2wbjp" deleted from default namespace
root@controlplane:~$ kubectl get pods
NAME                         READY   STATUS    RESTARTS   AGE
web-aula5-779c6f9566-8znl6   1/1     Running   0          16s
web-aula5-779c6f9566-f44vm   1/1     Running   0          12m
web-aula5-779c6f9566-n6l8d   1/1     Running   0          6m59s
web-aula5-779c6f9566-rchtq   1/1     Running   0          6m59s
web-aula5-779c6f9566-v6zh8   1/1     Running   0          6m59s
root@controlplane:~$ 


