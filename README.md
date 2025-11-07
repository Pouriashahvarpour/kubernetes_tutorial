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
9. **ReplicaSet**

   1. **Relationship between Deployment, ReplicaSet, and Pod:**  
      - A **Deployment** manages one or more **ReplicaSets**.  
      - Each **ReplicaSet** manages the lifecycle of *Pods*.  

   2. **Why ReplicaSet exists between Deployment and Pod:**  
      - A **Deployment** is not limited to replication only.  
      - It also handles advanced capabilities such as:  
      - **Update Strategy**  
      - **Replica Management**  
      - **Self-healing**  
      - **Rollback**  
      - **Declarative Updates**  

   3. **Do we create ReplicaSets directly?**  
      - No. Typically, we define a **Deployment**, and Kubernetes automatically creates and manages the corresponding **ReplicaSet**.  

   4. **Creating a Deployment (which generates a ReplicaSet):**  
      ```bash
      kubectl apply -f <DeploymentManifest>.yaml
      ```  

   5. **Inspect ReplicaSets created by a Deployment:**  
      ```bash
      kubectl get rs
      kubectl describe rs <ReplicaSet_Name>
      ```  

   6. **Inspect Pods managed by a ReplicaSet:**  
      ```bash
      kubectl get pods
      ```  
---
10. **Working with Services in Kubernetes**  

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
            - A service is considered headless when its ClusterIP is set to None
            - Works across the entire cluster.  
            - Requires updates in configuration if Pod IPs change.  
---
11. **Namespaces and Pods in Kubernetes:**  

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
      7. **Access a Pod using DNS (with Headless Service):**  
         
         ```bash
         <PodName>.<ServiceName>.<NamespaceName>.svc.cluster.local
         ```

         - **<PodName>**: Name of the Pod  
         - **<ServiceName>**: Name of the Headless Service (`clusterIP: None`)  
         - **<NamespaceName>**: Namespace where the Pod is running  
         - **svc.cluster.local**: Default suffix used for Services/Pods in Kubernetes  

         This FQDN allows you to access each Pod individually when it is part of a Headless Service (commonly used with StatefulSets).

---

12. **Kubernetes ConfigMap Guide**

      1. **Creating a ConfigMap from a custom env file:**
         ```bash
         kubectl create configmap <ConfigMap_Name> --from-env-file=<envFilePath>
         ```

      2. **Viewing the contents of a ConfigMap:**
         ```bash
         kubectl describe cm <ConfigMap_Name>
         ```

      3. **Ways to set environment variables (env):**

         1. **Hardcoded in the `deployment.yaml` file:**
            - You can directly add environment variables inside the `deployment.yaml` configuration file.

         2. **Using `envFrom`:**
            - This method allows you to add all keys and values from the ConfigMap as environment variables to the Pod.

            Example:

            ```yaml
            envFrom:
            - configMapRef:
                  name: <ConfigMap_Name>
            ```

         3. **Using `ValueFrom`:**
            - If you only want to inject a specific key from the ConfigMap into the environment variable in the Pod, use `ValueFrom`:

            ```yaml
            env:
            - name: <Value_Name>
               valueFrom:
                  configMapKeyRef:
                  name: <ConfigMap_Name>
                  key: <Value_Name_on_ConfigMap>
            ```

      4. **Viewing the output of a file without applying changes (Dry Run):**
         - If you want to see the output of a file without making any changes or creating new resources, use the `--dry-run=client` option:

         ```bash
         kubectl create -f <file.yaml> --dry-run=client
         ```
