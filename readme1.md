Q1: Recover Root password
     ON SERVER1 
rd.break
chroot /sysroot
mount -o remount,rw /
passwd
touch /.autorelabel
exit
reboot
     ON SERVER 2
rd.break
chroot /sysroot
mount -o remount,rw /
passwd
touch /.autorelabel
exit
reboot


Q2: Configure Network
     OM  SERVER 1
nmtui
cd /etc/sysconfig/network-script
nmcli con down ens..
nmcli con up ens..
ip a
ping 2ndserverip  (at the exam)

     ON SERVER2
nmtui
cd /etc/sysconfig/network-script
nmcli con down ens..
nmcli con up ens..
ip a
ping 2ndserverip  (at the exam)

Q3: Change hostname
   ON SERVER 1
hostnamectl set-hostname exam1.tech.local
systemctl restart systemd-hostnamed.service 
bash

   ON SERVER 2
hostnamectl set-hostname exam2.tech.local
systemctl restart systemd-hostnamed.service 
bash  

Q4: Configure Yum repository using these links
http://repo8.linuxtechnologies.us/AppStream
http://repo8.linuxtechnologies.us/BaseOS
   ON SERVER 1
vim /etc/yum.repos.d/karen.repo
[base]
name=brepo
baseurl=http://repo8.linuxtechnologies.us/BaseOS
enable=1
gpgchech=0

[app]
name=arepo
baseurl=http://repo8.linuxtechnologies.us/AppStream
enabled=1
gpgcheck=0

yum clean all; yum repolist

    ON SERVER 2
vim /etc/yum.repos.d/kaKA.repo
[base]
name=brepo
baseurl=http://repo8.linuxtechnologies.us/BaseOS
enable=1
gpgchech=0

[app]
name=arepo
baseurl=http://repo8.linuxtechnologies.us/AppStream
enabled=1
gpgcheck=0

yum clean all; yum repolist

Q5: Configure cron job (SERVER1 ONLY) 
Configure a cron job to run the command uptime and send the output to /root/uptime.txt every 5 minutes everyday
    ON SERVER 1
crontab -e
*/5 * * * * uptime >> /root/uptime.txt
crontab -l

Q7: Users & Groups (SERVER1 ONLY)
- Create user john with RedHat1 as password
- Create user joe with 5001 as uid and RedHat1 as password. The user joe belongs to the group dba
- Create user cathy with RedHat1 as password. The user cathy is not allowed to login into the server. 
  The user cathy belongs to the group finance
useradd john
passwd john
useradd -u 5001 joe 
passwd joe
groupadd dba
gpasswd -a joe dba
id joe
useradd -s /sbin/nologin cathy
passwd cathy
groupadd finance
gpasswd -a cathy finance
id cathy

Q8: Configure permissions  (SERVER1 ONLY)
- copy /etc/fstab to /var/tmp/fstab
- Owner and group owner of the file created should be retained by root user
- Others should have read permission on the file
- john should have neither read nor write access to the file
- joe should have read and write access to the file
- user cathy should not have any access
cat /etc/fstab
cp -rp /etc/fstab /var/tmp/fstab
cat /var/tmp/fstab

ll /var/tmp/fstab

getfacl /var/tmp/fstab
setfact -m u:john:--- /var/tmp/fstab
getfacl /var/tmp/fstab

setfact -m u:joe:rw- /var/tmp/fstab
getfacl /var/tmp/fstab

setfact -m u:cathy:--- /var/tmp/fstab
getfacl /var/tmp/fstab

Q9: Configure Group Collaboration (SERVER1 ONLY)
- Group ownership of /finance is finance. The directory should be readable, writable and
  accessible to members of finance group, but not to any other user.
- Files created in /finance automatically have group ownership set to the finance group
ll /finance 
mkdir /finance
ll -d /finance
chown -R :finance /finance 
ll -d /finance
chmod -R 2070 /finance 
ll -d /finance 
touch /finance/testfile
ll /finance/estfile

