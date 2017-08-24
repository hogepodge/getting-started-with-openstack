# -*- mode: ruby -*-
# vi: set ft=ruby :
# Getting Started With OpenStack
# Original work by Dan Radez, https://radez.fedorapeople.org/Vagrantfile

$script = <<SCRIPT
if rpm -q NetworkManager; then
  service NetworkManager stop
  yum remove -y NetworkManager
  chkconfig network on
  service network start
fi
if ! ls packstack-answers*; then
  yum-config-manager --enable cr
  yum update -y
  cd /etc/yum.repos.d/
  curl -O https://trunk.rdoproject.org/rdo-reqs-pre-7.3/rdo-reqs-pre-7.3.repo
  yum -y install yum-plugin-priorities
  curl -O https://trunk.rdoproject.org/centos7/delorean-deps.repo
  curl -O https://trunk.rdoproject.org/centos7/current-passed-ci/delorean.repo
  yum update -y
  yum install -y openstack-packstack
  packstack --provision-demo=n --install-hosts=192.168.37.2 --enable-rdo-testing=y
fi

source ~/keystonerc_admin
cd /tmp
curl -O http://download.cirros-cloud.net/0.3.4/cirros-0.3.4-x86_64-disk.img
openstack image create --public --file cirros-0.3.4-x86_64-disk.img cirros

if ! ovs-vsctl show | grep eth1; then
  ovs-vsctl add-port br-int eth1
fi
if ! ovs-vsctl show | grep eth2; then
  ovs-vsctl add-port br-ex eth2
fi

cat > /etc/sysconfig/network-scripts/ifcfg-br-int << EOF
DEVICE=br-int
BOOTPROTO=static
IPADDR=192.168.37.2
PREFIX=24
NM_CONTROLLED=no
ONBOOT=yes
EOF
cat > /etc/sysconfig/network-scripts/ifcfg-eth1 << EOF
NM_CONTROLLED=no
BOOTPROTO=none
ONBOOT=yes
DEVICE=eth1
PEERDNS=no
EOF
cat > /etc/sysconfig/network-scripts/ifcfg-br-ex << EOF
DEVICE=br-ex
BOOTPROTO=static
IPADDR=172.24.4.226
PREFIX=28
NM_CONTROLLED=no
ONBOOT=yes
EOF
cat > /etc/sysconfig/network-scripts/ifcfg-eth2 << EOF
NM_CONTROLLED=no
BOOTPROTO=none
ONBOOT=yes
DEVICE=eth2
PEERDNS=no
EOF

service network restart
SCRIPT

Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "centos/7"
  config.vm.provision "shell", inline: $script
  config.vm.network "private_network", ip: "192.168.37.2", netmask: "255.255.255.0", dhcp_enabled: false, forward_mode: "none"
  config.vm.network "private_network", ip: "172.24.4.226", netmask: "255.255.255.240", dhcp_enabled: false
      

  config.vm.provider :libvirt do |lv|
        lv.memory = 8192
        lv.cpus = 4
  end
  config.vm.provider :vmware_fusion do |vm|
        vm.memory = 8192
        vm.cpus = 4
  end
  config.vm.provider :virtualbox do |vb|
        vb.memory = 8192
        vb.cpus = 4
  end
end
