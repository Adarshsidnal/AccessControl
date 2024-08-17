# Kubernetes Cluster Setup On Ubuntu 22.04
A Kubernetes cluster is used to manage and orchestrate containerized applications at scale. Kubernetes is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications.

## Let's delve into the process of establishing a Kubernetes cluster.
<br>
1 .  When you run sudo -s and provide the necessary authentication (usually your password), you enter a new shell environment where you have administrative control over the system.


```
sudo -s
```
<br>
<br>

2 . when you run this command, it appends these two hostname-to-IP mappings to your /etc/hosts file, allowing your system to resolve the hostnames k8s-control and k8s-2 to their respective IP addresses when you use those hostnames in network-related operations.
```
printf "\n192.168.15.93 k8s-control\n192.168.15.94 k8s-2\n\n" >> /etc/hosts
```
<br>
<br>
3 .  The command is modifying the containerd configuration to ensure that the "overlay" and "br_netfilter" kernel modules are loaded during the system's startup. 

```
printf "overlay\nbr_netfilter\n" >> /etc/modules-load.d/containerd.conf
```
<br>
<br>
4 . This command is used to load the kernel module named "overlay" and "br_netfilter" respectively into the running kernel.

```
modprobe overlay
modprobe br_netfilter
```
<br>
<br>
5 . This command  configure the kernel to support networking requirements and packet filtering specific to containerization and orchestration environments.

```
printf "net.bridge.bridge-nf-call-iptables = 1\nnet.ipv4.ip_forward = 1\nnet.bridge.bridge-nf-call-ip6tables = 1\n" >> /etc/sysctl.d/99-kubernetes-cri.conf
```
<br>
<br>
6 . When you run sysctl --system, it reads these configuration files and applies the specified parameter settings, ensuring that the kernel operates with the desired configuration.

```
sysctl --system
```
<br>
<br>
7 . The commands  provided are used to download and install the containerd runtime on a Linux system.

```
wget https://github.com/containerd/containerd/releases/download/v1.6.16/containerd-1.6.16-linux-amd64.tar.gz -P /tmp/
tar Cxzvf /usr/local /tmp/containerd-1.6.16-linux-amd64.tar.gz
```
<br>
<br>
8 . The commands are setting up and configuring the systemd service for containerd.

```
wget https://raw.githubusercontent.com/containerd/containerd/main/containerd.service -P /etc/systemd/system/
systemctl daemon-reload
systemctl enable --now containerd
```
<br>
<br>
9 . The provided commands are used to download the runc binary and install it on a Linux system.

```
wget https://github.com/opencontainers/runc/releases/download/v1.1.4/runc.amd64 -P /tmp/
install -m 755 /tmp/runc.amd64 /usr/local/sbin/runc
```
<br>
<br>
10 . After running these commands, the CNI plugins are extracted from the archive and installed in the /opt/cni/bin directory. These CNI plugins are used for container networking in containerization platforms like Kubernetes and allow you to manage container network configurations.

```
wget https://github.com/containernetworking/plugins/releases/download/v1.2.0/cni-plugins-linux-amd64-v1.2.0.tgz -P /tmp/
mkdir -p /opt/cni/bin
tar Cxzvf /opt/cni/bin /tmp/cni-plugins-linux-amd64-v1.2.0.tgz
```
<br>
<br>
11 . After executing  these commands , containerd should be running with the updated configuration that enables systemd cgroup management. 

```
mkdir -p /etc/containerd
containerd config default | tee /etc/containerd/config.toml   <<<<<<<<<<< manually edit and change systemdCgroup to true
systemctl restart containerd
```
<br>
<br>
12 . not clear

```
swapoff -a  <<<<<<<< just disable it in /etc/fstab instead
```
<br>
<br>
13 .  Installs and updates necessary packages.

```
apt-get update
apt-get install -y apt-transport-https ca-certificates curl
```
<br>
<br>
14 .  These two commands together download the GPG key for the Kubernetes repository, and then add a new entry to your system's package sources list (/etc/apt/sources.list.d/kubernetes.list) that points to the Kubernetes repository using the GPG key for verification.

```
curl -fsSLo /etc/apt/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | tee /etc/apt/sources.list.d/kubernetes.list
```
<br>
<br>
15 .  Update and reboot

```
apt-get update
reboot
sudo -s
```
<br>
<br>
16 .The first command installs specific versions of Kubernetes components, and the second command marks these components as "held" to prevent automatic updates. 

```
apt-get install -y kubelet=1.26.1-00 kubeadm=1.26.1-00 kubectl=1.26.1-00
apt-mark hold kubelet kubeadm kubectl
```
<br>
<br>
17 . check swap config, ensure swap is 0

```
free -m
```
<br>
<br>
18 . After running this command, it will generate a set of instructions and commands that you should follow to configure the kubeconfig for your user and join worker nodes to the cluster.

```
### ONLY ON CONTROL NODE .. control plane install:
kubeadm init --pod-network-cidr 10.10.0.0/16 --kubernetes-version 1.26.1 --node-name k8s-control
```
<br>
<br>
19 .  The commands  provided are for installing Calico 3.25 as the CNI (Container Network Interface) in a Kubernetes cluster.

```
# add Calico 3.25 CNI 
### https://docs.tigera.io/calico/3.25/getting-started/kubernetes/quickstart
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/tigera-operator.yaml
wget https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/custom-resources.yaml
vi custom-resources.yaml <<<<<< edit the CIDR for pods if its custom
kubectl apply -f custom-resources.yaml
```
<br>
<br>
20 . The kubeadm token create --print-join-command command is used in a Kubernetes cluster to generate a token for joining additional nodes to the cluster, and it prints the corresponding join command.

```
kubeadm token create --print-join-command
```


 
