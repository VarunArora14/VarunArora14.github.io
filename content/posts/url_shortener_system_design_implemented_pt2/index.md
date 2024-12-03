---
title: "Url Shortener System Design Implementation - Part 2"
date: 2024-12-03T22:43:12+05:30
draft: false
tags: ["System Design", "Docker", "Kubernetes", "Deployment", "Minikube", "StatefulSet", "Volumes"]
---

So the next steps after generating tests and testing the application is as follows -

- Build **Dockerfile** for the app and run the app locally with **redis** and **mongodb** running locally or via docker
- After testing them locally, run them inside **docker-compose** to understand docker networking and DNS resolution
- Build docker images with proper tags to be used for kubernetes deployment and upload to **Dockerhub**
- Install minikube and run it locally
- Generate YAML files for **MongoDB** and **Redis** with **deployment** and **services** configuration and run them **stateless**
- Note their **FQDN** `<service>.<namespace>.svc.cluster.local` and add them as K8s env variables to be used
- Create YAML file for **FastAPI** application with environment set and with **deployment**, **service** and **ingress** and apply it
- Check the application running and access it via command `minikube tunnel`

---

## Deploying to local Kubernetes

Make sure to install minikube as per their documentation - https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2F.exe+download

Run command `minikube start` to run it locally
I will use **minikube** to create deployments with service and ingress. For ingress, I generally provide **host** which should be same as the DNS mapping in the DNS server (like Route53 on AWS cloud). Since I are not deploying right now to cloud, I pass the DNS name I want for our url shortener service for which I have to modify our **local hosts file** in **/etc/hosts** and NOT use the minikube IP. Follow the forum for entire steps - https://stackoverflow.com/questions/58561682/minikube-with-ingress-example-not-working

### Upload Local Docker Image to Dockerhub

- Firstly upload your local docker image to format `<dockerhub_username>/<image_name>:<tag>` like `docker image tag url_shortener-app rdxdocker14/url-shortener-app` (here the default tag used was latest).
- Push docker image to docker hub - `docker image push rdxdocker14/url-shortener-app`

Note - Make sure to when you **build docker images again** with code changes, you are not using the existing docker layers which docker may use for caching. To avoid, run the following command to build docker images `docker build --no-cache -t <tag_name> .`

### Run MongoDB via yaml files in single pods

Run the file `k8s/mongodb.yml` and then set the pod name as **mongo** (for the connection url `mongodb://mongo:27017`). To check the internal dns mapping, I can run busybox and confirm the DNS mappings of a **service** of kubernetes.

Run command `kubectl run -it --rm busybox --image=busybox:1.28` and then inside the busybox, run command `nslookup <service_name>` which here is `nslookup mongo` and I get the FQDN **mongo.default.svc.cluster.local**

Note - The `REDIS_PORT` variable being int was somehow being represented as `tcp://<internal_ip>:6379` as it's value and on commenting it and passing the actual **redis url** with `redis.from_url(...)` solved the issue. It is important to give **new tag** to each docker image to avoid having same code in docker image (cached) to be used.

Note - Currently these are stateless pods and if I have more than 1 replica of these, it will load balance the requests without data being replicated properly across. So I need to set the `replica` parameter in their YAML file to 1.

### Deployment to Kubernetes

- In order to deploy the app to kubernetes, I need to setup the databases first. For this I create files **mongodb.yml** and **redis.yml** and run command `kubectl apply -f <filename.yml>` to create those services respectively. These are services with single pods behind them as databases.
- Once these databases are tried and tested for connection, I can make changes in our code to mention new urls for internal communication in kubernetes which happens to be FQDN of these services in format `<service_name>.<namespace>.svc.cluster.local:<port_number>` like `mongo.default.svc.cluster.local:27017`.
- These can be tested via `nslookup <service_name>` command with a busybox (`kubectl run -it --rm busybox --image=busybox:1.28`) container to confirm DNS mapping.
- After this, I create new urls for mongo and redis connection based on their FQDN.
- Build docker image from code and then upload it to dockerhub in public repo after proper tagging
- Create a file named `k8s/app.yml` which contains the configuration of **pod**, **service** and **ingress** respectively. Refer to these files here - https://github.com/VarunArora14/url-shortener-system-design/tree/master/k8s
- I deploy this on **minikube** windows as per documentation here - https://minikube.sigs.k8s.io/docs/
- Run commands in terminal `minikube addons enable ingress-dns` and `minikube addons enable ingress` for DNS resolution locally
- Apply the app yml file via `kubectl apply -f app.yml` and then check pods and logs of these containers if the app has successfully run
- Run command `minikube tunnel` to access it via localhost. If it does not do DNS resolution by any reason, instead **port forward the app service to port you want** via `kubectl port-forward svc/<service_name> <host_port>:<service_port>` leading to `kubectl port-forward svc/url-shortener 5000:5000`
- Check the functionality to be same as it worked in locally with all APIs working as expected

Note - On deploying services, **ClusterIP** is the **default type** and you can change this to **NodePort** or **LoadBalancer** as well.

### Additional Steps for Scalability and Best Practices

