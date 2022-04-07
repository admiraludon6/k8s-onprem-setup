## K8S On-prem Installation
This is a poc for k8s single cluster using kubeadm.

### Pre-requisites for working environment
Your machine must have the following installed and in working condition:
1. `docker`.
2. `kubeadm`.
3. Host machine must be able to provide at least: 18vCPUs and 18GiB RAM.  

### Key knowledge you need to explore
1. `docker`
2. `kubeadm`
3. `calico / flunnel`
4. Server networking (hostnames, DNS, DHCP, IPs and Subnet setup)

### Cluster settings
Below are the settings for each nodes in the cluster.

| IP address   | Hostname                |          | vCPU | Memory (MiB) | Descriptions                        |
| ------------ | ----------------------- | -------- | ---- | ------------ | ----------------------------------- |
| 172.16.0.11  | kmaster1.localhost | kmaster1 | 2    | 2048         | Master node                |
| 172.16.0.101 | kworker1.localhost | kworker1 | 4    | 4096         | Worker node                       |

### Important notes
1. Always power off the VMs if you are not using it.
2. The VMs must use bridged network to host with active gateway defined for k8s to work.
3. `cgroup` for both `docker` and k8s must be set to `systemd`.
4. All VMs must be connected and accessable from each other using `ssh`.  
5. All hostnames must be resolveable on each VMs. 
5. `kubeadm join` for master or control-plane certificate-key only valid for 2 hours. Generate new certificate-key if you want to add new control-plane to the cluster after the previous key expires.
7. This sample POC does not take k8s hardening steps into account. 

### Basic steps to deploy a cluster
1. Install and setup `docker`, `kubeadm`, `kubelet` at all master and worker VMs.
2. Initiate `kubeadm init` on main master node.
4. Join worker to the cluster using join-command created in step 2.
5. Provision `calico / flunnel` network layer to the cluster.
6. Deploy your services, replicasets, deployments and pods.  


