# Set timezone
```
timedatectl set-timezone Europe/Moscow
timedatectl status
```

# Set static ip
```
cat <<EOF | tee /etc/systemd/network/static.network
[Match]
Name=ens192

[Network]
Address=192.168.50.128/24
#Gateway=10.40.1.2
DNS=192.168.50.254
EOF
```

systemctl restart systemd-networkd


download_container: false 

# UPDATE FLATCAR
```
curl -sSL https://stable.release.flatcar-linux.net/amd64-usr/current/version.txt | grep FLATCAR_VERSION
cat /etc/os-release | grep VERSION
```

# gcr and kubernetes image repo define
gcr_image_repo: "gcr.io"
kube_image_repo: "registry.k8s.io"

# docker image repo define
docker_image_repo: "docker.io"

# quay image repo define
quay_image_repo: "quay.io"

# github image repo define (ex multus only use that)
github_image_repo: "ghcr.io"

ansible-playbook -i inventory/mycluster/hosts.yaml cluster.yml \
    -e 'download_run_once=true' -e 'download_localhost=true' \
    --tags download --skip-tags upload,upgrade
	
ansible-playbook -i inventory/mycluster/hosts.yaml cluster.yml
	
	
	
-e 'ansible_python_interpreter=/opt/bin/python'	

wget -O https://downloads.python.org/pypy/pypy3.9-v7.3.9-linux64.tar.bz2
wget http://10.40.1.253/repository/raw-proxy/pypy/pypy3.9-v7.3.9-linux64.tar.bz2

PYPI_URL="http://10.40.1.253/repository/raw-proxy/pypy/${PYPY_FILENAME}.tar.bz2"

192.168.50.128   master1.local    master1
192.168.50.129   master2.local    master2
192.168.50.130   master3.local    master3
192.168.50.131   node1.local    node1
192.168.50.132   node2.local    node2
192.168.50.133   node3.local    node3
10.40.1.254   ansible.local    ansible
