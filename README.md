
# DSN580S T2 Practical Assignment

**Student Name:** Amanda  
**Student Number:** 250493330

---

## Task 1: Provision and Secure the Instance

Launched an Ubuntu Server LTS `t3.medium` EC2 instance named `250493330-minikube`. The Security Group was configured to allow SSH (port 22) strictly from my IP address to ensure security.

## Task 2: Install Docker, kubectl and Minikube

Docker was installed and the `ubuntu` user was added to the docker group to avoid running Minikube as root. `kubectl` and Minikube were installed using official binaries.

## Task 3: Deploy and Verify a Workload

Deployed a YAML manifest for an Nginx application with 3 replicas (labeled with `250493330`). Exposed it via NodePort and verified the connection using `curl`. Scaled the deployment up to 5 replicas, down to 3, and deleted a pod to demonstrate Kubernetes self-healing.

## Task 4: Connect a Dashboard (Portainer)

Installed Portainer via Helm using `ClusterIP` to prevent public exposure. Accessed the dashboard securely via an SSH tunnel and verified the Nginx deployment was visible.

## Task 5: Reflection

A complete reflection essay detailing the single point of failure, service discovery, and multi-node clusters is included in the submitted PDF report.

---

## Appendix: Command History

All raw terminal commands executed for this assignment are listed below in order.

### Task 1: SSH Connection

```bash
ssh -i C:\Users\bheki\minikube-key.pem ubuntu@3.90.180.32
echo 250493330 && date && hostname
```

### Task 2: Docker and Minikube Installation

```bash
sudo apt-get update
sudo apt-get install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings

sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo usermod -aG docker $USER
newgrp docker

curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

minikube start --driver=docker
kubectl get nodes
```

### Task 3: Deployment and Scaling

```bash
kubectl apply -f nginx-deployment.yaml
kubectl get pods
minikube service nginx-svc-250493330 --url

kubectl scale deployment nginx-deploy-250493330 --replicas=5
kubectl scale deployment nginx-deploy-250493330 --replicas=3

kubectl delete pod nginx-deploy-250493330-75976d84b-cpw66
```

### Task 4: Portainer Installation

```bash
sudo snap install helm --classic

helm repo add portainer https://portainer.github.io/k8s/
helm repo update

kubectl create namespace portainer

helm install --set service.type=ClusterIP portainer portainer/portainer -n portainer
```
