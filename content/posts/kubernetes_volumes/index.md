---
title: "Kubernetes Volumes Deep Dive"
date: 2024-09-13T00:02:12+05:30
draft: false
tags: ["Kubernetes", "Volumes"]
---

### References

https://stackoverflow.com/questions/45511339/kubernetes-minikube-with-local-persistent-storage

https://platform9.com/blog/tutorial-dynamic-provisioning-of-persistent-storage-in-kubernetes-with-minikube/

https://stackoverflow.com/questions/66355331/deployment-cannot-find-pvc-on-minikube

minikube supports hostPath mount out of box -

`minikube mount D:/vscode/devops:/usr/volume`

`kubectl api-resources --namespaced=false # returns resources which are cluster-wide`

`kubectl api-resources --namespaced=true # resources which are ns scoped`

PVCs request from storage classes - https://kubernetes.io/docs/concepts/storage/storage-classes/

PVC are ns scoped so 2 pvc can have same name if in different ns.
for minikube generate another storage class as classic uses a specific path

## Setting up own PV and VPC

For creating own **pv, pvc** you need to create a storage class as well as only then it will use the pv you creted otherwise will create **dynamic allocation**.

---

In Kubernetes, each Persistent Volume Claim (PVC) must have a unique name within its own namespace. However, it is possible for multiple PVCs from different namespaces to claim the same Persistent Volume (PV).

Here's a breakdown of these concepts:

1. **PVC Names within a Namespace**: Each PVC in a specific namespace must have a unique name within that namespace. This uniqueness is enforced by Kubernetes, as PVC names are namespaced resources. You cannot have two PVCs with the same name in the same namespace.

2. **Multiple PVCs from Different Namespaces Claiming the Same PV**: Multiple PVCs from different namespaces can indeed claim the same PV. When a PVC is created and specifies a storage class and access mode that match a PV's settings, Kubernetes will attempt to bind the PVC to an available PV, regardless of the PVC's namespace. This allows multiple Pods running in different namespaces to access the same shared storage.

Here's an example scenario:

- Namespace `namespace-1` has a PVC named `my-pvc` that claims a specific PV.
- Namespace `namespace-2` can also have a PVC named `my-pvc` that claims the same PV, assuming the storage class, access mode, and capacity requirements match.

It's important to be cautious when sharing PVs between namespaces, as it can lead to potential security and access control concerns. You need to ensure that your RBAC (Role-Based Access Control) and security policies are appropriately configured to control access to the shared PV.

Additionally, when PVCs from different namespaces claim the same PV, they effectively share the same underlying storage. Any changes or writes made to that storage are visible to all Pods using the PV. This shared storage can be useful in some scenarios, like sharing configuration data or reference data, but it can also lead to unexpected interactions between different applications if not managed carefully.

---

In Kubernetes, a StorageClass is an important concept that provides a way to dynamically provision and manage storage for Persistent Volume Claims (PVCs). It allows you to abstract the underlying storage infrastructure, making it easier to manage and provision storage in a consistent manner across various storage solutions. Here are the key details and important concepts related to StorageClasses:

1. **Definition of StorageClass**:

   - A StorageClass is a Kubernetes object that defines a set of storage characteristics and properties for dynamically provisioning storage.
   - It specifies the storage provider, access modes, reclaim policies, and any additional parameters required for creating Persistent Volumes (PVs).

2. **Dynamic Provisioning**:

   - One of the primary purposes of a StorageClass is to enable dynamic provisioning of PVs. When a PVC requests storage from a StorageClass, Kubernetes automatically provisions a PV that meets the criteria defined in the StorageClass.
   - This simplifies the process of managing storage because you don't need to manually create PVs for each application.

3. **Access Modes**:

   - A StorageClass can define one or more access modes for PVs. Common access modes are ReadWriteOnce (RWO), ReadOnlyMany (ROX), and ReadWriteMany (RWX).
   - RWO allows the PV to be mounted as read-write by a single node, ROX allows multiple nodes to mount it as read-only, and RWX allows multiple nodes to mount it as read-write.

4. **Provisioner**:

   - The `provisioner` field in the StorageClass specifies the storage provider or driver responsible for provisioning the PVs.
   - For example, if you're using a cloud provider, the provisioner might be the cloud storage system (e.g., AWS EBS, Google Cloud Persistent Disks). For on-premises or local clusters, it might be a local storage driver (e.g., local-path provisioner).

5. **Reclaim Policies**:

   - The `reclaimPolicy` field determines what happens to the PV and its associated data when the PVC is deleted or released.
   - Common policies are:
     - `Retain`: The PV is not deleted, and data is retained after the PVC is deleted. Manual cleanup is required.
     - `Delete`: The PV and its data are deleted when the PVC is deleted.
     - `Recycle`: Deprecated and not recommended. It used to perform basic cleanup operations.

6. **Parameters**:

   - Some StorageClasses allow you to specify additional parameters, such as volume type, size, and other storage-specific configurations.
   - These parameters are provider-specific and depend on the underlying storage system.

