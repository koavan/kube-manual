# Installation
## Install dependencies

### Install docker
```
sudo yum update -y
sudo amazon-linux-extras install docker
sudo service docker start
sudo systemctl enable docker
sudo usermod -a -G docker ec2-user
sudo chmod 777 /var/run/docker.sock
```
### Install kubelet, kubeadm and kubectl

```    
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=0
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF
sudo yum install -y kubelet kubeadm kubectl
```
### Reference
kudeadm -<br/>
https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
    
Edit the config file `/usr/lib/systemd/system/kubelet.service.d/10-kubeadm.conf` and add a custom Environment variable

```
# Custom
Environment="KUBELET_CGROUP_ARGS=--cgroup-driver=cgroupfs"

# This is a file that the user can use for overrides of the kubelet args as a last resort. Preferably, the user should use
# the .NodeRegistration.KubeletExtraArgs object in the configuration files instead. KUBELET_EXTRA_ARGS should be sourced from this file.
EnvironmentFile=-/etc/sysconfig/kubelet
ExecStart=
ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $KUBELET_KUBEADM_ARGS $KUBELET_EXTRA_ARGS $KUBELET_CGROUP_ARGS
```

Reload the daemon using <br/>
```systemctl daemon-reload```

# Create a multi-host cluster

## Initialise master node

Login as super user
```
sudo su -
```

To initialise master node run the below command <br/>
```
kubeadm init --apiserver-advertise-address=172.31.5.107 --pod-network-cidr=192.168.0.0/16  --ignore-preflight-errors=all
```

To start using your cluster, you need to run the following as a regular user:
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
Alternatively, if you are the root user, you can run the commands below: ( One time only )
```
echo "export KUBECONFIG=/etc/kubernetes/admin.conf" >> ~/.bashrc
source ~/.bashrc
```
Install a network interface. Calico in our case.
```
kubectl taint nodes --all node-role.kubernetes.io/master-
kubectl --kubeconfig=/etc/kubernetes/admin.conf create -f https://projectcalico.docs.tigera.io/v3.22/manifests/calico.yaml
```
Output of the init command will display the command ( kubeadm join ) to run in the worker nodes to join the worker with the master.

## Join worker nodes with master node
```
kubeadm join 172.31.5.107:6443 --token r0xqhg.5u8xsn31q8tjfmjs --discovery-token-ca-cert-hash sha256:32c62b5d9f7c78a73fe754d4e81100a66187fd0f81e07fa8ee34947b29142dfc
```
Run 
    kubectl get nodes