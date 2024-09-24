---
title: "Devops Roadmap"
# date: 2024-09-13T01:53:12+05:30
draft: false
tags: ["Devops", "AWS", "Kubernetes", "Python"]
weight: 2
---

Consider the following below topics and sub-topics to learn and understand to get into Devops.

## Kubernetes

- Architecture
  - Api Server - REST, watched by multiple items(kubelet, k-proxy), certificate for request
  - etcd - key-value store, cluster in eks for HA
  - Scheduler - taints, tolerations, affinity based scheduling
  - Kube Controller Manager - state of pods and changes, both watch api server
  - kubelet(run on each node) and interaction with containerd(it creates the container asked by scheduler)
  - k-proxy - runs on each node, handles networking and DNS. Flat networking
- Deployment, pod, service, secret, configmaps (understand their working and connection)
- Ingress Controller and ingress resource (ingress, egress etc and what they are)
- commands to run inside container
- pod lifecycles
- pod, node networking(DNS, fqdn)
- Service Account, roles, clusterrole, rolebinding and clusterrolebinding
- NetworkPolicies and how they work
- Volumes (types, mount location) **important**
- Authorization, authentication and Admission controller
- Flow of request (refer to image in Documents/devops folder)
- CRDs
- Go deeper into more topics and its API conventions

## AWS

- Knowledge of cloud practitioner course
- Working and knowledge of EC2
  - Setup
  - Patching
  - Security Groups
  - subnets
  - userdata
- Networking Knowledge
  - Public and private subnets(internet access to private subnets)
  - NAT and gateways
  - VPC endpoints
  - ALB vs NLB
  - vpc peering vs transit gateway
  - Security groups and their port mapping
  - CIDR block
- Route53
  - record types
  - working and health checks(setting them up)
  - monitoring route53
- Cloudwatch
  - logs
  - creating dashboard
  - understanding metrics and querying

## Web Server(Apache and Nginx)

- need of web server
- virtual hosting
- allow/deny IPs
- .htaccess and htpasswd
- reverse proxy
- Implement thread and other directives
- configure logging
- understand various log formats
- server logs, audit logs enable, check tracking of users

## Docker (containerization)

- need of docker
- docker networking (types of docker networks)
- docker commands and creating dockerfile
- Docker compose
- docker swarm
- volume mounts and their types
- port mapping (left host: right container)
- Meaning of commands like -
  - ENTRYPOINT
  - ARG (how its used)
  - WORKDIR etc

## Jenkins and Gitlab

- Pipeline setup
- understand groovy syntax
- understand .gitlab-ci.yml file as well
- create own groovy scripts

## terraform

- what is IAAC and why IAAC is needed?
- create own tf code and store locally

## ADHOC

- iops and throughput
- mail configure, smtp, send mail
- redirects
- openSSL
- postfix(similar to sendMail)
- sftp in linux, add user
- ipsec, ACL, port forwarding
- port forwarding in nginx
- **jumpbox** private to public network
- understand kernel, patching

## Consul and Istio

- setup and service mesh
- store env in consul and update them
- try istio for more like request tracing

## ArgoCD: TODO

- Setup Argo later for CD as CI needs its own jenkins/gitlab pipeline
- First learn pipelines and then use ArgoCD later

# Programming

## Python

- Automate anything and everything with this. Create and focus on logic with this

## Golang

- learn this for long time future
- complex solution sbuild from python to this

## Things to be good at

- Linux Administration
- Monitoring
- CI & CD Pipelines
- Some sort of scripting
- General cloud knowledge of a specific provider
- Communication
- Integrations
- Development workflows
- Performance tuning (web server, network, database)
