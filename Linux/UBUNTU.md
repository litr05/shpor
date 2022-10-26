
##### Содержание
[1. Изменение ip в UBUNTU](#ip)  

[2. Расширение жесткого диска](#disk)  

[3. Proxy](#proxy)  

[4. APT установщик пакетов](#apt)  

[5. Установка MYSQL Debian](#mysql)  

---
<a name="ip"><h3>1. Изменение ip в UBUNTU</h3></a>

```bash
sudo nano /etc/netplan/00-installer-config.yaml
sudo netplan generate && sudo netplan apply
sudo reboot
```

посмотреть отладку если ошибка

```bash
sudo netplan --debug generate && sudo netplan --debug apply 
```
---
<a name="disk"><H3>2. Расширение жесткого диска (для LVM)</H3></a>
- Смотрим на диски
```bash
df -h
```
      Filesystem                         Size  Used Avail Use% Mounted on
      udev                                16G     0   16G   0% /dev
      tmpfs                              3.2G  1.2M  3.2G   1% /run
      /dev/mapper/ubuntu--vg-ubuntu--lv   62G  6.2G   53G  11% /
      tmpfs                               16G     0   16G   0% /dev/shm
      tmpfs                              5.0M     0  5.0M   0% /run/lock
      tmpfs                               16G     0   16G   0% /sys/fs/cgroup
      /dev/sda2                          976M  104M  806M  12% /boot
      /dev/loop0                          56M   56M     0 100% /snap/core18/1944
      /dev/loop2                          32M   32M     0 100% /snap/snapd/10707
      /dev/loop1                          70M   70M     0 100% /snap/lxd/19188
      tmpfs                              3.2G     0  3.2G   0% /run/user/1000

- Расширяем в VCenter объем HDD  

- Расширяем диск ``` /dev/mapper/ubuntu--vg-ubuntu--lv ```
```bash
echo 1 > /sys/block/sda/device/rescan
parted
```

    GNU Parted 3.3
    Using /dev/sda
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) p
    Model: VMware Virtual disk (scsi)
    Disk /dev/sda: 275GB
    Sector size (logical/physical): 512B/512B
    Partition Table: gpt
    Disk Flags:

    Number  Start   End     Size    File system  Name  Flags
    1      1049kB  2097kB  1049kB                     bios_grub
    2      2097kB  1076MB  1074MB  ext4
    3      1076MB  68.7GB  67.6GB

- расширяем 3 диск, ставим объем ``` Disk /dev/sda: 275GB ```, выходим из parted

```
(parted) resizepart 3
End?  [68.7GB]? 275GB
(parted) quit
```

          Information: You may need to update /etc/fstab.

- Делаем ресайзинг командой `pvresize` и `lvextend`
```
pvresize /dev/sda3
```
        
          Physical volume "/dev/sda3" changed
          1 physical volume(s) resized or updated / 0 physical volume(s) not resized  

```
lvextend -r -l +100%FREE /dev/mapper/ubuntu--vg-ubuntu--lv
```
```
Size of logical volume ubuntu-vg/ubuntu-lv changed from <63.00 GiB (16127 extents) to<255.00 GiB (65279 extents).
Logical volume ubuntu-vg/ubuntu-lv successfully resized.
resize2fs 1.45.5 (07-Jan-2020)
Filesystem at /dev/mapper/ubuntu--vg-ubuntu--lv is mounted on /; on-line resizing required
old_desc_blocks = 8, new_desc_blocks = 32
The filesystem on /dev/mapper/ubuntu--vg-ubuntu--lv is now 66845696 (4k) blocks long.
```
- Проверяем результат для диска `/dev/mapper/ubuntu--vg-ubuntu--lv`

```
df -h
```

```
Filesystem                         Size  Used Avail Use% Mounted on
udev                                16G     0   16G   0% /dev
tmpfs                              3.2G  1.2M  3.2G   1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv  251G  6.2G  234G   3% /
tmpfs                               16G     0   16G   0% /dev/shm
tmpfs                              5.0M     0  5.0M   0% /run/lock
tmpfs                               16G     0   16G   0% /sys/fs/cgroup
/dev/sda2                          976M  104M  806M  12% /boot
/dev/loop0                          56M   56M     0 100% /snap/core18/1944
/dev/loop2                          32M   32M     0 100% /snap/snapd/10707
/dev/loop1                          70M   70M     0 100% /snap/lxd/19188
tmpfs                              3.2G     0  3.2G   0% /run/user/1000
```
- презагружаемся `reboot`  

    reboot
---
<a name="proxy"><H3>3. Proxy</H3></a>
добавляем в файл `.bashrc` переменные Proxy
```
cat <<EOF | tee -a ~/.bashrc
export no_proxy="localhost,127.0.0.1,10.0.0.0/8,10.96.0.0/12,192.168.0.0/16,*.mts.ru"
export http_proxy="http://10.47.240.250:9999/"
export https_proxy="http://10.47.240.250:9999/"
export ftp_proxy="http://10.47.240.250:9999/"
export rsync_proxy="http://10.47.240.250:9999/"
EOF
```
---
<a name="apt"><H3>4. APT установщик пакетов </H3></a>
```
dpkg-query -l
apt list --installed
apt list --installed | grep php
```
```
apt-cache search mysql
apt-cache show mysql-common
```
Прежде всего полностью обновим текущую систему
```
apt update && apt upgrade && apt dist-upgrade && apt --purge autoremove
```


---
<a name="mysql"><H3>5. Установка MYSQL Debian </H3></a>
```
wget https://repo.mysql.com//mysql-apt-config_0.8.19-1_all.deb
dpkg -i mysql-apt-config*
```
```
sql_mode=ONLY_FULL_GROUP_BY,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION
```

```
cat /etc/*release
cat /proc/version
journalctl -u containerd -l
```
```
iptables-save > ~/iptables_rules2.ipv4
```

- Добавить нужные пакеты
```
apt install -y mc htop wget libseccomp2 ca-certificates curl gnupg lsb-release bash-completion tmux sudo git
```
- Добавить PATH
```
export PATH=$PATH:/tmp/etcd-download-test:/usr/local/go/bin
cat <<EOF | tee -a ~/.bashrc
export GOPATH=~/go
export PATH=$PATH:/usr/local/go/bin
which etcd
```

- комбинацию команд, чтобы посмотреть, кто в данной директории занимает больше всего места. 
	Директории выстроятся в список, начиная с самой объемной и далее. 
	В моем примере будут выведены 10 самых больших папок в каталоге.
```
du . --max-depth=1 -ah | sort -rh | head -10
```

# Уменьшение VM диска после удаления файлов. Забить нулями файл, и удалить его. VMware сделать процедуру defrag and compact.
- Перейдите в корневую папку вашего диска и запустите эту команду:
```
for i in $(seq 1 //DISKSPACE//); do dd if=/dev/zero of=emptyfile${i} bs=1024 count=1048576; done; rm emptyfile*;
```
где // DISKSPACE// - это размер вашего жесткого диска в ГБ.