- The files `app.yml` contain **resources** inside it containing the cpu and memory **initial requests** and **limits** which are requested on the nodes they run on (personal computer in our case)
- I provided **liveness** probe at endpoint `/health` of container inside to determine the health of service inside kubernetes where it **restarts container** based on failure threshold
- For databases, I added yaml files for MongoDB and Redis with **StatefulSet**, **Persistent Volumes** and **Persistent Volume Claims** to be used for adding persistence of data if DB pods fail in files `mongodb-pv.yml` and `redis-pv.yml`
- **Horizontal Pod Autoscaler** was added for the FastAPI **Deployment** in K8s so it scales based off conditions in it's yaml `app-hpa.yml`

---

### Kubernetes Important commands and debugging

- To apply the YAML files, simply go to the directory where they are and run `kubectl apply -f filename.yaml`.
- To remove the appliec YAML file (even if it is partially applied), run command `kubectl delete -f filename.yaml`. This will remove the configs which have been applied to existing kubernetes cluster
- To debug containers for their networking, run pods in same namespace like **busybox** and check the DNS resolution of services via command `nslookup <service_name>` where `<service_name>` is the **K8s service object in that namespace**. This will provide the actual FQDN of that service.
- To get logs of a pod, run command `kubectl logs <pod_name> -n <namespace_name>`. The namespace flag and name can be ignored if all resources deployed in default one.
- If command `minikube tunnel` fails by any means for Windows, it is better to **port forward kubernetes service** via `kubectl port-forward svc/<service_name> <host_port>:<service_port>`. Note - This `<service_port>` is **NOT the NodePort** but value **port** in service YAML code.

Once you run this via port-forward, it will run same as it was running locally with the way it stored and redirected the urls.

### Deploying to cloud

While deploying to cloud is out of scope for this project for me, there are minor changes to be made to do so. The key thing is that **the base url `localhost:5000` has to be replaced with an actual DNS url to be allocated via DNS service like AWS Route53**.Assuming you have an EKS cluster running, follow the following steps -

Steps -

- Find the load balancer that the **FastAPI app ingress has created** and grab it's AWS **load balancer arn**
- Go to Route53 => Go to **Hosted Zones** => choose the name of hosted zone if many
- If the hosted zone is like **example.com** then create a **New Record** and enter the the website name. So if the actual website has to be **shortener.example.com** then put the **Record Name** as **shortener**
- Choose **Alias to Application and Classic Load Balancer** with the **Region** the LB is at and set the **Record Type to A**
- Now, inside the code, replace the `http://localhost:5000/` with **shortener.example.com** in all occurences and change variable **origins** it have **shortener.example.com** instead
- Modify the **app.yml** by going to **ingress** section and set the **host** to **shortener.example.com**
- The app is ready to work now

### Potential Improvements for Future

- The `created_date`, `expiration_date` can be added to service to make the service handle expiries of urls.
- The random short url generation can be replaced with KGS (Key Generation Service) which offline prepares a database of keys which are then used to create these short urls by taking that key. For concurrency, once key is taken, it should be marked to not be used. For multiple server scenarios, it can have 2 DBs (used and unused keys) and can transfer the key from used to unused as soon as the service gives key to one of the app servers. For a 6 character short code, the DB size will be `6 x 68.7B = 412 GB`.
- We wil also need KGS in a cluster so if master fails then the replica becomes master as a standby/read replica is needed in case of failover.
- We need to decide the cache size for redis, this can be around 20% of the daily traffic (assuming 80% of the traffic is by 20% of the urls) . Also, LRU key eviction strategy as used now should be decent. These numbers are taken from a system design website - https://www.designgurus.io/blog/url-shortening
- We need LB for client to application server, application server to mongoDB, application server to redis. This can be easily done with setting them in cluster config in k8s and use round robin as default load balancing algo.
- For purging or cleaning the expired urls, we can have functionality that if user clicks on expired url(having existing mapping in DB but expired) we return error of expiry and mark it expired (flag as part of db object or simply delete it). If we have a flag then we can run crons every x hours to delete all expired urls `current_time > expiry_time`. This handles cases when urls are expired but no one accesses them for a very long time without getting hits taking increasing space in DB.
- Proper CI/CD pipeline with Jenkins or Github actions(CI part as cluster is local for CD) to avoid running same docker commands again when code changes for deployment

---

### Here's all what I learnt

- Learnt development of **Async** applications in FastAPI
- Learnt **pydantic validation** along with best API practices
- Implemented **MongoDB** as NoSQL database and **Redis** as cache
- Built docker images for the service with best practices config and **docker-compose** for containerizing the application and database in same docker network
- Deployed application on kubernetes using YAML files with **services** as load balancer and **ingress** on **minikube** and used internal networking via FQDN urls and understood **stateless** application working
- Configured proper **deployment**, **service**, **ingress**, **horizontal pod autoscaler** for app along with **ingress controller**.
- Learnt **StatefulSets** for databases and other important kubernetes concepts like **Persistent Volumes** and **Persistent Volume Claims**
- Learnt best practices for configuration, uvicorn server and kubernetes
- Wrote tests for the app for local development via **pytest** and learnt **fixtures**, **anyio** package working and testing the core functionalities of the application
- Learnt system design principles of scalability for both application and database

While the problem statement was very simple as a leetcode problem, developing services for scalability, testing it, writing configs for deployment to kubernetes and understanding deeper networking and working of applications was a great experience.

Refer to the code repository here - https://github.com/VarunArora14/url-shortener-system-design/tree/master

Thanks for reading this blog. Have a nice day!
