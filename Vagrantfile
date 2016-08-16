# -*- mode: ruby -*-
# vi: set ft=ruby :

$init = <<SCRIPT
  apt-get update -y
SCRIPT

$quagga = <<SCRIPT
  apt-get install -y quagga traceroute
  
  # Enable IPv4 and IPv6 forwarding
  echo "net.ipv4.conf.all.forwarding=1" | sudo tee -a /etc/sysctl.conf 
  echo "net.ipv4.conf.default.forwarding=1" | sudo tee -a /etc/sysctl.conf 
  sed 's/#net.ipv6.conf.all.forwarding=1/net.ipv6.conf.all.forwarding=1/g' /etc/sysctl.conf | sudo tee /etc/sysctl.conf
  echo "net.ipv6.conf.default.forwarding=1" | sudo tee -a /etc/sysctl.conf 
  sudo sysctl -p 
  
  sed -i -e 's/zebra=no/zebra=yes/g' /etc/quagga/daemons
  sed -i -e 's/bgpd=no/bgpd=yes/g' /etc/quagga/daemons

  cat > /etc/quagga/bgpd.conf << EOF
hostname peer-1
password admin
enable password admin
log timestamp precision 6
log file /var/log/quagga/bgpd.log
!
line vty
 exec-timeout 0 0
!
EOF

  service quagga restart
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/trusty64"

  config.vm.provider "virtualbox" do |v|
    v.customize ["modifyvm", :id, "--cpuexecutioncap", "50"]
    v.customize ["modifyvm", :id, "--memory", "2048"]
  end

  # Guest configuration
  config.vm.hostname = "BGP-PCEP"
  config.vm.network "public_network", bridge: "en0: Ethernet"
  
  # Provisioning
  config.vm.provision "shell", inline: $init
  config.vm.provision "shell", inline: $quagga
  #config.vm.provision "shell", inline: $cleanup
end
