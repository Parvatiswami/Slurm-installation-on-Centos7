# Slurm-installation-on-Centos7

Slurm installation:

on Master:
[root@localhost ~]# hostnamectl set-hostname master
[root@localhost ~]# su
[root@master ~]# systemctl stop firewalld
[root@master ~]# systemctl disable firewalld
[root@master ~]# systemctl status firewalld

[root@master ~]# vim /etc/selinux/config
SELINUX=disabled

Node-1 &amp;Node-2
[root@localhost ~]# hostnamectl set-hostname node1
[root@localhost ~]# su
[root@node1 ~]# systemctl stop firewalld
[root@node1 ~]# systemctl disable firewalld

[root@node1 ~]# systemctl status firewalld

[root@node1 ~]# vim /etc/selinux/config
[root@node1 ~]# reboot

Node2

[root@node2 ~]# vim /etc/hosts

[root@node2 ~]# scp /etc/hosts root@node1:/etc/hosts
[root@node2 ~]# scp /etc/hosts root@master:/etc/hosts

Master:
Mounting
[root@master ~]# yum update -y
[root@master ~]# rpm -qa|grep nfs

nfs4-acl-tools-0.3.3-21.el7.x86_64
libnfsidmap-0.25-19.el7.x86_64
nfs-utils-1.3.0-0.68.el7.x86_64
[root@master ~]# yum install epel-release -y
[root@master ~]# yum install nfs-utils –y
[root@master ~]# mkdir home1
[root@master ~]# vim /etc/exports

[root@master ~]# exportfs -a
[root@master ~]# exportfs -arv
exporting *:/root/home1
node1-node2
[root@node1 ~]# mount -t nfs master:/root/home1 /root/home1
[root@node1 ~]# df -Th
Filesystem Type Size Used Avail Use% Mounted on
devtmpfs devtmpfs 894M 0 894M 0% /dev
tmpfs tmpfs 910M 0 910M 0% /dev/shm
tmpfs tmpfs 910M 11M 900M 2% /run
tmpfs tmpfs 910M 0 910M 0% /sys/fs/cgroup
/dev/mapper/centos-root xfs 17G 4.8G 13G 28% /
/dev/sda1 xfs 1014M 226M 789M 23% /boot
tmpfs tmpfs 182M 28K 182M 1% /run/user/0
/dev/sr0 iso9660 4.4G 4.4G 0 100% /run/media/root/CentOS 7 x86_64
master:/root/home1 nfs4 17G 4.9G 13G 29% /root/home1
[root@node2 ~]# mount -t nfs master:/root/home1 /root/home1
[root@node2 ~]# df -Th
Filesystem Type Size Used Avail Use% Mounted on
devtmpfs devtmpfs 894M 0 894M 0% /dev
tmpfs tmpfs 910M 0 910M 0% /dev/shm
tmpfs tmpfs 910M 11M 900M 2% /run
tmpfs tmpfs 910M 0 910M 0% /sys/fs/cgroup
/dev/mapper/centos-root xfs 17G 4.8G 13G 28% /
/dev/sda1 xfs 1014M 226M 789M 23% /boot
tmpfs tmpfs 182M 24K 182M 1% /run/user/0
/dev/sr0 iso9660 4.4G 4.4G 0 100% /run/media/root/CentOS 7 x86_64
master:/root/home1 nfs4 17G 4.9G 13G 29% /root/home1

[root@master ~]# yum -y install chrony
[root@master ~]# vim /etc/chrony.conf
1.comment all server
2.uncomment allow network
3.uncomment local stratum

