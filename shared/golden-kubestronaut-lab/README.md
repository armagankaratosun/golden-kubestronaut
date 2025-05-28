# 🧪 Golden Kubestronaut Lab 

This lab sets up a **3-node (1 controlplane - 2 worker ) Kubeadm Kubernetes cluster** using Vagrant.

---

## Prerequisites

Make sure the following tools are installed **on your host machine**:

### Vagrant + VirtualBox

VirtualBox 7.1+ adds native support for Apple Silicon (ARM64).

- **Download manually from:**  
  [https://www.virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads)

Please do **not** install via Homebrew, as it might be installing an older version that doesn't yet support ARM builds properly. 

Then, continue with the vagrant installation

```bash
# macOS (Homebrew)
brew install --cask vagrant
```
---

## 🚀 Usage

### Clone the repository (if not already)

```bash
git clone https://github.com/armagankaratosun/golden-kubestronaut.git
cd golden-kubestronaut/docs/golden-kubestronaut-lab
```

### Launch the cluster

```bash
vagrant up
```

This will:

- Start 1 controlplane + 2 worker Ubuntu 22.04 VMs

---

## Common Commands

### Re-provision

```bash
vagrant provision
```

### SSH into a node

```bash
vagrant ssh controlplane
```
### Shutdown the cluster

```bash
vagrant halt
```

### Restart the cluster

```bash
vagrant up
```

### Destroy all nodes

```bash
vagrant destroy -f
```

---

## Next Steps

- Automate the Kubernetes installation 
- Add Helm, GitOps tooling, Prometheus stack via additional roles for each certification scenario
- Use snapshots (`vagrant package`) for sharing labs

---

## 📚 References

- [Kubeadm Documentation](https://docs.Kubeadm.io/)
- [Vagrant Documentation](https://developer.hashicorp.com/vagrant/docs)
- [Ansible Documentation](https://docs.ansible.com/)

---

💡 This lab is part of the **Golden Kubestronaut** journey — a hands-on collection of notes, labs, and tools toward passing all 14 CNCF certifications.
