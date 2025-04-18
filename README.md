# CST8915 Final Lab Assignment: Best Buy Store (On Steroids)

Welcome to the Best Buy Store (On Steroids) application.

This sample demo app consists of a group of containerized microservices that can be easily deployed into a Kubernetes cluster. This is meant to show a realistic scenario using a polyglot architecture, event-driven design, and common open source back-end services (eg - RabbitMQ, MongoDB). The application also leverages OpenAI's models to generate product descriptions and images. This can be done using either [Azure OpenAI](https://learn.microsoft.com/azure/ai-services/openai/overview) or [OpenAI](https://openai.com/).

This application is inspired by Azure Kubernetes Service (AKS) quickstart demo [Azure Kubernetes Service (AKS) Docs](https://learn.microsoft.com/en-us/azure/aks/).



## 1. Architecture
![image](https://github.com/user-attachments/assets/b46addb2-9d77-401f-81f8-38e505a86a07)

## **2. Application and Architecture Explanation**
The **Best Buy App** includes the following components:  

| Service | Description |
| --- | --- |
| `store-front` | Web app for customers to place orders (Vue.js) |
| `store-admin` | Web app used by store employees to view orders in queue and manage products (Vue.js) |
| `order-service` | This service is used for placing orders (Javascript) |
| `product-service` | This service is used to perform CRUD operations on products (Rust) |
| `makeline-service` | This service handles processing orders from the queue and completing them (Golang) |
| `ai-service` | Optional service for adding generative text and graphics creation (Python) |
| `rabbitmq` | RabbitMQ for an order queue |
| `mongodb` | MongoDB instance for persisted data |
| `virtual-customer` | Simulates order creation on a scheduled basis (Rust) |
| `virtual-worker` | Simulates order completion on a scheduled basis (Rust) |

---
## **3. Deployment Instructions**

### Step 1: Clone the Algonquin Pet Store (On Steroids) Repository

To begin, clone the Algonquin Pet Store (On Steroids) repository, which contains all necessary deployment files, we will deploy Best Buy Store base on this.

### Step 2: Set Up the AKS Cluster
Create an AKS cluster with one user node and one system node for this exercise.

1. **Log in to Azure Portal:**
   - Go to [https://portal.azure.com](https://portal.azure.com) and log in with your Azure account.

2. **Create a Resource Group:**
   - In the Azure Portal, search for **Resource Groups** in the search bar.
   - Click **Create** and fill in the following:
     - **Resource group name**: cst8915labassignment
     - **Region**: Canada Central.
   - Click **Review + Create** and then **Create**.

3. **Create an AKS Cluster:**
   - In the search bar, type **Kubernetes services** and click on it.
   - Click **Create** and select **Kubernetes cluster**
   - In the `Basics` tap fill in the following details:
     - **Subscription**: Azure for Student.
     - **Resource group**: Choose cst8915labassignment.
     - **Cluster preset configuration**: Choose Dev/Test.
     - **Kubernetes cluster name**: `cst8915labassignmentcluster`.
     - **Region**: Canada Central.
     - **Availability zones**: None.
     - **AKS pricing tier**: Free.
     - **Kubernetes version**: Default.
     - **Automatic upgrade**: Disabled.
     - **Automatic upgrade scheduler**: No schedule.
     - **Node security channel type**: None.
     - **Security channel scheduler**: No schedule.
     - **Authentication and Authorization**: `Local accounts with Kubernetes RBAC`.
   - In the **Node pools** tap fill in the following details:
     - Select **agentpool**. Optionally change its name to **masterpool**. This nodes will have the controlplane.
        - Set **node size** to D2as_v4.
        - **Scale method**: Manual
        - **Node count**: 1
        - Click **update**
     - Click on **Add node pool**:
        - **Node pool name**: workerspool.
        - **Mode**: User 
        - Set **node size** to D2as_v4.
        - **Scale method**: Manual
        - **Node count**: 2
        - Click **add**
   - Click **Review + Create**, and then **Create**. The deployment will take a few minutes.

4. **Connect to the AKS Cluster:**
   - Once the AKS cluster is deployed, navigate to the cluster in the Azure Portal.
   - In the overview page, click on **Connect**. 
   - Select **Azure CLI** tap. You will need Azure CLI.
   - Login to your azure account using the following command:
      ```
      az login
      ```
   - Set the cluster subscription using the command shown in the portal (it will look something like this):
      ```
      az account set --subscription 'subscribtion-id'
      ```

   - Copy the command shown in the portal for configuring `kubectl` (it will look something like this):
     ```
     az aks get-credentials --cst8915labassignment --name cst8915labassignmentcluster
     ```

   - Verify Cluster Access:
      - Test your connection to the AKS cluster by listing all nodes:
        ```bash
        kubectl get all
        ```
        You should see details of the cluser service in your AKS cluster if the connection is successful.

### Step 3: Deploy the Best Buy Application

1. **Apply the YAML file to the AKS cluster:**
   - In this step, use the K8s deployment YAML files provided in **Deployment Files** folder.
   - Open the terminal and navigate to the file directory.
   - Run the following command to apply the YAML configuration and deploy the application to AKS:
      ```bash
      kubectl apply -f all-in-one.yaml
      ```

2. **Verify the deployment:**
   - After the command executes, verify that the pods are running by using the following command:
     ```bash
     kubectl get pods
     ```

3. **Check services:**
   - Confirm that all services are up and running:
     ```bash
     kubectl get services
     ```

4. **Access the Store Front and Store Admin Application:**
   - The **Store Front** service is configured as a LoadBalancer, which exposes the application to the internet.
   - In the Azure Portal, go to **Kubernetes Services** > **cst8915labassignmentcluster** > **Services and ingresses**.
   - Locate the **store-front** service, and note the **EXTERNAL-IP** address.
   - Open the store-front application in your browser:
     ```
       http://<EXTERNAL-IP>
     ```
   - Locate the **store-admin** service, and note the **EXTERNAL-IP** address.
   - Open the store-admin application in your browser:
     ```
       http://<EXTERNAL-IP>
     ```
---

## 4. Table of Microservice Repositories
A table listing each microservice repository and its GitHub link.

| Service | Description | Github Repo |
| --- | --- | --- |
| `store-front` | Web app for customers to place orders (Vue.js) | [store-front-final](https://github.com/JianyiF/store-front-final) |
| `store-admin` | Web app used by store employees to view orders in queue and manage products (Vue.js) | [store-admin-final](https://github.com/JianyiF/store-admin-final.git) |
| `order-service` | This service is used for placing orders (Javascript) | [order-service-final](https://github.com/JianyiF/order-service-final.git) |
| `product-service` | This service is used to perform CRUD operations on products (Rust) | [product-service-final](https://github.com/JianyiF/product-service-final.git) |
| `makeline-service` | This service handles processing orders from the queue and completing them (Golang) | [makeline-service-final](https://github.com/JianyiF/makeline-service-final.git) |
| `ai-service` | Optional service for adding generative text and graphics creation (Python) | [ai-service-final](https://github.com/JianyiF/ai-service-final) |
| `rabbitmq` | RabbitMQ for an order queue | [rabbitmq](https://github.com/docker-library/rabbitmq) |
| `mongodb` | MongoDB instance for persisted data | [mongodb](https://github.com/docker-library/mongo) |
| `virtual-customer` | Simulates order creation on a scheduled basis (Rust) | [virtual-customer-final](https://github.com/JianyiF/virtual-customer-final.git) |
| `virtual-worker` | Simulates order completion on a scheduled basis (Rust) | [virtual-worker-final](https://github.com/JianyiF/virtual-worker-final.git) |

A table listing each microservice repository and its Docker Hub image link.

| Service | Description | Github Repo |
| --- | --- | --- |
| `store-front` | Web app for customers to place orders (Vue.js) | [store-front-final](https://hub.docker.com/r/nealfan/store-front-final) |
| `store-admin` | Web app used by store employees to view orders in queue and manage products (Vue.js) | [store-admin-final](https://hub.docker.com/r/nealfan/store-admin-final) |
| `order-service` | This service is used for placing orders (Javascript) | [order-service-final](https://hub.docker.com/r/nealfan/order-service-final) |
| `product-service` | This service is used to perform CRUD operations on products (Rust) | [product-service-final](https://hub.docker.com/r/nealfan/product-service-final) |
| `makeline-service` | This service handles processing orders from the queue and completing them (Golang) | [makeline-service-final](https://hub.docker.com/r/nealfan/makeline-service-final) |
| `ai-service` | Optional service for adding generative text and graphics creation (Python) | [ai-service-final]([https://github.com](https://hub.docker.com/r/nealfan/ai-service-final) |
| `rabbitmq` | RabbitMQ for an order queue | [rabbitmq](https://github.com/docker-library/rabbitmq) |
| `mongodb` | MongoDB instance for persisted data | [mongodb](https://github.com/docker-library/mongo) |
| `virtual-customer` | Simulates order creation on a scheduled basis (Rust) | [virtual-customer-final](https://hub.docker.com/r/nealfan/virtual-custormer-final) |
| `virtual-worker` | Simulates order completion on a scheduled basis (Rust) | [virtual-worker-final](https://hub.docker.com/r/nealfan/virtual-worker-final) |



## 5. Demo Video

https://youtu.be/lp00QVRaO3Q

You can use the kubernetes YAML files provided in the [Deployment Files](./Deployment%20Files/) folder to deploy the app to an AKS cluster.