[root@master ~]# systemctl restart chronyd
[root@master ~]# systemctl enable chronyd
[root@master ~]# chronyc -a tracking :to check time synchronization
Node1-2:
[root@node1 ~]# ntpdate -u master :force to synchronise time to node
Go to browser: slurm download
Download schedmd
Download the tar file
OR
wget http://www.schedmd.com/download/latest/slurm-15.08.9.tar.bz2
[root@master ~]# yum install epel-release
[root@node1 ~]# yum install munge munge-libs munge-devel –y
[root@node2 ~]# yum install munge munge-libs munge-devel –y
Master:
[root@master ~]# yum install mariadb-server mariadb-devel –y
[root@master ~]# yum install munge munge-libs munge-devel –y
[root@master ~]# yum install rpm-build –y
[root@master ~]# rpmbuild -ta /root/Downloads/slurm-22.05.8.tar.bz2
error: Failed build dependencies:
python3 is needed by slurm-22.05.8-1.el7.x86_64
readline-devel is needed by slurm-22.05.8-1.el7.x86_64
perl(ExtUtils::MakeMaker) is needed by slurm-22.05.8-1.el7.x86_64
pam-devel is needed by slurm-22.05.8-1.el7.x86_64
install above dependencies
[root@master ~]# yum install python3 –y
[root@master ~]# yum install readline-devel* -y

[root@master ~]# yum install perl-ExtUtils-Install –y
[root@master ~]# yum install pam-devel –y
[root@master ~]# yum install gcc –y
[root@master ~]# rpmbuild -ta /root/Downloads/slurm-22.05.8.tar.bz2
[root@master ~]# /usr/sbin/create-munge-key -r
Please type on the keyboard, echo move your mouse,
utilize the disks. This gives the random number generator
a better chance to gain enough entropy.
Generating a pseudo-random key using /dev/random completed.
[root@master ~]# ll /dev/ra
random raw/
[root@master ~]# ll /dev/random
crw-rw-rw- 1 root root 1, 8 Jan 31 18:38 /dev/random
[root@master ~]# ll /etc/mu
multipath/ munge/
[root@master ~]# ll /etc/munge/
total 4
-r-------- 1 munge munge 1024 Jan 31 21:04 munge.key
[root@master ~]# scp /etc/munge/munge.key root@node1:/etc/munge
root@node1&#39;s password:
munge.key 100% 1024 1.0MB/s 00:00
[root@master ~]# scp /etc/munge/munge.key root@node2:/etc/munge
[root@master ~]# chown -R munge:munge /etc/munge/
[root@master ~]# ls -la /etc/munge/
total 16
drwx------ 2 munge munge 23 Jan 31 21:04 .
drwxr-xr-x. 145 root root 8192 Jan 31 20:47 ..
-r-------- 1 munge munge 1024 Jan 31 21:04 munge.key
[root@master ~]# chmod 400 /etc/munge/munge.key
Node1-2:
[root@node1 ~]# chown -R munge:munge /etc/munge/
[root@node1 ~]# chmod 400 /etc/munge/munge.key
Master:
[root@master ~]# systemctl start munge.service

[root@master ~]# systemctl status munge.service

[root@master ~]# cd /root/rpmbuild/RPMS/x86_64/
[root@master x86_64]# yum install slurm* -y
[root@master x86_64]# scp -r /root/rpmbuild/RPMS/x86_64 root@node1:/home/
[root@master x86_64]# scp -r /root/rpmbuild/RPMS/x86_64 root@node2:/home/

Node1-2:
[root@node2 ~]# cd /home/
[root@node2 home]# ls
hpcsa x86_64
[root@node2 home]# cd /home/x86_64/
[root@node2 x86_64]# yum install slurm* -y
AllNodes:
[root@master ~]# export SLURMUSER=1007
[root@master ~]# groupadd -g $SLURMUSER slurm
[root@master ~]# useradd -m -c &quot;SLURM workload manager&quot; -d /var/lib/slurm -u $SLURMUSER -g slurm -s /bin/bash slurm
[root@master ~]# cat /etc/passwd

[root@master ~]# cat /etc/group

Master:
[root@master ~]# cp /etc/slurm/slurm.conf.example /etc/slurm/slurm.conf
[root@master ~]# mkdir -p /var/share/slurm/ctld
[root@master ~]# chown -R slurm:slurm /var/share/slurm/
[root@master ~]# cd /etc/slurm/
[root@master slurm]# ls
cgroup.conf.example cli_filter.lua.example job_submit.lua.example prolog.example slurm.conf slurm.conf.example
slurmdbd.conf.example
[root@master slurm]# vim slurm.conf