---
13. **Secrets in Kubernetes**

      `Secret` files are used to store sensitive information (such as passwords, keys, and tokens). This entity is very similar to a `ConfigMap`, but the main difference lies in the confidentiality of the data.

      ### Important notes about Secrets:

      - The default **type** in Secrets is **Opaque**.
         
         This is commonly used for storing usernames and passwords. The data is defined in **Base64 encoded** format.
         
      - In the `secret.yaml` file, the **data** section must have values encoded in Base64.
      - In **Production** environments, the actual password (even if Base64 encoded) should **not** be stored in source control (e.g., Git).

      ### Common Commands:

      1. **Create a Secret from a YAML file:**
         
         ```bash
         kubectl apply -f <Secret_File>.yaml
         ```
         
      2. **List all Secrets:**
         
         ```bash
         kubectl get secret
         ```
         
      3. **View details of a specific Secret:**
         
         ```bash
         kubectl describe secret <Secret_Name>
         ```

         > **Key difference from ConfigMap:**
         > 
         > In the `describe` command for `ConfigMap`, the data content is displayed, but for `Secret`, due to confidentiality, the data is **not** shown.

         ### 4. Connecting a Secret to a Deployment using Volumes:

         To use a Secret in a Pod (for example, mounting secret files into the container), follow this configuration in your `deployment.yaml`:

         ```yaml
         containers:
         - name: <container_name>   # Container name
            volumeMounts:
               - name: <secret_volume_name>
               mountPath: <container_path>
               readOnly: true   # Only read access

         volumes:
         - name: <secret_volume_name>
            secret:
               secretName: <object_secret_name>
               defaultMode: 0400   # Similar to chmod file access permissions
         ```

         ---

         ### Explanation of `defaultMode`:

         - The **three rightmost digits** specify the file access permissions (like `chmod`).
         - The **leftmost digit** (usually 0) is to preserve the octal structure.
         - For example:
            - `0400` → Readable only by the owner
            - `0444` → Readable by everyone
---
 14. **Volume in Kubernetes**

      A **Volume** is a storage space allocated to containers. Using Volumes allows data to be stored outside the container lifecycle, ensuring data persistence even if the container itself is destroyed or replaced.

      ### Common Types of Volumes:

      - **EmptyDir**
      - **HostPath**
      - **Persistent Volume (PV / PVC)**

      ### 1. **EmptyDir**

      - **Feature:** It is not dependent on the container's lifecycle but on the Pod's lifecycle.
      - **Important Note:**
         - If a Pod **crashes** or is **replaced**, the data persists.
         - If the Pod is **deleted**, the data is also deleted.
      - Therefore, it is called **pod-wide storage**.
      - **Use Case:** Suitable for temporary data such as **caches** or files that do not require permanent persistence.

         **Example definition in YAML file:**

         ```yaml
         containers:
         - name: <container_name>
            volumeMounts:
               - name: <emptydir_volume_name>
               mountPath: <container_path>

         volumes:
         - name: <emptydir_volume_name>
            emptyDir: {}   # Volume type
         ```

      ### 2. **HostPath**

      - **Feature:** Data is stored on a Node (node-wide storage).
      - It is not dependent on the Pod's lifecycle. This means if the Pod **crashes**, **replaces**, or is **deleted**, the data will persist on the Node, and any new Pod created on the same Node will still have access to the data.
      - **Important Note:** `HostPath` ties the data to a specific Node. If the Pod is scheduled on a different Node, it will not have access to this data.

         **HostPath Type Values:**

         - `Directory`: The path must already exist on the Node.
         - `DirectoryOrCreate`: If the path does not exist, it will be created automatically.

         **Example definition in YAML file:**

         ```yaml
         containers:
         - name: <container_name>
            volumeMounts:
               - name: <hostpath_volume_name>
               mountPath: <container_path>

         volumes:
         - name: <hostpath_volume_name>
            hostPath:        # Volume type
               path: <Node_path>
               type: Directory  # Or DirectoryOrCreate
         ```

      ### 3. **Persistent Volume (PV)**

      A **Persistent Volume (PV)** is a real storage space that resides on a physical disk, providing durable storage outside of the lifecycle of Pods.

      ### 4. **Persistent Volume Claim (PVC)**

      - A **Persistent Volume Claim (PVC)** is a request for storage by a user (Pod). It specifies certain access permissions and size requirements. It's important to note that PVCs are not always fulfilled immediately
      When a Persistent Volume Claim (PVC) and a Persistent Volume (PV) are bound together, it is said that they are **"bound"**.

      ### Types of PVC Access Modes:

      1. **ReadWriteOnce:**
         - This permission means that only one node or server can read from and write to the volume at the same time. It is also possible for a Pod to read and write simultaneously.

      2. **ReadOnlyMany:**
         - Multiple Pods can simultaneously **read** from the volume, but cannot write to it.

      3. **ReadWriteMany:**
         - Multiple Pods and Nodes can read from and write to the volume at the same time.

      4. **ReadWriteOncePod:**
         - This permission is specific to a single Pod. Only that Pod can read and write to the volume.

       **StorageClass:**

      The `StorageClass` allows dynamic provisioning of storage. It enables clients to request specific storage configurations and define parameters, such as performance, through the StorageClass.

      **Example definition in YAML file:**

      ```yaml
      containers:
      - name: <container_name>  # Define container name
         volumeMounts:
            - name: <pvc_vol_Name>
            mountPath: <container_path>

      volumes:
      - name: <pvc_vol_Name>
         PersistentVolumeClaim: # Volume type
            claimName: <PVC_Metadata_name>
      ```

      **PVC_Metadata_name:** The name of the PVC metadata that we want to connect to.

      ### Commands for PVC:

      1. **Get PVCs:**
         
         ```bash
         kubectl get pvc
         ```

      2. **Get Storage Classes:**
         
         ```bash
         kubectl get storageclass
         ```

      3. **View the YAML configuration of a specific Storage Class:**
         
         ```bash
         kubectl get storageclass <StorageClass_Name> -oyaml
         ```

