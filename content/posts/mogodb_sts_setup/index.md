---
title: "MongoDB StatefulSet Setup"
date: 2024-07-11T10:26:12+05:30
draft: false
tags: ["Kubernetes", "MongoDB", "StatefulSet"]
---

Creating a MongoDB Replica Set with authentication using StatefulSets involves several steps. Below is a manifest example for deploying a MongoDB Replica Set with authentication using StatefulSets in Kubernetes. This assumes you have a Kubernetes cluster set up and `kubectl` configured.

1. **Create a Secret for MongoDB Authentication:**

   Create a Kubernetes Secret to store the MongoDB admin credentials. You can encode the username and password using `echo -n 'yourpassword' | base64`:

   ```yaml
   apiVersion: v1
   kind: Secret
   metadata:
     name: mongodb-secret
   type: Opaque
   data:
     username: <base64-encoded-username>
     password: <base64-encoded-password>
   ```

   Apply the secret using `kubectl apply -f secret.yaml`.

2. **Create a ConfigMap for MongoDB Configuration:**

   Create a ConfigMap to store the MongoDB configuration file:

   ```yaml
   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: mongodb-config
   data:
     mongo-configdb-keyfile: |
       systemLog:
         destination: file
         path: "/var/log/mongodb/mongod.log"
       storage:
         dbPath: "/data/db"
       net:
         bindIp: 0.0.0.0
       replication:
         replSetName: rs0
       security:
         keyFile: /var/mongo-keyfile/mongo-keyfile
   ```

   Apply the ConfigMap using `kubectl apply -f configmap.yaml`.

3. **Create a StatefulSet for MongoDB:**

   ```yaml
   apiVersion: apps/v1
   kind: StatefulSet
   metadata:
     name: mongodb
   spec:
     serviceName: "mongodb"
     replicas: 3
     selector:
       matchLabels:
         app: mongodb
     template:
       metadata:
         labels:
           app: mongodb
       spec:
         containers:
         - name: mongodb
           image: mongo:latest
           ports:
           - containerPort: 27017
           volumeMounts:
           - name: mongo-data
             mountPath: /data/db
           - name: mongo-keyfile
             mountPath: /var/mongo-keyfile
             readOnly: true
           - name: config-volume
             mountPath: /etc/mongo-config
     volumeClaimTemplates:
     - metadata:
         name: mongo-data
       spec:
         accessModes: ["ReadWriteOnce"]
         resources:
           requests:
             storage: 1Gi
     volumeClaimTemplates:
     - metadata:
         name: mongo-keyfile
       spec:
         accessModes: ["ReadWriteOnce"]
         resources:
           requests:
             storage: 1Gi
     volumes:
     - name: config-volume
       configMap:
         name: mongodb-config
   ```

   Apply the StatefulSet using `kubectl apply -f statefulset.yaml`.

4. **Initialize the Replica Set:**

   After the StatefulSet is created, you need to initialize the MongoDB Replica Set. You can use a temporary pod for this:

   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: init-mongodb
   spec:
     containers:
       - name: init-mongodb
         image: mongo:latest
         command:
           - bash
           - "-c"
           - |
             sleep 10; \
             mongo mongodb://mongodb-0.mongodb:27017 \
             --eval 'rs.initiate({_id: "rs0", members: [{_id: 0, host: "mongodb-0.mongodb:27017"}, {_id: 1, host: "mongodb-1.mongodb:27017"}, {_id: 2, host: "mongodb-2.mongodb:27017"}]})'
         volumeMounts:
           - name: mongo-keyfile
             mountPath: /var/mongo-keyfile
             readOnly: true
   ```

   Apply the init pod using `kubectl apply -f init-pod.yaml`.

5. **Accessing the Replica Set:**

   Once the Replica Set is initialized, you can access it using the service name (`mongodb`) from within the cluster. You can also set up external access if needed.

Make sure to adjust the configuration according to your specific requirements and security policies. This example is for educational purposes and may need modification based on your production needs.
