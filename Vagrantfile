$script_ha = <<-SCRIPT
apt-get update
echo 1 > /proc/sys/net/ipv4/ip_nonlocal_bind
apt-get install -y haproxy
cp /etc/haproxy.bak/haproxy.cfg /etc/haproxy/haproxy.cfg
sudo haproxy -f /etc/haproxy/haproxy.cfg -D
apt-get install -y keepalived
sudo service keepalived restart
SCRIPT

$script_web1 = <<-SCRIPT
apt-get update
apt-get install -y nginx
mkdir -p /usr/local/nginx/html
echo "I'm web 1" > /usr/share/nginx/html/index.html
SCRIPT

$script_web2 = <<-SCRIPT
apt-get update
apt-get install -y nginx
mkdir -p /usr/local/nginx/html
echo "I'm web 2" > /usr/share/nginx/html/index.html
SCRIPT

Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/trusty64"

  config.vm.define :ha1 do |node|
    node.vm.hostname = "ha1"
    node.vm.network :private_network, ip: "192.168.33.10"
    config.vm.synced_folder "ha1", "/etc/keepalived"
    config.vm.synced_folder "haproxy", "/etc/haproxy.bak"
    config.vm.provision "shell", inline: $script_ha
  end

  config.vm.define :ha2 do |node|
    node.vm.hostname = "ha2"
    node.vm.network :private_network, ip: "192.168.33.11"
    config.vm.synced_folder "ha2", "/etc/keepalived"
    config.vm.synced_folder "haproxy", "/etc/haproxy.bak"
    config.vm.provision "shell", inline: $script_ha
  end

  config.vm.define :web1 do |node|
    node.vm.hostname = "web1"
    node.vm.network :private_network, ip: "192.168.34.10"
    config.vm.provision "shell", inline: $script_web1
  end

  config.vm.define :web2 do |node|
    node.vm.hostname = "web2"
    node.vm.network :private_network, ip: "192.168.34.11"
    config.vm.provision "shell", inline: $script_web2
  end

end