---
   15. **Horizontal Scaling (HS)**

         To avoid downtime or breakpoints, you should use **Horizontal Scaling**. To implement HS, we increase the number of Replicas.

         **Example in YAML:**

         ```yaml
         spec:
         replicas: <Number_Of_Replicas>

         template:

         ```
---
   16. **Bulk Operations**

         1. **Apply command in bulk:**

            To apply configurations in bulk, use:

            ```bash
            kubectl apply -f <DirectoryName>/
            ```

         2. **Delete command in bulk:**

            To delete resources in bulk, use:

            ```bash
            kubectl delete -f <DirectoryName>/
            ```
---
17. **Resource Management in Kubernetes**

      In the **Resource Manager** section, the **Request** and **Limit** values are defined. The **Request** is the initial or typical amount of resources (such as CPU and RAM) that a Pod requests. In contrast, the **Limit** represents the maximum amount of resources that a Pod is allowed to consume. These settings help optimize resource usage and ensure more accurate resource allocation.

      The **Pod scheduling index on Nodes** is determined based on the **Request** value. This means that when resources are allocated on a Node, the priority of allocation is determined by the amount requested by the Pod.

      ### Commands for Checking Resource Usage:

      1. **To check the resource consumption of a Pod:**

         ```bash
         kubectl top pod
         ```

      2. **To check the resource consumption of a Node:**

         ```bash
         kubectl top node
         ```
---
 18. **Sidecar Pattern in Kubernetes**

      The **Sidecar** pattern is an architectural pattern used in microservices and containerized environments like Kubernetes to enhance and manage the performance of services and applications. This pattern involves running a **sidecar container** alongside the main container (which typically hosts the primary service) within the same Pod.

      ### General Explanation of the Sidecar Pattern:

      In the **Sidecar** architecture, the sidecar container is responsible for tasks that are outside the main service’s responsibilities but support its operation. The sidecar container can perform various tasks such as:

      - **Logging**
      - **Monitoring**
      - **Security**
      - **Traffic Routing** (Proxying)
      - **Scaling Support**
      - **Caching**

      The sidecar can handle these tasks without affecting or requiring changes to the main container.

      ### Key Features of the Sidecar Pattern:

      1. **Separation of Concerns:**
         - By using a sidecar container, additional responsibilities that are not directly related to the main service’s functionality can be offloaded to a separate container. This helps simplify the coding and development of the main service.

      2. **Independence from the Main Service:**
         - The sidecar container can be updated or configured independently, without affecting the main service's functionality.

      3. **Integration:**
         - The sidecar container usually runs in the same Pod as the main service, allowing easy communication and shared resources like networking and storage.

      4. **Increased Flexibility:**
         - This pattern allows development teams to add new features or tools independently, without making complex changes to the main service's code.

      ### Use Cases for the Sidecar Pattern:

      - **In Kubernetes:**
         - In Kubernetes, a Pod can contain multiple containers. In this pattern, the main container (e.g., a web server or application) and the sidecar container (e.g., a proxy like Envoy or a monitoring tool like Prometheus) run together in the same Pod.

      - **In Scalable Services:**
         - Tasks like caching, traffic forwarding, or logging can be offloaded to sidecar containers, helping to decouple these operations from the main service.

      ### Advantages of Using the Sidecar Pattern:

      1. **Reduced Complexity:**
         - By offloading non-essential tasks from the main service, code complexity and the management overhead of services are reduced.

      2. **Scalability:**
         - The sidecar container can be scaled independently and managed separately from the main service.

      3. **Flexibility in Feature Implementation:**
         - Features like security, monitoring, or logging can be added without requiring changes to the main service.
