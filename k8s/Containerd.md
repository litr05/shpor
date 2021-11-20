# Containerd

- Step 0: Install Dependent Libraries  
```
apt-get update
apt-get install libseccomp2
```
- Step 1: Download Release from github (chek latest version https://github.com/containerd/containerd)
```
VERSION=1.5.7
wget https://github.com/containerd/containerd/releases/download/v${VERSION}/cri-containerd-cni-${VERSION}-linux-amd64.tar.gz
wget https://github.com/containerd/containerd/releases/download/v${VERSION}/cri-containerd-cni-${VERSION}-linux-amd64.tar.gz.sha256sum
sha256sum --check cri-containerd-cni-${VERSION}-linux-amd64.tar.gz.sha256sum
```
-- Install from Docker repo  
```
apt-get install -y ca-certificates curl gnupg lsb-release
curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg && \
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update && apt-get install -y docker-ce docker-ce-cli containerd.io
```

Step 2: Install Containerd 
```
tar --no-overwrite-dir -C / -xzf cri-containerd-cni-${VERSION}-linux-amd64.tar.gz
mkdir /etc/containerd && containerd config default > /etc/containerd/config.toml
systemctl daemon-reload && systemctl enable containerd && systemctl start containerd
systemctl status containerd
```
-	Step 2.2: Containerd use proxy  
```
nano /etc/systemd/system/containerd.service
```
ADD to
```
[Service]
Environment="HTTP_PROXY=http://10.47.240.250:9999"
Environment="HTTPS_PROXY=http://10.47.240.250:9999"
Environment="NO_PROXY=localhost,127.0.0.1,10.0.0.0/8,10.96.0.0/12,192.168.0.0/16,*.mts.ru"
systemctl daemon-reload && systemctl restart containerd
```
- Step 3: Install Kubeadm, Kubelet and Kubectl
```
cat <<EOF | tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF
modprobe overlay
modprobe br_netfilter
```
```
cat <<EOF | tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF
```
```
sysctl --system
```
```
apt-get update && apt-get install -y apt-transport-https ca-certificates curl
curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | tee /etc/apt/sources.list.d/kubernetes.list
apt-get update && apt-get install -y kubelet kubeadm kubectl
apt-mark hold kubelet kubeadm kubectl
```

- Step 4: Create Systemd Drop-In for Containerd
```
nano /etc/systemd/system/kubelet.service.d/0-containerd.conf
```
ADD to

[Service]
Environment="KUBELET_EXTRA_ARGS=--container-runtime=remote --runtime-request-timeout=15m --container-runtime-endpoint=unix:///run/containerd/containerd.sock"
```
- Step 5: Using the systemd cgroup driver
To use the systemd cgroup driver in /etc/containerd/config.toml with runc, set

[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
  ...
  [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
    SystemdCgroup = true
If you apply this change make sure to restart containerd again:

```
sudo systemctl restart containerd
```