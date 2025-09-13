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
8. **Managing Pods Using a Deployment**  

   Instead of creating Pods directly, we usually create a **Deployment**, which manages the lifecycle of Pods. This means we no longer need to deal with Pods individually. A Deployment abstracts away the complexity and provides higher-level management features such as **scaling**, **rolling updates**, and **self-healing**.  

   1. **Create a Deployment** named **PodName** with a specified container image:  
      ```bash
      kubectl create deployment PodName --image=IMAGE_NAME
      ```

   2. **List all Deployments** to ensure it was created successfully:  
      ```bash
      kubectl get deployments
      ```

   3. **Get details of a specific Deployment:**  
      ```bash
      kubectl get deploy PodName
      ```

   4. When a Deployment is created, it automatically manages the Pods and assigns labels to them. To view the labels:  
      ```bash
      kubectl get deploy PodName -o jsonpath="{.spec.template.metadata.labels}"
      ```  

      For example, a Deployment may add a label like `app=PodName`. To list Pods with this label:  
      ```bash
      kubectl get pods -l app=PodName
      ```

   5. **Labels** are a core Kubernetes pattern used to connect resources. Controllers like Deployments rely on **label selectors** to manage their Pods.  

   6. **Port-forward traffic** from the Deployment to one of its Pods:  
      ```bash
      kubectl port-forward deploy/PodName 8080:80
      ```  
      This forwards traffic from `localhost:8080` to port `80` inside the Pod.  

   7. **Delete a Deployment** and all of its managed Pods:  
      ```bash
      kubectl delete deploy PodName
      ```

   8. **Exec into a container** inside a Pod managed by the Deployment:  
      ```bash
      ## Exec into a specific container
      kubectl exec deployment/PodName -c ContainerName -it -- sh

      ## Exec into a Pod directly
      kubectl exec PodName -it -- /bin/sh
      ```

   9. **View Pod logs:**  
      ```bash
      ## Logs from all containers
      kubectl logs PodName --tail=5

      ## Logs from a specific container
      kubectl logs PodName -c ContainerName --tail=5
      ```

   10. **Get more details** about Pods managed by the Deployment (IP, Node, etc.):  
       ```bash
       kubectl get pod -l app=PodName -o wide
       ```

       Example output:  
       ```bash
       NAME                         READY   STATUS    RESTARTS   AGE   IP            NODE           NOMINATED NODE   READINESS GATES
       podname-7f9cbd8c6d-abc12     1/1     Running   0          12m   10.244.1.23   worker-node1   <none>           <none>
       podname-7f9cbd8c6d-def34     1/1     Running   0          12m   10.244.2.15   worker-node2   <none>           <none>
       ```
---
9. **Working with Services in Kubernetes**  

   1. **Get information about a Service:**  
      ```bash
      kubectl get svc <ServiceName>
      ```

   2. **What are Services?**  
      - Services in Kubernetes enable communication between Pods and also between the cluster and the external network.  
      - Their main role is to receive and route network traffic.  

   3. **Types of Services:**  
      - **ClusterIP:** Access only within the cluster.  
      - **LoadBalancer:** External access through a cloud Load Balancer.  
      - **NodePort:** External access via a static port on cluster nodes.  
      - **ExternalName:** Maps a Service inside the cluster to an external service.  
      - **Headless:** Makes each Pod directly accessible without a Load Balancer.  

   4. **Details of Each Service Type:**  

      1. **ClusterIP:**  
         - Internal communication between Pods (accessible only inside the cluster).  

      2. **LoadBalancer:**  
         - Provides a public IP to make the Service externally accessible.  

      3. **NodePort:**  
         - Opens a port in the range `30000–32767` on each node.  
         - Rarely used in production environments.  
         - Traffic flow:  
           > **User → NodePort [Port] → Service [Port] → Pod [targetPort]**  

      4. **ExternalName:**  
         - Routes in-cluster traffic to an external service.  
         - DNS name is mapped to a public IP.  
         - No changes in Kubernetes are needed if the external IP changes.  

      5. **Headless:**  
         - DNS resolves directly to Pod IPs (no virtual IP).  
         - Works across the entire cluster.  
         - Requires updates in configuration if Pod IPs change.  
---
10. **Namespaces and Pods in Kubernetes:**  

      1. **List Pods in a specific Namespace:**  
         ```bash
         kubectl get pod -n <Namespace_Name>
         ```

      2. **List Services in a specific Namespace:**  
         ```bash
         kubectl get svc -n <Namespace_Name>
         ```

      3. **Create a new Namespace:**  
         ```bash
         kubectl create namespace <Namespace_Name>
         ```

      4. **Create a Pod in a specific Namespace:**  
         ```bash
         kubectl apply -f <PodManifest>.yaml -n <Namespace_Name>
         ```

      5. **Communication between Pods across different Namespaces:**  
         - By using the IP address of a Pod, it is possible to communicate with it from other Pods in different Namespaces.  

      6. **Access a Service in another Namespace using FQDN (Fully Qualified Domain Name):**  
         ```bash
         <ServiceName>.<NamespaceName>.svc.cluster.local
         ```

         - **<ServiceName>**: Name of the Service  
         - **<NamespaceName>**: Namespace where the Service is running  
         - **svc.cluster.local**: Default suffix used for Services in Kubernetes  

         This FQDN allows you to access Services across different Namespaces.  
---