---
19. **Init Containers in Kubernetes**

      **Init Containers** are special containers that run before the main containers in a Pod. They are responsible for preparing the environment and performing initial tasks, such as loading data, setting configurations, or running tests.

      ### Key Points about Init Containers:

      - **Guarantee by Kubernetes:**
         - Kubernetes ensures that all **Init Containers** are executed successfully before the main container starts. Once all Init Containers have completed successfully, the main container will be launched.
      
      - **Sequential Execution:**
         - Init Containers are executed one by one, in sequence. Each **Init Container** will only start once the previous one has completed successfully.

      - **Parallel Execution for Main Containers:**
         - In contrast, **Containers** in a Pod run in parallel, meaning all containers inside the Pod will start at the same time.

      ### Example Spec for Init Containers:

      ```yaml
      spec:
      initContainers:
         - name: <init-container-name>  
            image: <image-name>          
      containers:
         - name: <container-name>     
            image: <image-name>         
      ```

      ### Use Cases for Init Containers:
      - **Environment Setup:** Used for tasks like loading configuration files, setting up environment variables, or ensuring external dependencies are available.
      - **Testing or Validation:** Running tests or validations before the main container starts (e.g., checking if a database is reachable before starting a service).
---
20. **Stateless App** vs **Stateful App**

      1. **Stateless App:**
         - **Characteristics:**
         - **Stateless** apps do not store information related to previous user states or activities. In other words, the required information is available with each request, and these apps do not store any data persistently.
         - These apps do not depend on any specific state and function independently with every request.
         
         - **Advantages:**
         - Since these apps do not rely on any specific state, they are easily replaceable and **interchangeable** when **scaling** or using a **Load Balancer**.

       2. **Stateful App:**

            - **Characteristics:**
            - **Stateful** apps store information sent to them and use that data for subsequent processing. Typically, these types of apps require a database or persistent storage.
            - These apps need data storage to retain their state across requests.
            
               - **Not Suitable for Deployment:** These apps are generally not suited for **Deployment** because **Deployment** is designed for **Stateless Apps** by default.
               - **Pods of a Stateful App** are not interchangeable and cannot be replaced with each other. Hence, **Deployment** is not ideal for managing such apps.
---
21. **StatefulSet** in Kubernetes

      **StatefulSet** is a controller in Kubernetes specifically designed for managing and scaling **Stateful Apps**. This controller is responsible for managing Stateful pods and their persistent storage.

      ### Features and Capabilities of **StatefulSet**:
      1. **Scaling and Pod Management:** 
         - **StatefulSet** is designed for apps that require long-term storage of data.
         - This controller is used for managing pods, assigning unique identities to each pod, and managing large volumes of data.

      2. **Unique Identity for Each Pod:**
         - In **StatefulSet**, each pod is assigned a unique identity, allowing distinct communication with each pod.
         - These identities are accessible via a **DNS** mechanism.

      3. **Headless Service:**
         - For using **StatefulSet**, you must define a **Headless Service** (without a fixed IP) so you can communicate with each pod in the **StatefulSet** through DNS names.

      4. **Best Practices:**
         - When defining a **StatefulSet**, it is recommended to set the `terminationGracePeriodSeconds` variable so that pods have enough time to save data or complete necessary operations before deletion.
         
         ```yaml
         terminationGracePeriodSeconds: <time>
         ```

      5. **Leader-Follower Mechanism:**
         - In this mechanism, one pod is designated as the **Leader**, and only the **Leader** pod can read and write data. Other pods, called **Followers**, only have read access.
         - When the **Leader** pod is updated, the **Follower** pods automatically sync their data.

      6. **VolumeClaimTemplate:**
         - This feature allows you to define a separate **Persistent Volume Claim** (PVC) for each pod in the **StatefulSet**.
         - This allows each pod to store its data in a specific volume, ensuring data persistence.
         - In case the pods in the **StatefulSet** are deleted, PVCs defined through the **VolumeClaimTemplate** remain intact. This is an important feature of **StatefulSet**, which ensures data persistence even if pods are deleted.
