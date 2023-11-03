## Understanding Kubernetes Architecture and Performing a Kubernetes Installation Lab

![image](https://github.com/janjiralakirankumar/Kubernetes-Cluster/assets/137407373/38c36d1f-88c1-4cd6-8531-8c18c8c3db9d)

**Introduction:**

Kubernetes, an open-source platform, plays a pivotal role in automating the deployment, scaling, and management of containerized applications. Initially developed by Google and currently maintained by the Cloud Native Computing Foundation (CNCF), it's crafted using the Go (Golang) programming language. This blog post is dedicated to unraveling the core architecture of Kubernetes, along with a step-by-step laboratory guide for setting up your own Kubernetes cluster.

**Kubernetes Architecture:**

Kubernetes employs a master-worker architecture to ensure the efficient operation of containerized applications. Let's delve deeper into the critical components:

**Master Node:**

1. **API Server:**
   - The API server functions as the primary gateway to the Kubernetes cluster. It receives and processes user requests, ultimately storing the desired state of the cluster in the ETCD database.

2. **ETCD:**
   - ETCD, a distributed key-value store, serves as the repository for the Kubernetes cluster's configuration data. This is where the API server stores the current state of the cluster.

3. **Controller Manager:**
   - Responsible for preserving the desired state of the Kubernetes cluster, the controller manager continually monitors the cluster's status. It takes corrective actions when needed, such as creating a new pod to maintain the desired state if a pod is deleted.

4. **Kube-scheduler:**
   - The Kube-scheduler plays a pivotal role in assigning pods to worker nodes within the cluster. When a user submits a request to create a new pod, the Kube-scheduler identifies a suitable node based on factors like available resources (CPU and memory), pod resource requirements, and user-defined constraints or preferences.

**Worker Node:**

1. **Kubelet:**
   - The kubelet is the primary component on the worker node. It communicates with the API server to receive instructions on running applications and services on the node.

2. **Kube Proxy:**
   - Kube Proxy takes charge of network proxying on the worker node. It manages network rules and directs traffic to the appropriate containers.

3. **Pods:**
   - Pods are the smallest deployable units in Kubernetes. They encapsulate one or more containers that share the same network namespace and storage volumes.

4. **Container Runtime:**
   - The container runtime is responsible for executing containers on the worker node. Kubernetes is compatible with multiple container runtimes, including Docker, CRI-O, and Containerd.

**Kubernetes Installation Laboratory:**

**Step 1:** The laboratory begins with launching two instances: a master node and a worker node. It's essential to choose the right instance types; in this guide, we recommend a 't2.medium' for the master and a 't2.micro' for the worker. However, your specific requirements may necessitate different choices.

**Step 2:** The first practical step is to install Docker on both the master and worker nodes. You can use the following commands:

```bash
sudo apt update -y
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
```

Docker will play a crucial role in running containers within your Kubernetes cluster.

**Step 3:** With Docker in place, the next task is to install `kubeadm`. Start by fetching the necessary packages and updating the package list on both the master and worker nodes:

```bash
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt update -y
```

This step paves the way for the installation of Kubernetes components.

**Step 4:** Install `kubeadm`, `kubectl`, and `kubelet` on both nodes to complete the Kubernetes setup:

```bash
sudo apt install kubeadm=1.20.0-00 kubectl=1.20.0-00 kubelet=1.20.0-00 -y
```

These components are vital for orchestrating containers in your cluster.

**Step 5:** The laboratory proceeds with preparing the master node and initiating the cluster setup. Here are the steps in detail:

- Switch to the superuser account:

  ```bash
  sudo su
  ```

- Initialize the master node:

  ```bash
  kubeadm init
  ```

- Create a directory for the Kubernetes configuration file:

  ```bash
  mkdir -p $HOME/.kube
  ```

- Copy the configuration file to the appropriate directory:

  ```bash
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  ```

- Set the correct permissions:

  ```bash
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
  ```

- Install a networking plugin for Kubernetes. In this guide, we're using Weave Net, but there are other options available:

  ```bash
  kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml
  ```

- Create a token to authenticate the worker nodes and establish a secure connection to the master node:

  ```bash
  kubeadm token create --print-join-command
  ```

**Step 6:** Moving on to the worker node, use the following command to ensure it's ready to join the cluster:

```bash
sudo su
kubeadm reset pre-flight checks
```

This step validates that the worker node is configured correctly for joining the cluster.

**Step 7:** It's time to verify the connection between the nodes. On the master node, run the following command:

```bash
kubectl get nodes
```

This command should display a list of connected nodes, confirming that your worker node has successfully joined the cluster.

**Step 8:** As the laboratory draws to a close, create an Nginx pod on the master node with this command:

```bash
kubectl create deployment nginx --image nginx
```

This demonstrates how you can deploy applications within your Kubernetes cluster.

**Step 9:** To conclude the lab, learn how to delete a pod. Use the following command on the master node:

```bash
kubectl delete pod nginx
```

This step highlights the flexibility and control Kubernetes offers for managing containerized applications.

**Conclusion:**

Armed with a profound understanding of Kubernetes architecture and hands-on experience, you're well-equipped to navigate the dynamic world of container orchestration. Remember that Kubernetes is a versatile platform, offering a plethora of features and tools for your containerized applications. As you journey further into the Kubernetes ecosystem, you'll discover even more opportunities for optimizing your container management processes. Happy Kuberneting!

---