Node1-2:
[root@node1 ~]# mkdir -p /var/share/slurm/d
[root@node1 ~]# chown -R slurm:slurm /var/share/slurm/d
[root@node1 ~]# slurmd -C
NodeName=node1 CPUs=1 Boards=1 SocketsPerBoard=1 CoresPerSocket=1 ThreadsPerCore=1 RealMemory=1819
UpTime=0-03:58:33 :copy first line and paste at slurm.conf file in compute node
[root@node2 ~]# slurmd -C
NodeName=node2 CPUs=1 Boards=1 SocketsPerBoard=1 CoresPerSocket=1 ThreadsPerCore=1 RealMemory=1819
UpTime=0-01:02:45
Comment out Nodename=linux

Master:
[root@master slurm]# scp /etc/slurm/slurm.conf root@node1:/etc/slurm/slurm.conf
root@node1&#39;s password:
slurm.conf 100% 3232 3.4MB/s 00:00
[root@master slurm]# scp /etc/slurm/slurm.conf root@node2:/etc/slurm/slurm.conf
root@node2&#39;s password:
slurm.conf 100% 3232 2.0MB/s 00:00
[root@master ~]# cp /etc/slurm/cgroup.conf.example /etc/slurm/cgroup.conf
[root@master ~]# scp /etc/slurm/cgroup.conf.example root@node1:/etc/slurm/cgroup.conf
[root@master ~]# scp /etc/slurm/cgroup.conf.example root@node2:/etc/slurm/cgroup.conf

[root@master slurm]# systemctl start slurmctld
[root@master slurm]# systemctl start munge
Node1-2:
[root@node1 ~]# systemctl start slurmd
[root@node1 ~]# systemctl start munge
[root@node1 ~]# systemctl status munge.service

If there are any error look into log file:
[root@master ~]# vim /var/log/slurmctld.log

Master:
[root@master ~]# sinfo
If want to make unk node to idle then
scontrol update nodename=node1 state=idle
scontrol update nodename=node2 state=idle

Job submission:
[root@master ~]# srun -N1 /bin/sh job to open shell

Job is in progress
Job submission by batch scripting: this job submission can be done from both master and nodes
[root@master ~]# vim sbatchscrpt.sh
#!/bin/bash
# Job name:
#SBATCH --job-name=test
#
# Account:
#SBATCH --account=account_name
#
# Partition:
#SBATCH --partition=partition_name
#
# Request one node:
#SBATCH --nodes=1
#
# Specify one task:

#SBATCH --ntasks-per-node=1
#
# Number of processors for single task needed for use case (example):
#SBATCH --cpus-per-task=4
#
# Wall clock limit:
#SBATCH --time=00:00:30
#
## Command(s) to run (example):
export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK
./a.out
And add two lines:
/bin/hostname
sleep 60

[root@master ~]# sbatch sbatchscrpt.sh :this can be run on nodes or master
Submitted batch job 11
[root@master ~]# squeue
JOBID PARTITION NAME USER ST TIME NODES NODELIST(REASON)
11 debug test root R 0:14 1 node2
[root@master ~]# sinfo
PARTITION AVAIL TIMELIMIT NODES STATE NODELIST
debug* up infinite 1 down* node1
debug* up infinite 1 alloc node2
node2:
[root@node2 ~]# scontrol show job 11
[root@node2 ~]# ls
anaconda-ks.cfg Desktop Documents Downloads home1 initial-setup-ks.cfg Music Pictures Public sbatchscrpt.sh slurm-
11.out slurm-12.out Templates Videos

Partitioning:
Master:
[root@master ~]# vim /etc/slurm/slurm.conf

[root@master ~]# systemctl restart slurmctld
[root@master ~]# systemctl restart munge.service
[root@master ~]# systemctl status slurmctld munge
Node1-2:
[root@node1 ~]# systemctl restart slurmd
[root@node1 ~]# systemctl restart munge
[root@node1 ~]# systemctl status munge slurmd
Master:
[root@master ~]# sinfo
[root@master ~]# scontrol update nodename=node1 state=idle
[root@master ~]# scontrol update nodename=node2 state=idle
[root@master ~]# sinfo
[root@master ~]# scontrol show node node1
[root@master ~]# scontrol show part medium

