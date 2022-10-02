### Edit sshd_conf
```
cat <<EOF | tee -a /etc/ssh/sshd_config
	PermitRootLogin yes
	PubkeyAuthentication yes
	PubkeyAcceptedKeyTypes=+ssh-rsa
EOF
rc-service sshd restart
```
### ASH color promt
```
cp /etc/profile.d/color_prompt.sh.disabled /etc/profile.d/color_prompt.sh
```
# Network static
```
vi /etc/network/interfaces
iface eth0 inet static
        address 10.40.1.130
        netmask 255.255.255.0
        gateway 10.40.1.2
```
### Add External disk nexus-data
```
pvcreate /dev/sdb

vgcreate vg1 /dev/sdb

lvcreate -l 100%FREE -n nexus vg1

lvdisplay

mkfs.ext4 /dev/vg1/nexus

mount /dev/vg1/nexus /opt/nexus-data

vi /etc/fstab

/dev/vg1/nexus  /opt/nexus-data    ext4    defaults        1 2

```

### Install packeges
```
apk add podman
```

### You can enable cgroups v2 by editing /etc/rc.conf and setting rc_cgroup_mode to unified
```
cd /etc && cp rc.conf rc_.conf && sed -e 's/rc_cgroup_mode=\"hybrid\"/rc_cgroup_mode=\"unified\"/g' rc_.conf > rc.conf && rm rc_.conf

```

### To run podman you'll need to enable the cgroups service, consider enabling cgroups v2.
You might need to restart your machine for this to work properl
```
rc-update add cgroups
rc-service cgroups start
reboot
```
### For rootless support (replace <USER> with your username):
```
modprobe tun
echo tun >>/etc/modules
echo litr:100000:65536 >/etc/subuid
echo litr:100000:65536 >/etc/subgid
podman system migrate

```

### Open unprivileged_port
```
cat <<EOF | tee -a /etc/sysctl.conf
	net.ipv4.ip_unprivileged_port_start=80
EOF
```
### Run an example container to verify everything works:
```
podman run --rm hello-world
```
### podman generate systemd
```
podman generate systemd --new --files --name nexus
```
### Containers terminate on shell logout
``` loginctl enable-linger ```


# Run podman nexus repo
```
podman run --name=nexus_nexus_1 -d --label io.podman.compose.config-hash=123 --label io.podman.compose.project=nexus --label io.podman.compose.version=0.0.1 --label com.docker.compose.project=nexus --label com.docker.compose.project.working_dir=/home/litr/nexus --label com.docker.compose.project.config_files=docker-compose.yml --label com.docker.compose.container-number=1 --label com.docker.compose.service=nexus -v /nexus-data:/opt/nexus-data --net nexus_default --network-alias nexus docker.io/sonatype/nexus3:3.41.1 \
&& podman run --name=nexus_nginx_1 -d --label io.podman.compose.config-hash=123 --label io.podman.compose.project=nexus --label io.podman.compose.version=0.0.1 --label com.docker.compose.project=nexus --label com.docker.compose.project.working_dir=/home/litr/nexus --label com.docker.compose.project.config_files=docker-compose.yml --label com.docker.compose.container-number=1 --label com.docker.compose.service=nginx --net nexus_default --network-alias nginx -p 80:80 -p 443:443 -p 6666:6666 -p 7777:7777 nginx-nexus nginx -g daemon off;
```
```
podman pod create --name nexus -p 80:80 -p 443:443 -p 6666:6666 -p 7777:7777 > /dev/null \
&& podman run --pod nexus --name=nexus_nexus_1 -d -v /opt/nexus-data:/nexus-data docker.io/sonatype/nexus3:3.41.1 > /dev/null\
&& podman run --pod nexus --name=nexus_nginx_1 -d nginx-nexus > /dev/null
```
```
podman stop -t 10 nexus_nginx_1 > /dev/null && podman stop -t 10 nexus_nexus_1 > /dev/null \
&& podman rm nexus_nginx_1 > /dev/null && podman rm nexus_nexus_1 > /dev/null \
&& podman pod stop nexus > /dev/null && podman pod rm nexus > /dev/null
```
# Add init.d service for nexus
```
cp /etc/init.d/hostname /etc/init.d/nexus
cat <<EOF | tee /etc/init.d/nexus
#!/sbin/openrc-run

description="Set Nexus Repository."

depend() {
        need net
}

start() {
        ebegin "Start Nexus"
        podman pod create --name nexus -p 80:80 -p 443:443 -p 6666:6666 -p 7777:7777 > /dev/null \
        && podman run --pod nexus --name=nexus_nexus_1 -d -v /opt/nexus-data:/nexus-data docker.io/sonatype/nexus3:3.41.1 > /dev/null \
        && podman run --pod nexus --name=nexus_nginx_1 -d nginx-nexus > /dev/null
        eend $?
}

stop() {
        ebegin "Stop Nexus"
        podman stop -t 10 nexus_nginx_1 > /dev/null && podman stop -t 10 nexus_nexus_1 > /dev/null \
        && podman rm nexus_nginx_1 > /dev/null && podman rm nexus_nexus_1 > /dev/null \
        && podman pod stop nexus > /dev/null && podman pod rm nexus > /dev/null
        eend $?
}
EOF
```