---
22. **Liveness & Readiness Probes in Kubernetes**

      **Liveness & Readiness Probes** are essential tools in Kubernetes for monitoring container health. These probes allow Kubernetes to check whether containers in a pod are healthy and ready to receive traffic.

      #### 1. **Liveness Probe:**
      - The **Liveness Probe** checks if a container is still running properly. If the probe fails, Kubernetes restarts the container to ensure that the service stays available.
      - The probe can be configured to perform an **exec** command or use an HTTP request to check the container’s health.

      #### 2. **Readiness Probe:**
      - The **Readiness Probe** checks if a container is ready to handle requests. If the probe fails, Kubernetes removes the pod from the pool of available resources, stopping traffic from being routed to it until the container is ready again.

      #### 3. **Key Differences Between Liveness and Readiness Probes:**
      1. **Liveness Probe:**
         - If the probe fails, the container is restarted.
         - Useful for detecting situations where the container is running but stuck in an unhealthy state.
      2. **Readiness Probe:**
         - If the probe fails, the container is temporarily removed from traffic.
         - Useful for scenarios where the container is running but needs time to initialize or wait for external dependencies, such as a database connection.

      #### 4. **Configuring Liveness and Readiness Probes:**
      - Kubernetes allows you to configure both probes to specify how they should check the health and readiness of containers.

         Example configuration:

         ```yaml
         spec:
         initContainers:
            - name: <init-container-name>
               # Other init container settings

         containers:
            - name: <container-name>
               volumeMounts:
               - name: <volume-name>
                  mountPath: <mount-path>
               resources:
               limits:
                  memory: "512Mi"
                  cpu: "1"
               requests:
                  memory: "256Mi"
                  cpu: "0.5"

               livenessProbe:
               exec:
                  command:
                     - <command-to-check-liveness>
               initialDelaySeconds: 5
               periodSeconds: 5
               failureThreshold: 3

               readinessProbe:
               exec:
                  command:
                     - <command-to-check-readiness>
               initialDelaySeconds: 5
               periodSeconds: 5
               failureThreshold: 3
         ```
         #### 5. **Leader-Follower Mechanism:**

         - In this mechanism, one pod is designated as the **Leader**, and only the **Leader** pod can read and write data. Other pods, called **Followers**, only have read access.
         - When the **Leader** pod is updated, the **Follower** pods automatically sync their data.

         #### 6. **VolumeClaimTemplate:**

         - This feature allows you to define a separate **Persistent Volume Claim** (PVC) for each pod in the **StatefulSet**.
         - This allows each pod to store its data in a specific volume, ensuring data persistence.
         - In case the pods in the **StatefulSet** are deleted, PVCs defined through the **VolumeClaimTemplate** remain intact. This is an important feature of **StatefulSet**, which ensures data persistence even if pods are deleted.
---
23. **Job** in Kubernetes

      A **Job** is an entity in Kubernetes designed to run one or more pods. These pods do not need to run continuously; instead, they terminate as soon as they complete a specific task. In other words, a **Job** is responsible for completing a task, and after the task is finished, the pods are terminated.

       #### 1. **Difference Between Job and Deployment:**

      - While a **Deployment** is designed to run pods continuously and keep restarting them, a **Job** is used for running one-off (non-continuous) tasks.
      - **Job** removes the pods once its task is complete.

      #### 2. **The `apiVersion` for Job:**

      According to the official Kubernetes documentation, the `apiVersion` for a **Job** is `batch/v1`.

      #### 3. **The `restartPolicy` in the Pod Template Spec:**
      In a **Job**, there are two valid values for `restartPolicy`:

      - **Never**: This means that if the container inside the pod crashes, no attempt will be made to restart the pod.
      - **OnFailure**: In this case, if the container exits with a non-zero (failure) exit code, it will be restarted. This is done in the hope that restarting the container will resolve the issue.

      #### **Job Example Structure:**

      ```yaml
      apiVersion: batch/v1
      kind: Job
      metadata:
      name: <job-name>
      spec:
      template:
         spec:
            restartPolicy: Never || OnFailure
            containers:
            - name: <container-name>
               image: <image-name>
               command: ["<command>"]
      ```