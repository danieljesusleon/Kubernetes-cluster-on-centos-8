# Kubernetes-cluster-on-centos-8
# Installing and Configuring a Kubernetes Cluster with One Controller and Two Nodes

This guide will walk you through the steps to install and configure a Kubernetes cluster on Linux/CentOS 8 with one controller and two worker nodes.

## Step 1: Install the necessary packages on all the nodes

You need to install some packages on all the nodes to prepare them for the Kubernetes installation. You can do this by running the following command on all the nodes:

sudo dnf install -y yum-utils device-mapper-persistent-data lvm2 curl vim


## Step 2: Install Docker on all the nodes

Kubernetes requires a container runtime, and Docker is one of the most popular choices. You can install Docker on all the nodes by running the following command:

sudo dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo
sudo dnf install -y docker-ce docker-ce-cli containerd.io
sudo systemctl start docker
sudo systemctl enable docker


## Step 3: Install Kubernetes on all the nodes

You can install Kubernetes on all the nodes by adding the Kubernetes repository and installing the necessary packages. Run the following commands on all the nodes:

sudo tee /etc/yum.repos.d/kubernetes.repo <<EOF
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF

sudo dnf install -y kubelet kubeadm kubectl
sudo systemctl start kubelet
sudo systemctl enable kubelet


## Step 4: Initialize the Kubernetes cluster on the controller node

On the controller node, you need to initialize the Kubernetes cluster by running the following command:

sudo kubeadm init --pod-network-cidr=10.244.0.0/16


This command will take a few minutes to complete, and it will output a command that you need to run on the worker nodes to join them to the cluster.

## Step 5: Configure the Kubernetes network

You need to configure the Kubernetes network to allow communication between the nodes. You can do this by running the following command on the controller node:

sudo kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml


This will install the Calico network plugin, which is a popular choice for Kubernetes networking.

## Step 6: Join the worker nodes to the Kubernetes cluster

On each of the worker nodes, run the command that was outputted by the `kubeadm init` command in Step 4. It should look something like this:

sudo kubeadm join <controller-node-ip>:6443 --token <token> --discovery-token-ca-cert-hash <hash>


## Step 7: Verify the Kubernetes cluster

You can verify that the Kubernetes cluster is working by running the following command on the controller node:

sudo kubectl get nodes


This should output the controller node and the two worker nodes, indicating that they have successfully joined the cluster.

That's it! You now have a working Kubernetes cluster with one controller and two worker nodes.
