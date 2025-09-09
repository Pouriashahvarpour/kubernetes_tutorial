# Kubernetes

1. **Kubernetes** 
   - **General explanation:** 
        NK8S is a platform for *orchestration* or container management, typically used in infrastructures with a large number of servers.
   - **According to the official Kubernetes documentation:**  
        *“Kubernetes is an open-source system for automating deployment, scaling, and management of containerized applications.”*
---
2. **Cluster**
    - A **Cluster** is a logical unit managed by Kubernetes, consisting of one or more servers, each referred to as a **Node**.
---
3. **Node**
    - A **Node** in Kubernetes is a physical or virtual machine that runs as part of a Cluster. Each Node contains the necessary resources like CPU, memory, and storage to run containers, and can host Pods. Nodes can be of two types: Master Node (which is responsible for managing and controlling the Cluster) and Worker Node (which runs the actual application workloads by hosting Pods).
    - Each **Node** can run one or more containers.
---
4. **Pod**  
   - **General explanation:**  
     A **Pod** is the **smallest** and **simplest** unit in Kubernetes that you can deploy and manage. It is a group of one or more containers that are logically related and run together on the same host.  
   
   - **According to the official Kubernetes documentation:**  
     *“A Pod is the basic execution unit of a Kubernetes application, which consists of one or more containers that share storage, network resources, and a specification for how to run the containers.”*  

   - **Key characteristics of Pods:**  
     - A **Pod** can host **one** or **more** containers, and these containers share the same network namespace and storage volumes.
     - Containers inside a **Pod** can communicate with each other using `localhost`, as they share the same IP address and port space.
     - Kubernetes manages the lifecycle of Pods, including scaling, self-healing, and upgrades.  
---
5. **Declarative vs Imperative in Kubernetes**  
   - In Kubernetes, we work in a **Declarative** manner, not **Imperative**, which means we define the desired state, and Kubernetes is responsible for bringing the system from its current state to that desired state. In other words, Kubernetes manages *how* the work is done.


---
