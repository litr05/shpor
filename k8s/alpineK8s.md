# Alpine Linux K8s in 10 Minutes

Prerequisits
You need an Alpine Linux install (this guide is written against version 3.15 standard image) with internet access. I recommend at least 2 CPU with 4GB of ram and 10GB of disk for each node.

For HA control planes you'll need a mininum of three nodes

## 1. Setup the Repositories
Update you repositories under /etc/apk/repositories to include community, edge community and testing.
```
#/media/cdrom/apks
http://dl-cdn.alpinelinux.org/alpine/v3.15/main
http://dl-cdn.alpinelinux.org/alpine/v3.15/community
#http://dl-cdn.alpinelinux.org/alpine/edge/main
http://dl-cdn.alpinelinux.org/alpine/edge/community
http://dl-cdn.alpinelinux.org/alpine/edge/testing
```
## 2. Node Setup
This series of commands solves a series is incremental problems and sets up the system (if the first control node) for kubectl/kubeadm to run properly on next login by linking the config.
The result here gives you a functional node that can be joined to an existing cluster or can become the first control plane of a new cluster. 🎶

*** This build assumes CNI usage of flannel for networking ***
```
#add kernel module for networking stuff
echo "br_netfilter" > /etc/modules-load.d/k8s.conf
modprobe br_netfilter
apk add cni-plugin-flannel
apk add cni-plugins
apk add flannel
apk add flannel-contrib-cni
apk add kubelet
apk add kubeadm
apk add kubectl
apk add docker
apk add uuidgen
apk add nfs-utils
#get rid of swap
cat /etc/fstab | grep -v swap > temp.fstab
cat temp.fstab > /etc/fstab
rm temp.fstab
swapoff -a
#Fix prometheus errors
mount --make-rshared /
echo "#!/bin/sh" > /etc/local.d/sharemetrics.start
echo "mount --make-rshared /" >> /etc/local.d/sharemetrics.start
chmod +x /etc/local.d/sharemetrics.start
rc-update add local
#Fix id error messages
uuidgen > /etc/machine-id
#Add services
rc-update add docker
rc-update add kubelet
#Sync time
rc-update add ntpd
/etc/init.d/ntpd start
/etc/init.d/docker start
#fix flannel
ln -s /usr/libexec/cni/flannel-amd64 /usr/libexec/cni/flannel
#kernel stuff
echo "net.bridge.bridge-nf-call-iptables=1" >> /etc/sysctl.conf
sysctl net.bridge.bridge-nf-call-iptables=1
```
Your blank node is now ready! If it's the first, you'll want to make a control node.

## 3. Setup the Control Plane (New Cluster!)
Run this command to start the cluster and then apply a network.

#do not change subnet
```
kubeadm init --pod-network-cidr=10.244.0.0/16 --node-name=master
mkdir ~/.kube
ln -s /etc/kubernetes/admin.conf /root/.kube/config
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
```

You now have a control plane. This also gives you the command to run on our blank nodes to add them to this cluster as workers.

## 4. Join the cluster.
Run this to get the join command from the control plane which you would then run on your new worker.

``` kubeadm token create --print-join-command ```
### Bonus: Setup NFS Mounts on K8s

This can be shared NFS storage to allow for auto persistent claim fulfilment. You'll need your IP updated and export information.
```
helm repo add nfs-subdir-external-provisioner \ 
	https://kubernetes-sigs.github.io/nfs-subdir-external-provisioner/

helm install nfs-subdir-external-provisioner \ 
	nfs-subdir-external-provisioner/nfs-subdir-external-provisioner \
    --set nfs.server=192.168.1.31 \
    --set nfs.path=/exports/cluster00
```
Now set the default storage class for the cluster.

```
kubectl get storageclass
kubectl patch storageclass nfs-client -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
Check on System
Check on your system.

kubectl get nodes
kubectl get all
```