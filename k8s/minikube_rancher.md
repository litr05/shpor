# Here's a roundup of shell steps done to get minikube with Rancher 2.5.2 installed and working on EC2. Docker driver. Ubuntu 18.04
- Install Docker
```
DEBIAN_FRONTEND=noninteractive curl https://releases.rancher.com/install-docker/19.03.sh  | sh
sudo usermod -aG docker $USER
cat <<EOF | sudo tee /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF
sudo cat /etc/docker/daemon.json
sudo mkdir -p /etc/systemd/system/docker.service.d
sudo systemctl daemon-reload
sudo systemctl enable docker
sudo systemctl restart docker
```
- Install minikube  
The latest here is minikube 1.5.1
```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb
sudo dpkg -i minikube_latest_amd64.deb
sudo apt-get update && sudo apt-get install -y apt-transport-https gnupg2 curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
minikube start --cpus 4 --memory 10000 --ports=80:80,443:443
minikube addons enable ingress
alias k=kubectl
k get svc -A
kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v1.0.4/cert-manager.crds.yaml
helm repo add jetstack https://charts.jetstack.io
helm repo add rancher-latest https://releases.rancher.com/server-charts/latest
helm repo update
kubectl create namespace cert-manager
kubectl create namespace cattle-system
helm install cert-manager jetstack/cert-manager --namespace cert-manager --version v1.0.4
k get po -A
k get all -A
helm install rancher rancher-latest/rancher --namespace cattle-system --set hostname=test.<PUBLIC_IP>.xip.io
kubectl -n cattle-system rollout status deploy/rancher
```