Q10: Configure NTP (ON SERVER 1&2)
Configure the server to use time.apple.com as ntp server
        ON SERVER 1
yum install chrony
systemctl --now enable chronyd
systemctl status chronyd
vim /etc/chrony.conf
yyp
server time.apple.com iburst 
systemctl restart chronyd
chronyc sources 
chronyc status 
chronyc tracking

        ON SERVER 2
yum install chrony
systemctl --now enable chronyd
systemctl status chronyd
vim /etc/chrony.conf
yyp
server time.apple.com iburst 
systemctl restart chronyd
chronyc sources 
chronyc status 
chronyc tracking
NB:complete ntp config on server2

Q11:FIND (ON SERVER 1)
Use find command to search for all files owned by user user1 and copy them into /root/find directory
         ON SERVER 1
mkdir /root/find
ll -d find
find / -user user1 -exec cp -rp {} /root/find \;
ll -a find 

Q12:GREP (ON SERVER 1)
Use the command grep to search for the word bin in /etc/passwd file and send the output into /root/grep.txt file
grep -w "bin" /etc/passwd >> /root/grep.txt
cat grep.txt

Q5:TUNING.PROFILE (SERVER 2 ONLY)
Q: Set the recommendated tuning profile for your system
yum install tuned 
systemctl --now enable tuned
systemctl status tuned
tuned-adm recommnend  --> the check the recommended profile
tuned-adm active    --> to check the active profile 
tuned-adm profile virtual-guest  -->change the profile to virtual-guest as its the recommmended profile. 
tuned-adm active  --> to check if the change is effective.

Q6: Managing Storage (SERVER 2 ONLY) 
Create a swap partition of 1G and make it available at next reboot
lsblk 
cfdisk /dev/nvme0n2
fdisk /dev/nvme0n2
p
enter
enter
+1G
enter 
t
2
82 (swap code)
p
w
lsblk 
mkswap /dev/nvme0pn2p2
free -g
vim /etc/fstab
/dev/nme0n2p2 swap swap defaults 0 0 
swapon -a
free -g
Q: Create a volume group with the name vgexam 8MiB PE and create the lvm name lvexam with PE 120 PE. 
create a vfat file system and mount it in /exam. This mount must be permament 
lsblk
cfdisk /dev/nvme0n2   --> to check the available space size
fdisk /dev/nvme0n2
n
enter
enter
+3G
8e
p
enter
w 
pvcreate /dev/nvme0n2p3
pvs
vgcreate -s 8 vgexam /dev/nvme0n2p3 
vgdisplay vgexam
lvcreate -l 120 -n lvexam vgexam 
lvdisplay /dev/mapper/vgexam-lvexam
mkfs.vfat /dev/mapper/vgexam-lvexam
mkdir /exam
vim /etc/fstab
/dev/mapper/vgexam-lvexam /exam vfat defaults 0 0 
mount -a
df -h
Q: Resize lvm partition /finance to 3G
lsblk
lvs
cfdisk /dev/nvme0n2
fdisk /dev/nvme0n2
p
n
e
4
enter
p
n
5
enter
+1G  (has 2G already)
enter
p
t
8e
p
w
lsblk
partprobe /dev/nvme0n2   -->check if size has changed. Don't reboot the server at this time. 

Q: VDO 
yum install vdo kmod-kvdo  --> install the vdo, then restart the VM

          ON SERVER 1
