# Install Helm From Apt (Debian/Ubuntu)

```
curl https://baltocdn.com/helm/signing.asc | apt-key add -
echo "deb https://baltocdn.com/helm/stable/debian/ all main" | tee /etc/apt/sources.list.d/helm-stable-debian.list 
apt-get update && apt-get install helm
```

 minikube start --force --driver=docker