# 🛠️ Kubeadm Kubernetes Cluster Step-by-step Guide 

> Works for Ubuntu 22.04 nodes in Vagrant/VirtualBox labs.


<p align="center">
  <img src="../img/kubeadm.png" alt="Kubeadm" width="250"/>
</p>


## 1. Disable Swap

```bash
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab
```


## 2. Load kernel modules

```bash
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter
```


## 3. Enable Bridged Networking and IP Forwarding

```bash
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

sudo sysctl --system
```

## 4. Install CRI-O Runtime

```bash

sudo apt-get update -y
sudo apt-get install -y software-properties-common curl apt-transport-https ca-certificates

# Add CRI-O repo
curl -fsSL https://pkgs.k8s.io/addons:/cri-o:/prerelease:/main/deb/Release.key |
   sudo gpg --dearmor -o /etc/apt/keyrings/cri-o-apt-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/cri-o-apt-keyring.gpg] https://pkgs.k8s.io/addons:/cri-o:/prerelease:/main/deb/ /" |
    sudo tee /etc/apt/sources.list.d/cri-o.list

# Install CRI-O
sudo apt-get update -y
sudo apt-get install -y cri-o

# Enable and start
sudo systemctl daemon-reload
sudo systemctl enable crio --now
```

## 5. Add Kubernetes APT Repository (v1.32)

```bash
sudo mkdir -p /etc/apt/keyrings

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.32/deb/Release.key \
  | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /" \
  | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

## 6. Add Node IPs to `/etc/hosts` 

```bash
sudo tee -a /etc/hosts > /dev/null <<EOF
10.0.0.10  controlplane
10.0.0.11  worker1
10.0.0.12  worker2
EOF
```

## 7. Install Kubernetes Components

```bash
sudo apt-get update
sudo apt-get install -y kubelet=1.32.0-* kubeadm=1.32.0-* kubectl=1.32.0-*
sudo apt-mark hold kubelet kubeadm kubectl
```

## 7. Set `kubelet` Extra Arguments

```
sudo apt-get install -y jq


local_ip="$(ip --json a s | jq -r '.[] | if .ifname == "eth1" then .addr_info[] | if .family == "inet" then .local else empty end else empty end')"

sudo tee /etc/default/kubelet <<EOF
KUBELET_EXTRA_ARGS=--node-ip=${local_ip}
${ENVIRONMENT}
EOF
```

## 8. Initialize Control Plane (ControlPlane Node Only)

```bash
sudo kubeadm init \
  --apiserver-advertise-address="${local_ip}" \
  --apiserver-cert-extra-sans="${local_ip}" 
  --service-cidr=172.17.1.0/18
  --pod-network-cidr=172.16.1.0/16
```

## 9. Set Up `kubectl` for the `vagrant` User (ControlPlane Node Only)

```bash
mkdir -p $HOME/.kube
sudo cp /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## 10. Install Calico CNI (ControlPlane Node Only)

```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.30.0/manifests/calico.yaml
```

## 11. Verify ControlPlane Status (ControlPlane Node Only)

```bash
kubectl get nodes -o wide
kubectl get pods -n kube-system
```

You should see the controlplane in `Ready` state and all system pods running.

## 12. Join Worker Nodes (Worker Node(s) Only)

On each worker node, run the commands until the `step 7` then  run the command printed at the end of `kubeadm init`. If you lost it, regenerate it from the `controlplane`:

```bash
kubeadm token create --print-join-command
```

Run the output on each worker:

```bash
sudo kubeadm join <ControlPlaneIp>:6443 --token <...> --discovery-token-ca-cert-hash sha256:<...> \

```

## 13. Verify Cluster Status Again

```bash
kubectl get nodes -o wide
kubectl get pods -n kube-system
```

You should see all nodes in `Ready` state and all system pods running.