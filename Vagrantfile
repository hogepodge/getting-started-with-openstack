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
  curl -O https://trunk.rdoproject.org/centos7/current/delorean.repo
  yum update -y
  yum install -y python-setuptools openstack-packstack
  CONFIG_HEAT_INSTALL=y CONFIG_MAGNUM_INSTALL=y CONFIG_CEILOMETER_INSTALL=n CONFIG_AODH_INSTALL=n CONFIG_GNOCCHI_INSTALL=y packstack \
            --provision-demo=n \
            --install-hosts=192.168.11.20 \
            --enable-rdo-testing=y \
            --os-neutron-ovs-bridge-mappings=extnet:br-ex \
            --os-neutron-ovs-bridge-interfaces=br-ex:eth2 \
            --os-neutron-ml2-type-drivers=vxlan,flat \
            --os-heat-install=y \
            --os-ceilometer-install=n \
            --os-aodh-install=n \
            --os-gnocchi-install=n
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
IPADDR=192.168.11.20
PREFIX=24 NM_CONTROLLED=no
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
IPADDR=192.168.22.226
PREFIX=24
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

cat > /etc/neutron/plugins/ml2/linuxbridge_agent.ini << EOF
[linux_bridge]
physical_interface_mappings = provider:eth2
EOF
chgrp neutron /etc/neutron/plugins/ml2/linuxbridge_agent.ini 

openstack network create --share --external --provider-physical-network extnet \
                         --provider-network-type flat extnet
openstack subnet create --network extnet \
                        --allocation-pool start=192.168.22.100,end=192.168.22.200 \
                        --dns-nameserver=8.8.8.8 --gateway 192.168.22.1 \
                        --subnet-range 192.168.22.0/24 extnet

openstack network create --share private
openstack subnet create --subnet-range 10.0.0.0/24 --network private "10.0.0.0/24"
openstack router create router
openstack router add subnet router "10.0.0.0/24"
neutron router-gateway-set router extnet
service neutron-dhcp-agent restart

SCRIPT

Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "centos/7"
  config.vm.provision "shell", inline: $script
  config.vm.network "private_network", ip: "192.168.11.20", netmask: "255.255.255.0"
  config.vm.network "private_network", ip: "192.168.22.226", netmask: "255.255.255.0"
      

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
