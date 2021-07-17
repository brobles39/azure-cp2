#master
dnf install epel-release -y
dnf install ansible git tree jq -y
dnf install python36 -y
useradd -md /home/ansible ansible
passwd ansible
ssh-keygen -t rsa -b 4096
ssh-copy-id -i .ssh/id_rsa.pub ansible@ansibleclient
vi /etc/ssh/sshd_config
systemctl restartd sshd
#luego de instalar kubernetes
kubeadm config images pull
firewall-cmd --permanent --add-rich-rule 'rule family=ipv4 source address=10.0.1.11/32 accept'
firewall-cmd --permanent --add-rich-rule 'rule family=ipv4 source address=10.0.1.12/32 accept'
firewall-cmd --permanent --add-rich-rule 'rule family=ipv4 source address=10.0.1.13/32 accept'
firewall-cmd --zone=public --permanent --add-rich-rule 'rule family=ipv4 source address=172.17.0.0/16 accept'
firewall-cmd --reload
kubeadm init --pod-network-cidr 192.169.0.0/16
export KUBECONFIG=/etc/kubernetes/admin.conf
mkdir -p /root/.kube
cp -i /etc/kubernetes/admin.conf /root/.kube/config
chown $(id -u):$(id -g) /root/.kube/config
kubectl get nodes
#instalando sdn en azure
firewall-cmd --permanent --add-port=8285/udp
firewall-cmd --permanent --add-port=8472/udp
firewall-cmd --reload
kubectl apply -f https://docs.projectcalico.org/manifests/canal.yaml



#tareas comunes en todos las vms 
useradd -md /home/ansible ansible
passwd ansible
/etc/sudoers.d/ansible
ansible ALL=(ALL) NOPASSWD:ALL
vi /etc/ssh/sshd_config
passwordauthentication yes 
systemctl restartd sshd
vi /etc/hosts
10.0.1.10 master master.brian
10.0.1.11 worker-1 worker-1.brian
10.0.1.12 worker-2 worker-2.brian
10.0.1.11 worker-3 worker-3.brian
systemctl enable firewalld
systemctl start firewalld
modprobe br_netfilter
firewall-cmd --add-masquerade --permanent
firewall-cmd --reload
cat <<EOF > /etc/sysctl.d/k8s.conf
> net.bridge.bridge-nf-call-ip6tables = 1
> net.bridge.bridge-nf-call-iptables = 1
> EOF
sysctl --system
swapoff  -a
dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo
dnf install docker-ce-20.10.6-3.el8 -y
systemctl enable docker
systemctl start docker
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
> [kubernetes]
> name=Kubernetes
> baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
> enabled=1
> gpgcheck=1
> repo_gpgcheck=1
> gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
> exclude=kubelet kubeadm kubectl
> EOF
dnf install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
systemctl enable kubelet
systemctl start kubelet
firewall-cmd --permanent --add-port=6443/tcp
firewall-cmd --permanent --add-port=2379-2380/tcp
firewall-cmd --permanent --add-port=10250/tcp
firewall-cmd --permanent --add-port=10251/tcp
firewall-cmd --permanent --add-port=10252/tcp
firewall-cmd --permanent --add-port=10255/tcp
firewall-cmd --reload

vi hosts
[all:vars]
ansible_user=ansible
ansible_python_interpreter=/usr/bin/python3

[master]
master.brian

[worker]
worker-1.brian
worker-1.brian

[nfs]
nfs.brian


#kubernetes
timedatectl set-timezone America/Guayaquil
dnf install chrony -y
systemctl enable chronyd
systemctl start chronyd
timedatectl set-ntp true
sed -i s/=enforcing/=disabled/g /etc/selinux/config
dnf install nfs-utils nfs4-acl-tools wget -y
dnf install nfs-utils net-tools -y


#workers
useradd -md /home/ansible ansible
passwd ansible
/etc/sudoers.d/ansible
ansible ALL=(ALL) NOPASSWD:ALL
#luego de instalacion de kubernetes
firewall-cmd --zone=public --permanent --add-port={10250,30000-32767}/tcp
firewall-cmd --reload

#nfs
systemctl  enable nfs-server
systemctl start nfs-server
vi /etc/exports
/srv/nfs	192.168.1.110(rw,sync)
/srv/nfs	192.168.1.111(rw,sync)
/srv/nfs	192.168.1.112(rw,sync)
exportfs -r
exportfs -s
firewall-cmd --permanent --add-service=nfs
firewall-cmd --permanent --add-service=rpc-bind
firewall-cmd --permanent --add-service=mountd
firewall-cmd --reload