7. **Default StorageClass**:

   - You can mark a StorageClass as the default for the cluster, which means if a PVC doesn't specify a StorageClass, it will use the default one.
   - This simplifies PVC definitions by eliminating the need to specify a StorageClass in each claim.

8. **Distributed Storage**:

   - StorageClasses can also be used with distributed file systems and networked storage solutions to provide shared storage across multiple Pods and nodes.
   - Examples include NFS, Ceph, GlusterFS, and more.

9. **Security and Permissions**:
   - Ensure that RBAC (Role-Based Access Control) permissions are set up correctly to control who can create and use StorageClasses, as well as create and delete PVCs.

StorageClasses are a fundamental building block for managing storage in Kubernetes. They abstract the complexities of various storage providers, enable dynamic provisioning, and help ensure efficient and consistent use of storage resources across your cluster. Properly configuring and using StorageClasses is essential for effective storage management in Kubernetes.

## Types of volumes

- **emptyDir** - Till lifetime of pod

```yml
apiVersion: v1
kind: Pod
metadata:
  name: redis
spec:
  containers:
    - name: redis
      image: redis
      volumeMounts:
        - name: redis-storage
          mountPath: /data/redis
  volumes:
    - name: redis-storage
      emptyDir: {}
```

- **hostpath** - mounts a file or directory from the host node's filesystem into your Pod. This is not something that most Pods will need, but it offers a powerful escape hatch for some applications.
- **configmap** - A ConfigMap is always mounted as **readOnly**. A container using a ConfigMap as a subPath volume mount will not receive ConfigMap updates. Text data is exposed as files using the UTF-8 character encoding. For other character encodings, use **binaryData**.
- **downwardAPI** - makes downward API data available to applications. Within the volume, you can find the exposed data as read-only files in plain text format.
- **local** - A local volume represents a mounted local storage device such as a disk, partition or directory. Local volumes can only be used as a statically created PersistentVolume. Dynamic provisioning is not supported. Compared to hostPath volumes, local volumes are used in a durable and portable manner without manually scheduling pods to nodes. The system is aware of the volume's node constraints by looking at the node affinity on the PersistentVolume.**Problem with local volume when node becomes unhealthy**

```yml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: example-pv
spec:
  capacity:
    storage: 100Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Delete
  storageClassName: local-storage
  local:
    path: /mnt/disks/ssd1
  nodeAffinity:
    required:
      nodeSelectorTerms:
        - matchExpressions:
            - key: kubernetes.io/hostname
              operator: In
              values:
                - example-node
```

- **nfs** - persistent unlike emptyDir. NFS volume can be pre-populated with data, and that data can be shared between pods.
- **serviceaccounttoken** - https://kubernetes.io/docs/concepts/storage/projected-volumes/#serviceaccounttoken
- **secret** - A secret volume is used to pass sensitive information, such as passwords, to Pods. You can store secrets in the Kubernetes API and mount them as files for use by pods without coupling to Kubernetes directly. **secret volumes are backed by tmpfs (a RAM-backed filesystem) so they are never written to non-volatile storage.** Note -
  - **They must be created before attached**,
  - they are always mounted as readOnly,
  - container using a Secret as a subPath volume mount will not receive Secret updates.

---

### subpath volume -

The following example shows how to configure a Pod with a LAMP stack (Linux Apache MySQL PHP) using a single, shared volume. This sample **subPath** configuration is **not recommended** for production use.

**The PHP application's code and assets map to the volume's html folder and the MySQL database is stored in the volume's mysql folder.**

```yml
apiVersion: v1
kind: Pod
metadata:
  name: my-lamp-site
spec:
  containers:
    - name: mysql
      image: mysql
      env:
        - name: MYSQL_ROOT_PASSWORD
          value: "rootpasswd"
      volumeMounts:
        - mountPath: /var/lib/mysql
          name: site-data
          subPath: mysql
    - name: php
      image: php:7.0-apache
      volumeMounts:
        - mountPath: /var/www/html
          name: site-data
          subPath: html
  volumes:
    - name: site-data
      persistentVolumeClaim:
        claimName: my-lamp-site-data
```

## VolumeClaimTemplates

https://kubernetes.io/docs/tutorials/stateful-application/basic-stateful-set/

```yml
apiVersion: v1
kind: Service
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  ports:
    - port: 80
      name: web
  clusterIP: None
  selector:
    app: nginx
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  serviceName: "nginx"
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: registry.k8s.io/nginx-slim:0.8
          ports:
            - containerPort: 80
              name: web
          volumeMounts:
            - name: www
              mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
    - metadata:
        name: www
      spec:
        accessModes: ["ReadWriteOnce"]
        resources:
          requests:
            storage: 1Gi
```

Here you can see a volumeClaimTemplates which otherwise is persistentVolumeClaim =>