Selinux
Q: Knowning that httpd runs on port 5214, make sure https is running and start at the server reboot
yum install httpd 
semanage port -l | grep 5214  -->to check if the port is in use
vim /etc/httpd/conf/httpd.conf
Listen 5214    --> comment out the remaining 
systemctl --now enable httpd
semanage port -a -t http_port_t -p tcp 5214 
systemctl --now enable httpd
systemctl status httpd
firewall-cmd --permanent --add-port=5214/tcp
firewall-cmd --reload
firewall-cmd --list-port
curl http://192.168.0.251:5214 
Q: httpd has 3 pages in the document root directory, make sure the 3 pages are accessible 
cd /var/www/html
ll
ll -Z
ll -Zd    -->should look like this
man semanage fcontent   -->to copy the right spelling of the commands in Examples
semanage fcontent -a -t httpd_sys_content_t "/var/www/html(/.*)?"
restorecon -R -v /var/www/html    --> to restore content
ll -Z
curl http://192.18.0251/page1.html   --> should see "page OK"
curl http://192.18.0251/page2.html
curl http://192.18.0251/page3.html
cd

     BACK TO SERVER 2
Reboot -f      --> picking it right where we left it at
pvcreate /dev/nvme0n2p5
vgextend vgfinance /dev/nvme0n2p5
vgs
lvextend -l +100%FREE /dev/mapper/vgfinance-lvfinance -r 
df -h

Q: Use the unpartitionned disk to create a VDO device with a logical size of 40GB and mount it in 
/examvdo as vdo service 
lsblk 
lsmod | grep kvdo 
modprobe kvdo    -->if nothing is showing
lsmod | grep kvdo
systemctl --now enable vdo
systemctl status vdo
vdo create --name=VDO --device=/dev/nvme0n3 --vdoLogicalSize=40G 
mkfs.xfs -K /dev/mapper/VDO
mkdir /examvdo
find / -iname "vdo.mount.example"
cd /usr/share/doc/vdo/examples/systemd
cp VDO.mount.example /etc/systemd/system/examvdo.mount
vim /etc/systemd/system/examvdo.mount   -->change only What and Where 
[mount]                     
What = /dev/mapper/VDO
Where = /examvdo
Type = xfs 
Options = discard
systemctl --now enable examvdo.mount
systemctl --status examvdo.mount
df -h

UPDATE THE KERNEL 
Q: 
uname -r 
yum update kernel 
reboot
uname -r 

     ON SERVER 2
NFS
Q:
yum install autofs nfs-utils
systemctl --now enable autofs
systemctl status autofs
showmount -e server1IP
vim /etc/auto.master
##copy paste the line #/misc   /etc/auto.misc  
## and modify 
/remote   /etc/auto.home
## then,
vim /etc/auto.home
user1 -fstype=nfs,nfsvers=3,rw,soft,intr  192.168.1.251:/home/user1
systemctl restart autofs
ll /remote/user1
df -h



     ON SERVER 1
CONTAINER 
Q: Create /svr/web directory anc 
nkdir -p /svr/web
ll -d /svr/web
yum install podman-docker
wget https://training.linuxtechnologies.us/test/web-content.tar.gz   /svr/web
cd /svr/web
tar xvf web-content.tar.gz 
useradd daniel
passwd daniel 
chown -R daniel:daniel /svr/web
ssh daniel@192.168.1.252
exit  -->swtich to root
loginctl show-user daniel 
loginctl enable-linger daniel 
show-user daniel    --Linger now set to YES
getsebool -a | grep container 
setsebool -P container_manage_cgroup on
getsebool -a | grep container    -->to verify
su - daniel
podman run -d -it --name=web -p 8080:8888 -v /svr/web:/var/www/html:Z registry.access.redhat.com/ubi8/php-74 /bin/bash
podman ps
mkdir -p .config/systemd/user
cd .config/systemd/user
podman generate systemd --name web --files
ll
vim container-web.service   -->exit 
podman stop web
padman ps   -->to check if podman has stopped
systemctl --user daemon-reload   -->if it donesn't work, exit and ssh back as user daniel 
exit
ssh daniel@192.168.1.252  -->use the user to SSH to the server 
systemctl -user daemon-reload
systemctl --user enable --now container-web.service 
systemctl --user status container-web.service 
podman ps   -->to check 






















































 


































































