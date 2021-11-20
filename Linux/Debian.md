### IP адресация  
	pre-up - выполнить команду перед запуском интерфейса;
	post-up - выполнить команду после запуска интерфейса;
	up - выполнить команду при запуске интерфейса;
	pre-down - команда перед отключением;
	post-down - команда после отключения;
	iface - указывает имя интерфейса;
	inet - указывает
	description - создать имя синоним для устройства;
	address - устанавливает ip адрес для статического соединения;
	netmask - установка маски сети;
	broadcast - широковещательный адрес;
	metric - приоритет для шлюза по умолчанию;
	gateway - шлюз по умолчанию;
	hwaddress - установить MAC адрес;
	mtu - размер одного пакета.  
- НАСТРОЙКА ДИНАМИЧЕСКОГО IP
```
	auto eth0
	iface eth0 inet dhcp
```
- НАСТРОЙКА СТАТИЧЕСКОГО IP АДРЕСА  
Если вы хотите установить именно статический IP, то здесь все будет немного сложнее. 
Нам нужно знать не только этот свободный IP адрес, но и шлюз, маску сети и DNS сервер. 
Для настройки используется такой набор строк:
```
iface ens32 inet static
address 192.168.2.129
netmask 255.255.255.0
gateway 192.168.2.2
broadcast 192.168.2.255
dns-nameserver 8.8.8.8
```
- НАСТРОЙКА ВИРТУАЛЬНЫХ ИНТЕРФЕЙСОВ  
В некоторых случаях нам может понадобиться создать виртуальный интерфейс. 
Это позволяет добавить еще один IP адрес к интерфейсу. 
Чтобы создать такой интерфейс достаточно дописать его номер после двоеточия:
```
auto eth0:0
iface eth0:0 inet static
address 192.168.1.101
netmask 255.255.255.0
gateway 192.168.1.1
dns-nameservers 8.8.8.8
```
- НАСТРОЙКА МОСТОВ  
Сетевые мосты между виртуальными интерфейсами в системе позволяют настроить полноценный доступ к интернету из виртуальных машин. 
Они могут применяться для KVM,qemu,XEN и других конфигураций. Для настройки моста используйте:
```
auto br0
iface br0 inet static
address 192.168.1.20
network 192.168.1.0
netmask 255.255.255.0
broadcast 192.168.1.255
gateway 192.168.1.1
bridge_ports eth0
bridge_stp off
bridge_fd 0
bridge_maxwait 0
```
- ПЕРЕЗАГРУЗКА СЕТИ  
После внесения всех изменений необходимо перезапустить сеть, чтобы сетевые настройки debian вступили в силу, для этого наберите:
```
 sudo systemctl restart networking
```
# Proxy
```
cat <<EOF | tee -a ~/.bashrc 
function proxy(){
   echo -n "username:"
   read -e username
   echo -n "password:"
   read -es password
   #password=`echo $password |sed -e 's/%/%25/g'|sed -e 's/\@/%40/g'|sed -e 's/\//%2F/g'|sed -e 's/\:/%3A/g'|sed -e 's/\\$/%24/g'|sed -e s/#/%23/g|sed -e s/!/%21/g`
   export http_proxy="http://$username:$password@bproxy.ug.mts.ru:3131/"
   #export http_proxy_auth="basic:*:$username:$password"
   #export http_proxy="http://bproxy.ug.mts.ru:3131"
   export https_proxy=$http_proxy
   export ftp_proxy=$http_proxy
   export rsync_proxy=$http_proxy
   export no_proxy="localhost,127.0.0.1,localaddress,.localdomain.com"
   echo -e "\nProxy environment variable set."
   }
   function proxyoff(){
   unset HTTP_PROXY
   unset http_proxy
   unset HTTPS_PROXY
   unset https_proxy
   unset FTP_PROXY
   unset ftp_proxy
   unset RSYNC_PROXY
   unset rsync_proxy
   unset proxy
   unset proxy_username
   unset proxy_password
   echo -e "\nProxy environment variable removed."
   }
EOF
```
