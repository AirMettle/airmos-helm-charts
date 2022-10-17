# Repository for helm charts


### Add install repo
```
helm repo add airmos https://airmettle.github.io/airmos-helm-charts/
helm install -n airmos airmos airmos/airmos
```


### Optionally install with modified values
```
helm install -n airmos airmos airmos/airmos -f values.yaml
```

### Persistent Volumes
```
kubectl get pvc -n airmos
NAME                        STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
database-storage-airmos-0   Bound    csi-017d41ba-b996-49ae-89a4-d74fe4aa3ce9   50Gi       RWO            oci-bv         2m
database-storage-airmos-1   Bound    csi-4b9e6fe5-a05d-46fb-aaf3-cf4e33be2fb2   50Gi       RWO            oci-bv         20s
object-storage-airmos-0     Bound    csi-4913810e-c609-4ea6-a393-5dc74c2e4ddd   50Gi       RWO            oci-bv         2m
object-storage-airmos-1     Bound    csi-0e7817b8-3a76-4f7a-99ef-eb36f5c9f429   50Gi       RWO            oci-bv         20s
```


### Logs
```
kubectl logs airmos-0 -n airmos

2022-10-17 00:30:41,882111(140708245640960): INFO: /home/vagrant/git/AirMOS/src/pcos_raft.c: pcosraft_set_leader(590): Setting leader for type 5 to airmos-0.airmos.airmos.svc.cluster.local:8082
```

### Pods

```
kubectl get pods -n airmos
NAME       READY   STATUS    RESTARTS   AGE
airmos-0   1/1     Running   0          2m57s
airmos-1   0/1     Running   0          77s
```


### Service
```
kubectl get services -n airmos
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)        AGE
airmos       ClusterIP      None            <none>          80/TCP         3m10s
airmos-svc   LoadBalancer   10.96.198.120   129.80.66.108   80:31039/TCP   3m10s
```

![Setup 1](/img/setup1.png)
![Setup 2](/img/setup2.png)
![Setup 3](/img/setup3.png)

```
helm delete airmos -n airmos
```
