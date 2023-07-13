//you need to learn this file only to pass the exam//


Q.1  setup an IP adress for primary virtual machine ?

Given:- ip address 172.25.x.11 subnetmask 255.255.255.0 default gateway 172.25.25.254.254 and hostname as primary.net.example.com


Ans:- for this question we need to use graphicaly methad after set all information manualy remmeber that we sholud off automatic every where and in the end we need to trun off and than on the internet.

ifconfig 
ip addr //both for ip check

hostname // for check hostname
hostnamectl set-hostname 'hostname' 	->>for set hostname





Q.2 Yum repository configration on both machine

Ans:- first we  need to make a directory in root path 
mkdir "dir_name" // to make directory

//we need to mount iso file for this go to setting->storage>empty->click on disk i right side->select iso file->ok

now we need to mount this disk
lsblk / for see the disk mount or not if mount than it show in the last at sr0

now we need to mount is at specific path
mount /dev/sr0 /mnt // for mount disk at /mnt path
to permanently mount-> cd /root >> vim .bashrc 
add mount dev/sr0 /mnt at last

vim /etc/yum.repos.d/"dir_name.repo // to open repo file it is mandatory to same name to dir and repo file
 we need to edit this file by following this commands
//we need to press i in go for insert mod in editor
[AppStream]
name=AppStream
baseurl = file:///mnt/AppStream
gpgcheck=0
enabled=1

[BaseOS]
name=BaseOS
baseurl = file:///mnt/BaseOS
gpgcheck=0
enabled=1

then for save it we need to press esc->:->w->q->!->enter // it show in the left bottom corner :wq! then enter
yum repoinfo //for config repo file inn exam we need to run only repoinfo do not use yum before repoinfo




Q.3 for change port of httpd

yum install httpd -y //for install httpd
semanage port -l | grep http (to see all ports)
man semanage  port 
//1. shift+g // to go in the end
//2.copy-> semanage  port -a -t http port t -p tcp port "port_number" 
systemctl restart httpd // for restart httpd service
systemctl enable --now httpd // for enable/restart httpd service 

man firewall-cmd ->scroll up from last(above the table)-> example 2 -> copt parmnt
firewall-cmd  --reload
firewall-cmd --list-all
cd /var/www/html
ls-lZ -> find which file's context is different 
chcon -t part of context which is different file_name

systemctl status httpd // to check the status of httpd



Q.4 configure a cronjob on primary machine

Ans. crontab -e // to enter editor of crontab for root user or primary machine
     crontab -eu "username"// to enter editor of specific user

in editor sequence of column are following 
syntax minutes hours dayofmonth monthofyear dayofweek command (MHDMDC)
		5    7     5         *             */3      /usr/bin/echo hellow      // here are * means every monnth of year and */3 means every 3 day of week

NOTE:- we should not enter syntex in editor 
now save file and can check cron job
crontab -l -u "username" 




Q5. Create the following users,groups,and group membership:-

Ans:- A group named sysadmin. A user Natasha who belongs to sysadmin as asecondry group. A user Sarah who also belongs to sysadmin as a secondry group. A user Harry who does not have access to an intractive shell on the system, and who is not a member of sysadmin. Natasha and Harry should all have the password of atenorth.

groupadd sysadmin
useradd -G sysadmin natasha // -G for secoudry group and -g for primary group
useradd -G sysadmin sarah  //
useradd -s /sbin/nologin "username"  //for user add without give login credentials //s stands for shell
usermod -s /sbin/nologin "username" //for modification of user to remove login credentials
usermod -s /bin/bash "username" //for modification of user to give login credentials
passwd "username"

passwd "sarah"
passwd "harry"

