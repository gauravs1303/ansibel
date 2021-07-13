# This Repository is for configuring the Kubernates Multi Node Cluster Using Ansible Play-Book

_You can manually configure Kubernates Multi Node Cluster by following below steps:-_
 
=================================================================================================================================================================================
# **Master**
Step1:-
#yum install docker
#systemctl enable docker --now
Step2:-
#vi /etc/yum.repos.d/kubernetes.repo

---
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg

---
Step3:- #yum repolist -y
Step4:- #yum install -y kubeadm --disableexcludes=kubernetes
Step5:- #systemctl enable kubelet --now
Step6:- #kubeadm config images pull
Step7:- #yum install iproute-tc
Step8:- By default docker use "cgroupfs" as "cgroup driver" for kubeadm we need to change it to "systemd", for doing so:-
#vi /etc/docker/daemon.json

---
{
  "exec-opts": ["native.cgroupdriver=systemd"]
}

---
Step9:- # kubeadm init --pod-network-cidr=10.240.0.0/16  --ignore-preflight-errors=NumCPU  --ignore-preflight-errors=Mem
Step10:-
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
Step11:-
- Run this command for token list in Master node 
#kubeadm token list
- Run this command in master node to get join command
#kubeadm token create --print-join-command
Step12:-
#kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

=================================================================================================================================================================================
# **Slave**

Step1:-
#yum install docker
#systemctl enable docker --now
#yum install iproute-ce
Step2:-
#vi /etc/yum.repos.d/kubernetes.repo

---
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg

---
Step3:- # yum install -y kubectl kubeadm kubelet --disableexcludes=kubernetes
#systemctl enable kubelet --now

Set the cgroup driver
Step4:- Install Iproute-tc
Step5:- Enable the bridge facility by folder "/etc/sysctl.d/k8s.conf"

---
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1

---
Step6:- Run this command in worker nodes to join master #kubeadm join <token>

  =================================================================================================================================================================================
# Error on kubeadm init command:-

1.[WARNING IsDockerSystemdCheck]:-

#vi /etc/docker/daemon.json
  
---
{
  "exec-opts": ["native.cgroupdriver=systemd"]
}

---
#systemctl restart docker
Check:- #docker info | grep Driver

2.[WARNING FileExisting-tc]:-
#yum install iproute-tc

3.[ERROR NumCPU]:
--ignore-preflight-errors=NumCPU

4.[ERROR Mem]:
--ignore-preflight-errors=Mem

Extra:-
--ignore-preflight-errors=FileContent--proc-sys-net-bridge-bridge-nf-call-iptables
