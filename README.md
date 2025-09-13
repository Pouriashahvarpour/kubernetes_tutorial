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
6. **Manually Running a Pod Without a Manifest**  

   1. **Run a Pod manually:**  
      ```bash
      kubectl run PodName --image=IMAGE_NAME --restart=Never
      ```

   2. **List all Pods:**  
      ```bash
      kubectl get pod PodName
      ```

   3. **Get detailed information about a Pod:**  
      ```bash
      kubectl describe pod PodName
      ```

   4. **Get Pod output in different formats (JSON or YAML):**  
      ```bash
      kubectl get pod PodName -o json
      kubectl get pod PodName -o yaml
      ```

   5. **Delete a Pod:**  
      ```bash
      kubectl delete pod PodName
      ```
---
7. **Running a Pod Using a Manifest**  

   1. The **`apply`** command in Kubernetes is **idempotent**. This means that even if you run it multiple times, as long as the cluster state already matches the manifest, there will be no side effects. Re-running this command will not change the cluster since the resource is already created.  

      ```bash
      kubectl apply -f PodName.yaml

      kubectl apply -f PodName1.yaml -f PodName2.yaml
      ```

   2. To delete a Pod created with a manifest, use the following command:  

      ```bash
      kubectl delete -f PodName.yaml
      ```

---