```yml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - "ReadWriteOnce"
  resources:
    requests:
      storage: 1Gi
  selector:
    matchLabels:
      pv-label: pv001

---
apiVersion: v1
kind: Pod
metadata:
  name: pv-pod1
spec:
  containers:
    - name: pv-pod1
      image: nginx
      resources:
        limits:
          cpu: 100m
          memory: 128Mi
        requests:
          cpu: 50m
          memory: 64Mi
      ports:
        - containerPort: 80
          name: http-port
      volumeMounts:
        - mountPath: "/data"
          name: my-pv-volume # match volume name
  volumes:
    - name: my-pv-volume
      persistentVolumeClaim:
        claimName: my-pvc # match claim name
```

**Reference -**
https://www.alibabacloud.com/help/en/ack/ack-managed-and-ack-dedicated/user-guide/enable-a-statefulset-to-support-persistent-storage#:~:text=PVCs%20and%20PVs%20can%20be,deployed%20for%20the%20StatefulSet%20application.

**PVCs and PVs can be automatically created based on VolumeClaimTemplates**

**Note** - volumeClaimTemplates represents a type of template that the system uses to create PVCs. The **number of PVCs equals the number of replicas** that are deployed for the StatefulSet application. The configurations of these PVCs are the same except for the PVC names.

---

**Note -**
Expose pod information via **env** as

- **fieldRef** in **spec.env**
- **secretRef** - get value from secrets
- **resourceFieldRef** - get value from resources - **limits.cpu**

https://kubernetes.io/docs/tasks/inject-data-application/environment-variable-expose-pod-information/

### Use Pod fields as values for environment variables

```yml
apiVersion: v1
kind: Pod
metadata:
  name: dapi-envars-fieldref
spec:
  containers:
    - name: test-container
      image: registry.k8s.io/busybox
      command: ["sh", "-c"]
      args:
        - while true; do
          echo -en '\n';
          printenv MY_NODE_NAME MY_POD_NAME MY_POD_NAMESPACE;
          printenv MY_POD_IP MY_POD_SERVICE_ACCOUNT;
          sleep 10;
          done;
      env:
        - name: MY_NODE_NAME
          valueFrom:
            fieldRef:
              fieldPath: spec.nodeName
        - name: MY_POD_NAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        - name: MY_POD_NAMESPACE
          valueFrom:
            fieldRef:
              fieldPath: metadata.namespace
        - name: MY_POD_IP
          valueFrom:
            fieldRef:
              fieldPath: status.podIP
        - name: MY_POD_SERVICE_ACCOUNT
          valueFrom:
            fieldRef:
              fieldPath: spec.serviceAccountName
  restartPolicy: Never
```

### Use container fields as values for environment variables

```yml
apiVersion: v1
kind: Pod
metadata:
  name: dapi-envars-resourcefieldref
spec:
  containers:
    - name: test-container
      image: registry.k8s.io/busybox:1.24
      command: ["sh", "-c"]
      args:
        - while true; do
          echo -en '\n';
          printenv MY_CPU_REQUEST MY_CPU_LIMIT;
          printenv MY_MEM_REQUEST MY_MEM_LIMIT;
          sleep 10;
          done;
      resources:
        requests:
          memory: "32Mi"
          cpu: "125m"
        limits:
          memory: "64Mi"
          cpu: "250m"
      env:
        - name: MY_CPU_REQUEST
          valueFrom:
            resourceFieldRef:
              containerName: test-container
              resource: requests.cpu
        - name: MY_CPU_LIMIT
          valueFrom:
            resourceFieldRef:
              containerName: test-container
              resource: limits.cpu
        - name: MY_MEM_REQUEST
          valueFrom:
            resourceFieldRef:
              containerName: test-container
              resource: requests.memory
        - name: MY_MEM_LIMIT
          valueFrom:
            resourceFieldRef:
              containerName: test-container
              resource: limits.memory
  restartPolicy: Never
```

## Downward API to expose information

https://kubernetes.io/docs/tasks/inject-data-application/downward-api-volume-expose-pod-information/

## Volume claim templates

You can use these templates to dynamically specify Persistent Volumes (PVs) that will be dynamically provisioned when Pods are created.

```yml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  serviceName: "web"
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
  volumeClaimTemplates:
    - metadata:
        name: data
      spec:
        accessModes: ["ReadWriteOnce"]
        resources:
          requests:
            storage: 1Gi
```

When the StatefulSet creates Pods, it uses these templates to dynamically provision Persistent Volumes. The volume's name will be based on the name of the StatefulSet and the index of the Pod (e.g., web-0, web-1, etc.). So, **if you have three replicas, it will create three PVCs: web-0-data, web-1-data, and web-2-data.**

Make sure your cluster has a **storage class configured that supports dynamic provisioning for this to work.**

## PV and PVC explained

- **PV** is piece of storage in cluster provisioned by **admin** or **dynamically provisioned by storage classes**
- **PVC is request for storage by user.** It is similar to pod where like pod resources, claim request **specific size and access modes**(ReadWriteOnce, ReadOnlyMany, ReadWriteMany etc)
- While PVC consume abstract storage resources, users need PV with varying properties related to access and performance(storage class like gp2, gp3, custom)

Types of PV -

- **Static** - Created by admins.
- **Dynamic** - When none of static matches user's PVC, cluster will try to dynamically provision volume for the pvc
