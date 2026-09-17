# Homelab

### Secrets
```bash
kubectl create secret generic pihole-secret \
  --namespace=pihole \
  --from-literal=WEBPASSWORD='PIHOLE_PASSWORD'

kubectl create secret generic pihole-secret \
  --namespace=external-dns \
  --from-literal=WEBPASSWORD='PIHOLE_PASSWORD'

kubectl create secret generic github-write-creds \
  --namespace=argocd \
  --from-literal=username='GITHUB_TOKEN_NAME' \
  --from-literal=password='GITHUB_TOKEN'
```

### Kuberentes version upgrades

Control Plane node:
```bash
mcedit /etc/apt/sources.list.d/kubernetes.sources
apt-mark unhold kubeadm
apt-get update && sudo apt-get install -y kubeadm='1.37.0-*'
apt-mark hold kubeadm

kubeadm version
kubeadm upgrade plan
kubeadm upgrade apply v1.37.0

apt-mark unhold kubelet kubectl
apt-get update && sudo apt-get install -y kubelet='1.37.0-*' kubectl='1.37.0-*'
apt-mark hold kubelet kubectl
systemctl daemon-reload
systemctl restart kubelet
```

Worker Nodes
```bash
mcedit /etc/apt/sources.list.d/kubernetes.sources
apt-mark unhold kubeadm
apt-get update && sudo apt-get install -y kubeadm='1.37.0-*'
apt-mark hold kubeadm

kubeadm version
kubeadm upgrade node

apt-mark unhold kubelet kubectl
apt-get update && sudo apt-get install -y kubelet='1.37.0-*' kubectl='1.37.0-*'
apt-mark hold kubelet kubectl
systemctl daemon-reload
systemctl restart kubelet
```

Change version in Ansible playbook and execute it
