---
title: "Kubernetes Python Client Usage"
date: 2024-09-16T01:43:12+05:30
draft: false
tags: ["Kubernetes", "Python"]
---

Kubernetes has a python client as well, which is very well underrated and should be used for a variety of tasks as follows -

**Automated Deployment**: You want to automatically deploy a new version of your application when code changes are pushed to your Git repository.

- Use the Python client to create a new Kubernetes deployment, specifying the Docker image version to deploy.
- Set up a webhook or integration in your CI/CD tool (e.g., Jenkins, GitLab CI/CD, or GitHub Actions) to trigger this deployment when new code is pushed.

**Rolling Updates**: You want to perform rolling updates to your application without downtime.

- Use the Python client to update the Docker image version in your deployment.
- Implement logic in your pipeline to wait for the update to complete successfully (e.g., by checking the readiness of pods).
- Rollback the update if any issues are detected.

**Scaling**: You want to automatically scale your application based on metrics, such as increased traffic.

- Use the Python client to adjust the replica count of your deployment based on metrics collected by monitoring tools like Prometheus.
- Set up alerting and automation to trigger scaling actions as needed.

**Resource Cleanup**: After a successful deployment, you want to clean up old resources to save resources and maintain cluster hygiene.

- Use the Python client to delete old deployments, pods, or other resources that are no longer needed.

**Custom Actions**: You want to perform custom actions before or after deployments, such as updating configurations in a ConfigMap, setting environment variables, or invoking a custom script.

- Use the Python client to perform these actions as part of your deployment process.

## CPU Usage automation

Here is example of scaling replicas with python client with keeping watch on the CPU metrics of pods -

```py
import os
from kubernetes import client, config, watch
import time

# Load Kubernetes configuration
config.load_kube_config()

# Create an instance of the Kubernetes API client
api = client.ExtensionsV1beta1Api()

# Define the deployment name and namespace
deployment_name = "my-deployment"
namespace = "default"

# Set the CPU usage threshold for scaling (e.g., 80%)
cpu_threshold_percentage = 80

def get_deployment_replica_count():
    # Get the current replica count of the deployment
    deployment = api.read_namespaced_deployment(deployment_name, namespace)
    return deployment.spec.replicas

def scale_deployment(replica_count):
    # Scale the deployment to the specified replica count
    deployment = api.read_namespaced_deployment(deployment_name, namespace)
    deployment.spec.replicas = replica_count
    api.replace_namespaced_deployment(name=deployment_name, namespace=namespace, body=deployment)

def watch_cpu_usage():
    # Watch for changes in CPU usage (e.g., from Prometheus alerts)
    w = watch.Watch()
    for event in w.stream(client.CustomObjectsApi().list_namespaced_custom_object, group="metrics.k8s.io", version="v1beta1", namespace=namespace, plural="pods"):
        pod_name = event['object']['metadata']['name']
        container_name = event['object']['containers'][0]['name']
        cpu_usage_percentage = event['object']['containers'][0]['usage']['cpu']

        print(f"Pod: {pod_name}, Container: {container_name}, CPU Usage: {cpu_usage_percentage}")

        # Check if CPU usage exceeds the threshold
        if float(cpu_usage_percentage.strip('%')) >= cpu_threshold_percentage:
            # Scale up the deployment by 1 replica
            current_replicas = get_deployment_replica_count()
            scale_deployment(current_replicas + 1)
            print(f"Scaling up to {current_replicas + 1} replicas")

if __name__ == "__main__":
    try:
        watch_cpu_usage()
    except KeyboardInterrupt:
        print("Monitoring stopped.")

```