ctrl+d or exit( //for logout 
we can also type logout //for logout

user_name:password:uid:dir // in passwd file save information of users in this syntex
NOTE:- information of users save in /etc/passwd ot /home file
information of group save in /etc/group file

cat "file_name"  // for read filevalues without open that
example:- cat /etc/group or tail -5 /etc/group //to show information of groups



Q6. Create a collaborative direction "/common/admin" with the following characteristcs:
Ans:-	 Group ownership if /common/admin is sysadmin. The directory should br readable,writable, and accessible to members of sysadmin, but not to any other user (it is understood thtroot has access to all files and directions on the system.) Files created in/common/admin automatically have group ownership set to the sysadmin group.

mkdir -p /common/admin  //-p for parent directery
chgrp "groupname" "path" //for for change group owner
example :- chgrp sysadmin /common/admin 
chmod "permission" "path" // for modification in permisiion of group
example :- chmod 2770 /common/admin  (r=4,w=2,x=1 suid=4,sgid=2,sticky bit (T)=1 )
ll to check
setfacl -m g:"groupname":"permission" "path"  //-m for modification; g for group;
getfacl path/.->to check group permissions
example:- setfacl -m:sysadmin:rwx /common/admin/

facl //used for gives permission for specific user





Q.7 -->> Configure NTP in your system so that it is an NTP client of classroom.example.com ?
ntp pacage name crony
Ans :- yum install chrony -y
  vim /etc/chrony.conf // it is a file where save 
commant line which statart from pool ///comment this line and add following line
server "server_name" iburst
savefile
systemctl start chronyd // d for demoan   
system enable chronyd
chronyc sources -v  [for check]


*****************find use for find file and greb use for find string ************************

Q.8 Find the files in your system which is owned by simone user & copy all files on /root/found directory?
mkdir /root/found
find / -user simone -exec cp -rvf {} /root/found \; //here ; is also include in command //-r for recursive; v for vervoves means it shoe files that are oprate are showen on screen; -a for also copy files permisiions etc; {} stands for encloes all files  in given directory;





Q.9 	->> Find the string "strato" from /usr/share/dict/word/file and save the result in searchfile

grep "string" "pathoffile" //for find the string in given file
example:- grep strato /user/share/dict/words
grep "string" "pathoffile" > "found file" // found file stands for a file where we want to store output
example:- grep strato /user/share/dict/words > /searchfile  			//grep for find string; strato is a string; 

Q.10 :- Create a user barry user id of the user is 2112 and set it's pass atenorth

useradd "uid" "username"
example:- useradd 2112 barry
passwd "password"
example: - passwd atenorth


Q.11 using automounter service mount remoteuserX onto the provider folder /our/remoteuserX

check-> df -h

Q.12 sudo privillege a group name is 'elite' they have to give administrative permission without password?
soln.
groupadd elite
useradd -G elite john
visudo 
go to last line
copy %wheel ... line >> paste below it without #
change wheel to elite(group name) >>save
su john
sudo ls (try sudo commands) 


Q.13 simple shell script 
write a script mysearch to list the contents of /users that are smaller 10MB and get group id (SGID) permission the script should be present in  /usr/local/bin After  exect on, the script should automatically write all the lines and save it to /root/lines

find /usr -size -10M  //for find all files smaller than 10mb in given dir //-size opetion used to constraints of size
touch /root/lines
vim /usr/local/bin/myscript(script name) // create script
    #!/bin/bash
    find /usr -size -10M  -perm -g=s > /root/lines >>save
chmod 2777 /usr/local/bin/myscript (give execution permission)
myscript ( run script)
 cat /root/lines (check)



Q.14Build Application 
-build an apllication testapp that priint the message when user1 loggend in "welcome to user1"
useradd (name) >> login
mkdir .testapp
vim testapp/testapp.conf >>enter the message which is shown when logedin in  user 
vim /bin/testapp >>enter the message which is shown when logedout in  user
from root
useradd bulla
su bulla
cd /home/bulla
mkdir .testapp
cd .testapp
vim testapp.conf
enter here message and save it
exit 
in root
vim /bin/testapp
entries:
#!/bin/bash
if [ -f ~/.testapp/testapp.conf ]
then 
     cat ~/.testapp/testapp.conf
else 
	echo "no ,message"
fi
//save 
chmod a+x /bin/testapp
testapp -> (message in /bin/testapp)
su bulla
testapp->(message in .testapp/testapp.conf)

___________________________________________________________________________100 MarksQ 15,16_________________________________________________________________________________________________
Q.15 Create partition of Hard Disk, make volume group,swap memory.
soln.
[A] TO create partition
attach disk in SATA
fdisk /dev/sdb
m >> n >> enter >
+(size)
m>>w
mkfs.option(to make flie, option->xfs/xms)>>fill entries
mkdir /database
mount (path) (directory)
vim /etc/fstab
# fill entries >>uuid or (path)  "mount_address"  type  auto   default    0   0
mount -a (to verify entries)
 

[B] TO create Volume Group (vg):-
pvcreate /dev/sdb1(path) (to create physical volume of sdb1)
pvcreate /dev/sdb2(path) (to create physical volume of sd2)
pvs(to show pvs)
pvdisplay(for detail info of partition)
vgcreate -s 8M myvol /dev/sdb1 /dev/sdb2 (to create volume group pe ,1 pe=8MB ) 
#physical extent - size of memeory blocks
vgs
vgdisplay
lvcreate -l 50 -n partition_name/dev/myvol (lv->to create logical volume,-l->physical extent of 50 MB=50x8=400MB,-n->name,)
mkfs.vfat /dev/myvol/mylv
mkdir /dir
mount (path) (directory)
vim /etc/fstab -> fill entry
TO resize memory 
lvextend -rL +500M(amount to extend) (path)   //to extend
lvreduce -rL -500M(amount to extend) (path)   //to
lvresize -rL +200M/-200M(amount to extend) (path) // to extend or reduce 200MB

In short 			
{ pvcreate <one_path>
.
.
pvcreate <n_path>
vgcreate <name> <path>........<n_path>
lvcreate -L +<size> -n <name> <path>
to extend:-
lvextend -rL +<size> <path> } 

[C] To swap memory
mkswap (path)/dev/sdc1wapon (to on swap //partiton stands for sdc1 etc n)
 copy uuid
vim /etc/fstab
fill entries-> (uuid)   swap    swap    defaults  0   0 >>save
swapon -a >>copy uuid
lsblk


CONTAINER Theory
 
Q.16 To download image from given link
soln.
adduser tushar (already added in exam)
vim /home/tushar/Dockerfile
FROM   docker.io/library/httpd:latest >> save
login ->ssh tushar@localhost (only localhost) (ssh-> secure shell)
download file-> wget http://server.com/Dockerfile. ( given link)
podman build . -t watcher    (. to search correct file)
podman image rm _name(to remove image if incorrect)
podman image ls
rpm -q podman (to check is podman installed or not)

b. To run a root less container with name sky2pdf and mount on /opt/files to data/input (path of directories can be change)
in root-
mkdir -p /opt/files
mkdir -p /opt/processed
ls -l /opt
chown andrew:andrew /opt/files/
chown andrew:andrew /opt/processed/
in user
podman container run -d --name sky2pdf -v /opt/files:/data/incoming:Z -v /opt/processed:/data/output watcher(-d for run in bkgrnd)
podman ps(to check container is running?)
podman ps -a (show all container)
podman container rm _name(to remove container if incorrect)
podman ps

# location ~/.config/systemd/user
mkdir -p ~/.config/systemd/user
cd ~/.config/systemd/user
pwd (to verify)
podman generate systemd --name sky2pdf --new --files
ls-> container-sky2pdf-service
# systemctl --user daemon-reload ( to use systemctl for particular user)
systemctl --user  enable container-sky2pdf.service
systemctl --user restart container-sky2pdf.service
loginctl enable-linger tushar(_name)
reboot
loginctl show-user user_name
podman ps

ls /opt/processed/
echo "hwllo world"> /opt/files/hello.txt
ls /opt/processed/

____________________________________________________________________________________________________________________________________________________________________________________________

Q 17 Create a backup.tar.(bz2 or gz) of /etc directory in /home location 
soln.
tar -cvjf/-cvzf /home/backup.tar.bz2/gz /etc
check-> ll /home

Q 18 Set a recommended tuning profile for your system. 
(profile already available)
soln.
tunned-adm recommend
a profile will appear eg. virtual-guest
tunned-adm profile virtual-guest(type profile)
check-?>tunned-adm active

                              
                                                                                            EXAM INTERFACE
                    
imp config. -> root's pass
vm manager -> node 1&2
node 1
console
login in root
GUI init 5
node 2
console
reboot
pass break
login in root
init 5

       errors may occurs
READ Qs KRFULLY
error 404 -> check link
backup tar in gz:- man tar -> /bz(what u lokkin)
umask rrr perm:-login in user-> vim .bashrc -> at last->umask 022(777-perm)
script perm:- -g=s
andrew pass	:- not require
systemctl start then enable 

1 chown owner_username user_name -> to change owner of a user
2 To run multiple command in one line, add ';' after every command or && to execute second command only when first runs
3 To download file->wget (url)
4 To check any service-> systemctl status httpd(servicename) (d stands for service)
  to stop/start a service -> systemctl stop/start (servicename)  
5 To copy files from one path to another-> cp path1  path2
6 TO give special permissions to a group/user setfacl -m g:group/user_name:(permissions) (path)
7 ctrl + d for logout to localuser
8 rpm -q (package name like:- podman,chroyd,httpd....		









