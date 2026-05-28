# Kubernetes Architecture Reference

This document outlines the core components of our Kubernetes cluster architecture, divided into the Control Plane (Master Node) and the Data Plane (Worker Nodes).

## 🧠 Master Node (Control Plane)
The brains of the cluster, responsible for global decision-making, detecting events, and responding to cluster changes.

*   **API Server (`kube-apiserver`):** The front-end gateway that exposes the Kubernetes API. It acts as the entry point for all control commands from users and tools (like `kubectl`).
*   **Scheduler (`kube-scheduler`):** The matchmaker. It watches for newly created Pods that have no assigned node and selects the best worker node for them to run on based on resource requirements.
*   **etcd:** The cluster's source of truth. A highly available, secure key-value store that saves the complete configuration data and state map of the entire Kubernetes cluster.
*   **Controller Manager (`kube-controller-manager`):** The enforcer. It runs continuous background loops to regulate the state of the cluster, ensuring the current active state matches your desired setup (e.g., maintaining ReplicaSet counts).

## 🏗️ Worker Node (Data Plane)
The workhorses of the cluster, responsible for hosting and executing your live application containers.

*   **kubelet:** The node captain. An agent that runs on each worker node to ensure that the assigned containers are healthy, running, and matching the Pod specifications.
*   **kube-proxy:** The traffic cop. A network agent running on each node that maintains network rules, enabling communication to your Pods from inside or outside the cluster.
*   **Container Runtime:** The engine. The underlying software responsible for actually running the container images (e.g., `containerd`, Docker, or CRI-O).
