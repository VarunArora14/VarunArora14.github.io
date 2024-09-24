---
title: "Nodeaffinity, Taints and Tolerations"
date: 2024-09-17T01:43:12+05:30
draft: false
tags: ["Kubernetes", "Advanced"]
---

Node affinity and taints/tolerations are Kubernetes features that allow you to control where and how pods are scheduled onto nodes in a cluster. They serve different purposes but can be used in combination to achieve more advanced scheduling requirements.

1. **Node Affinity**:
   Node affinity is a feature that allows you to specify rules for **which nodes your pods are scheduled on based on labels assigned to nodes**. It can be used to **influence pod placement** based on node characteristics such as hardware capabilities, geographic location, or other custom attributes.

   - **Node Selector**: Node affinity **uses node selectors** to specify requirements. A node selector is a set of key-value pairs that must match the labels on a node for a pod to be scheduled on that node. For example, you might label nodes in specific availability zones, and then use node affinity to ensure that certain pods are scheduled in specific zones.

   - **Node Affinity Types**:
     - **RequiredDuringSchedulingIgnoredDuringExecution**: This means that a pod will only be scheduled onto nodes that match the node affinity rules. If no nodes match, the pod won't be scheduled.
     - **PreferredDuringSchedulingIgnoredDuringExecution**: This is a preference rather than a hard requirement. Pods with preferred node affinity will be scheduled on nodes that match if possible, but they can still be scheduled on nodes that don't match.

2. **Taints and Tolerations**:
   Taints and tolerations are used to mark nodes and pods with special attributes and constraints.

   - **Taints**: A taint is a label that is applied to a node, typically to indicate that the node has some special characteristic or constraint. For example, you might taint nodes with specific hardware features or to indicate maintenance is needed.

   - **Tolerations**: A toleration is a specification placed on pods, allowing them to tolerate (i.e., be scheduled onto) nodes with matching taints. Tolerations are used to ensure that pods are scheduled on nodes even if those nodes have certain taints. Tolerations are defined in the pod's YAML spec.

   - **Effect**: Taints can have three effects:
     - **NoSchedule**: Pods that don't tolerate the taint are not scheduled on the node.
     - **PreferNoSchedule**: Kubernetes tries to avoid scheduling pods that don't tolerate the taint on the node, but it's not a hard restriction.
     - **NoExecute**: Existing pods on a node that don't tolerate the taint are evicted (removed) when the taint is added.

Combining Node Affinity and Taints/Tolerations:

- You can use node affinity to specify where you want your pods to run based on node labels.
- You can use taints to mark certain nodes and then use tolerations in your pod specifications to allow them to run on those tainted nodes, even if they don't meet the affinity rules.
- This combination allows for more fine-grained control over pod placement in complex scenarios.

In summary, node affinity helps control pod placement based on node labels, while taints and tolerations help control pod placement based on node attributes, such as taints. Used together, they provide a powerful mechanism for controlling pod scheduling in Kubernetes clusters.

Example -

**Scenario**: You have a Kubernetes cluster that spans **multiple availability zones** in a cloud provider's data center. You want to ensure high availability for your application by spreading your pods across these availability zones. At the same time, you have specialized nodes in one of the availability zones with unique hardware that can process certain workloads more efficiently.

**Use of Taints**:

1. **Tainting Specialized Nodes**: In the availability zone with the specialized hardware, you can apply a taint to the nodes indicating their uniqueness. For example:

   ```yaml
   kubectl taint nodes node-in-zone-1 specialized=true:NoSchedule
   ```

   This taint tells the Kubernetes scheduler that nodes with the label `specialized=true` should not be scheduled with pods unless those pods tolerate the taint.

**Use of Node Affinity**:

1. **Labeling Nodes by Availability Zone**: You label nodes in each availability zone with zone-specific labels. For example:

   ```yaml
   kubectl label nodes node-in-zone-1 availability-zone=zone-1
   kubectl label nodes node-in-zone-2 availability-zone=zone-2
   ```

2. **Node Affinity Rules**: In your application's pod specifications, you can use node affinity to specify that certain **pods** should be **scheduled** in **specific availability zones**. For example:

   ```yaml
   affinity:
     nodeAffinity:
       requiredDuringSchedulingIgnoredDuringExecution:
         nodeSelectorTerms:
           - matchExpressions:
               - key: availability-zone
                 operator: In
                 values:
                   - zone-1
   ```

   Here **key** is name of key **availability-zone of node** and **operator represents relationship with the values(In, NotIn, Exists, DoesNotExist. Gt, and Lt.)** and then we have **values** which are **list of values for key allowed**(multiple nodes can have same key but different values and we want to to attach to multiple of them)

   This node affinity rule ensures that **pods with this specification are scheduled only on nodes with label `availability-zone=zone-1`**.

**Combining Taints and Node Affinity**:
Now, let's say you have a **specific workload** that benefits significantly from running on the specialized nodes in `zone-1`. Here's where you can combine taints and node affinity:

1. **Toleration for Tainted Nodes**: In the **pod specification** for the workloads that benefit from the **specialized nodes, you can add tolerations for the taint** you applied earlier:

   ```yaml
   tolerations:
     - key: specialized
       operator: Equal
       value: "true"
       effect: NoSchedule
   ```

   This allows these specific pods to tolerate the taint and be scheduled onto the specialized nodes, even though the node affinity rule might have otherwise placed them in a different availability zone.

Here you not only first choose the node via labels, but also choose the eact node with tolerations for the same labelled nodes.
If there are 5 nodes out of which u want specialised workloads to run on 2 specific then you can add taint to the nodes and then tolerations to pods for fine-grained choice.

By using taints and tolerations in conjunction with node affinity, you achieve the following:

- Nodes with unique characteristics (e.g., specialized hardware) are marked with taints to prevent non-compatible pods from being scheduled there.
- Node affinity ensures that most of your pods are spread across availability zones for high availability.
- Tolerations allow specific workloads to override node affinity rules and run on the specialized nodes when it's beneficial for them.

This combination of features gives you fine-grained control over pod placement in scenarios where you need to balance high availability with specialized node requirements.
