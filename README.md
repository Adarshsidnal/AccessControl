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

 
