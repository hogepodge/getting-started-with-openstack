# -*- mode: ruby -*-
# vi: set ft=ruby :
# Getting Started With OpenStack
# Original work by Dan Radez, https://radez.fedorapeople.org/Vagrantfile

$script = <<SCRIPT
if rpm -q NetworkManager; then
  service NetworkManager stop
  kill $(pgrep dhclient)
  yum remove -y NetworkManager
  chkconfig network on
  service network start
fi
if ! ls packstack-answers*; then
  yum install -y e2fsprogs
  yum install -y https://repos.fedorapeople.org/repos/openstack/openstack-pike/rdo-release-pike-1.noarch.rpm
  yum update -y
  yum install -y openstack-packstack
  CONFIG_HEAT_INSTALL=y CONFIG_MAGNUM_INSTALL=n CONFIG_CEILOMETER_INSTALL=n CONFIG_AODH_INSTALL=n CONFIG_GNOCCHI_INSTALL=y packstack \
            --provision-demo=y \
            --install-hosts=192.168.33.30 \
            --enable-rdo-testing=y \
            --os-neutron-ovs-bridge-mappings=extnet:br-ex \
            --os-neutron-ovs-bridge-interfaces=br-ex:eth2 \
            --os-neutron-ml2-type-drivers=vxlan,flat \
            --os-heat-install=y \
            --os-ceilometer-install=n \
            --os-aodh-install=n
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
IPADDR=192.168.33.30
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
  config.vm.network "private_network", ip: "192.168.33.30", netmask: "255.255.255.0", dhcp_enabled: true
  config.vm.network "private_network", ip: "172.24.4.226", netmask: "255.255.255.240", dhcp_enabled: true
      

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
