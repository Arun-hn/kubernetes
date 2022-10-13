# kubernetes
Create Master and Worker Nodes of K8s in AWS EC2 Instances

1. Create Instance for Master and one Worker Node with 2 CPU ie t2.medium
with Docker pre-Installed (bootstrapping)

#!/bin/bash
sudo yum update
sudo yum install docker -y
sudo systemctl enable docker


2. Change Hostnames of both master and worker 
hostnamectl set-hostname master
exec bash

3. Check for docker Installed or not in both the machines
docker --version


4. ------Install Kubeadm ( In Both Master and Worker Nodes) Linux

------Install kubeadm with native package management 

cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-\$basearch
enabled=1
gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF

cat /etc/yum.repos.d/kubernetes.repo

sudo yum install kubeadm .............     (on both the nodes)

systemctl restart docker && systemctl enable docker && systemctl restart kubelet && systemctl enable kubelet ----(on both the nodes)


5. Only on the Master Node:
------------------------------
On the master node initialize the cluster.

kubeadm init --apiserver-advertise-address 172.31.21.224 (private ip) --pod-network-cidr 192.168.0.0/16
kubeadm init --apiserver-advertise-address 172.31.21.224 --pod-network-cidr 192.168.0.0/16

6. Wait for around 4 minutes. 
-----------------------------
Following commands will appear on the output screen of the master node. 
apply it on the master’s terminal.

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

This will also generate a kubeadm join command with some tokens.
Copy that command


kubeadm join 172.31.21.224:6443 --token 9ahb67.3mzmfj7l2tovdfaj \
        --discovery-token-ca-cert-hash sha256:2834bcdfdbc5b60db888008f6f5df62ae6908df68582e89281fd94a0bb537bf7


7. Goto worker Node Paste the Kubeadmjoin token command

8. In Master Node 
-----------------
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml

kubectl get nodes

kubectl get pods
