# Add root cert as trusted cert
```bash
mkdir /usr/share/ca-certificates/nexus && cd /usr/share/ca-certificates/nexus
openssl s_client -showcerts -connect ansible:443 -servername ansible  </dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > ansible.crt
cat <<EOF | tee -a /etc/ca-certificates.conf
nexus/ansible.crt
EOF
update-ca-certificates
```
# Посмотреть свойства CA сертификата
```
awk -v cmd='openssl x509 -noout -subject' ' /BEGIN/{close(cmd)};{print | cmd}' < /etc/ssl/certs/ca-certificates.crt | grep -i ansible
```
# Выключение проверки SSL для APT
```
touch /etc/apt/apt.conf.d/99verify-peer.conf \
&& echo >>/etc/apt/apt.conf.d/99verify-peer.conf "Acquire { https::Verify-Peer false }"
```
# Заменить repo Debian на nexus apt-proxy
```
cd /etc/apt/
sed \
-e 's/http/https/g' \
-e 's/deb.debian.org/ansible\/repository\/apt-deb/g' \
-e 's/security.debian.org/ansible\/repository\/apt-deb/g' \
sources.list.save > sources.list
```