Reservation:
Master:
[root@master ~]# scontrol create reservation starttime=2023-02-01T12:35:00 duration=5 user=root flags=maint,ignore_jobs
nodes=ALL
Reservation created: root_1 ###reservation of all node for 5 minut
[root@master ~]# scontrol show reservation

[root@master ~]# sinfo

The &quot;maint&quot; flag is used to identify the reservation for accounting purposes as system maintenance.
# Create a two core reservation for user alan

$ scontrol create reservation StartTime=now Duration=60 \

NodeCnt=1 CoreCnt=2 User=alan

# Create a reservation for user brenda with two cores on

# node tux8 and 4 cores on node tux9

$ scontrol create reservation StartTime=now Duration=60 \

Nodes=tux8,tux9 CoreCnt=2,4 User=brenda
[root@master ~]# scontrol create reservationname=test start=now duration=1 user=hpcsa partition=small tres=cpu=1,node=1
Creating node reservation for 1 cpu for hpcsa user for 1 min
[root@master ~]# scontrol show reservation
[root@master ~]# sinfo

Reservation of the node for sbatch:
[root@master ~]# scontrol create reservation starttime=2023-02-01T13:20:00 duration=5 user=root NodeCnt=1 Partition=small
First create the reservation for particular time, particular user, and number of node
[root@master ~]# scontrol show reservation
To see info about the reservation also reservation name created by sluurm
[root@master ~]# sbatch --reservation=root_3 sbatchscrpt.sh
After it we will run the script with a particular reservation name
[root@master ~]# squeue

To update the reservation:
scontrol update ReservationName=root_3 \

duration=150 users=admin
to delete the reservation:
[root@master ~]# scontrol create reservationname=test start=now duration=5 user=hpcsa partition=small tres=cpu=1,node=1
[root@master ~]# sinfo
[root@master ~]# scontrol show reservation
[root@master ~]# scontrol delete ReservationName=test
[root@master ~]# sinfo

Accounting in slurm:
[root@master ~]# vim /etc/slurm/slurmdbd.conf
Uncomment storagetype,storagehost,storagreuser,storageloc
[root@master ~]# chown slurm:slurm /etc/slurm/slurmdbd.conf
[root@master ~]# systemctl restart slurmdbd mariadb
[root@master ~]# systemctl status slurmdbd mariadb
Also comment the #storagePass=Password

[root@master ~]# vim /etc/slurm/slurm.conf
add accountstoragehost=localhost, acccountstorageuser=slurm,accountstoragetype=accounting_storage/slurmdbd

[root@master ~]# scp /etc/slurm/slurm.conf root@node1:/etc/slurm/slurm.conf :both node
[root@master ~]# systemctl restart slurmdbd mariadb
[root@master ~]# systemctl status slurmdbd mariadb
[root@master ~]# mysql
MariaDB [(none)]&gt; create user &#39;slurm&#39;@&#39;localhost&#39;;
MariaDB [(none)]&gt; grant all on slurm_acct_db.* To &#39;slurm&#39;@&#39;localhost&#39;;
MariaDB [(none)]&gt; create database slurm_acct_db;
MariaDB [(none)]&gt; show database;

MariaDB [(none)]&gt; exit
[root@master ~]# systemctl restart slurmdbd mariadb
[root@master ~]# sacctmgr
User creation,account creation. Allocate account user
sacctmgr: show cluster .
sacctmgr: add cluster slurm1
sacctmgr: show cluster
sacctmgr: show user
sacctmgr: show account
sacctmgr: add account slurm_acc
sacctmgr: show account

# squeue|wc –l submitted job
[root@master ~]# squeue|grep R|wc –l running job
QOS creation:
[root@master ~]# sacctmgr
sacctmgr: show qos
sacctmgr: create qos sample maxwall=2-00:00:00
sacctmgr: show qos
sacctmgr: create qos sample1 Priority=1000
sacctmgr: show qos
