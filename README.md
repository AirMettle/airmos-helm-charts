##  Airmettle helm chart
Repository for the airmettle helm chart to install AirMOS software on a kubernetes cluster.

### Prerequisites
- A running kubernetes cluster.  It can be in the cloud or locally with minikube.
- Your local environment should have helm and kubectl setup to point to the cluster.
- The cluster should support a load balancer and persistent storage.
- The cloud providers all have managed kubernetes clusters than can be used.  This example used oracle container engine.
- The cluster should be setup with nodes with high cpu count.   Each AirMOS pod will be run a separate node.  The default configuration has two AirMOS pods so there should be at least two nodes.  A pod represents a single AirMOS process running the AirMOS docker container.

### Installing AirMOS with default values
To install, run these helm commands:
```
helm repo add airmos https://airmettle.github.io/airmos-helm-charts/
helm install -n airmos airmos airmos/airmos
```


### Installing AirMOS with custom values
Download and edit [values.yaml](values.yaml).  The file has instruction for modifying values such as the number of pods.

To install, run these helm commands:
```
helm repo add airmos https://airmettle.github.io/airmos-helm-charts/
helm install -n airmos airmos airmos/airmos -f values.yaml
```

### Persistent Volumes
The helm scripts create persistent storage volumes for the database and object files.  The data will persist even if the pods are restarted.
The [values.yaml](values.yaml) can be modified to set the storage class.  For oracle, by default the oci-vb storage class of attached storage is used.  If you want to use local nvme drives, https://blogs.oracle.com/cloud-infrastructure/post/using-local-persistent-volumes-with-container-engine-for-kubernetes has instructions for creating a storage class that can be used for them.

```
kubectl get pvc -n airmos
NAME                        STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
database-storage-airmos-0   Bound    csi-017d41ba-b996-49ae-89a4-d74fe4aa3ce9   50Gi       RWO            oci-bv         2m
database-storage-airmos-1   Bound    csi-4b9e6fe5-a05d-46fb-aaf3-cf4e33be2fb2   50Gi       RWO            oci-bv         20s
object-storage-airmos-0     Bound    csi-4913810e-c609-4ea6-a393-5dc74c2e4ddd   50Gi       RWO            oci-bv         2m
object-storage-airmos-1     Bound    csi-0e7817b8-3a76-4f7a-99ef-eb36f5c9f429   50Gi       RWO            oci-bv         20s
```

### Logs
AirMOS logs are written to stderr and can be viewed with standard kubectl command.

```
kubectl logs airmos-0 -n airmos

2022-10-17 00:30:41,882111(140708245640960): INFO: /home/vagrant/git/AirMOS/src/pcos_raft.c: pcosraft_set_leader(590): Setting leader for type 5 to airmos-0.airmos.airmos.svc.cluster.local:8082
```

### Pods
If the AirMOS server comes up correctly, kubectl should show the pods running

```
kubectl get pods -n airmos
NAME       READY   STATUS    RESTARTS   AGE
airmos-0   1/1     Running   0          2m57s
airmos-1   0/1     Running   0          77s
```


### Service
If the AirMOS server comes up correctly, kubectl should show the service endpoints running.  The external IP shown can then be used with a browser
to finish configuring the system.  For example, here you would open http://129.80.66.108 in a browser.
```
kubectl get services -n airmos
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)        AGE
airmos       ClusterIP      None            <none>          80/TCP         3m10s
airmos-svc   LoadBalancer   10.96.198.120   129.80.66.108   80:31039/TCP   3m10s
```

### Admin UI login
In the UI, login with Tenant: root, Hsername: admin, Password: airmos
The password is configurable in [values.yaml](values.yaml).

![Setup 1](/img/setup1.png)

### Admin tenant setup
Under Settings -> Tenants, add a new tenant and set the Total Copies to the number of AirMOS pods configured (default 2).

![Setup 2](/img/setup2.png)

### Admin account setup
User Settings -> Accounts, add an account for the tenant you created.   The values you set for API key and S3 secret will be used to make S3 queries to the cluster.

![Setup 3](/img/setup3.png)

### Calling the S3 api


### Shutting down the cluster
The AirMOS pods can be shutdown by running this command.
```
helm delete airmos -n airmos
```
