$script_ha = <<-SCRIPT_HA
set -e
echo 1 > /proc/sys/net/ipv4/ip_nonlocal_bind
apt-get update && apt-get install -y haproxy keepalived
cp /etc/haproxy.bak/haproxy.cfg /etc/haproxy/haproxy.cfg
haproxy -f /etc/haproxy/haproxy.cfg -D
service keepalived restart
SCRIPT_HA

$script_web1 = <<-SCRIPT_WEB
set -e
apt-get update && apt-get install -y nginx
echo "I'm web $1" > /usr/share/nginx/html/index.html
service nginx restart
SCRIPT_WEB

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.define :ha1 do |node|
    node.vm.hostname = "ha1"
    node.vm.network :private_network, ip: "192.168.33.10"
    node.vm.synced_folder "ha1", "/etc/keepalived"
    node.vm.synced_folder "haproxy", "/etc/haproxy.bak"
    node.vm.provision "shell", inline: $script_ha
  end

  config.vm.define :ha2 do |node|
    node.vm.hostname = "ha2"
    node.vm.network :private_network, ip: "192.168.33.11"
    node.vm.synced_folder "ha2", "/etc/keepalived"
    node.vm.synced_folder "haproxy", "/etc/haproxy.bak"
    node.vm.provision "shell", inline: $script_ha
  end

  config.vm.define :web1 do |node|
    node.vm.hostname = "web1"
    node.vm.network :private_network, ip: "192.168.34.10"
    node.vm.provision "shell" do |s|
      s.inline = $script_web1
      s.args   = "1"
    end
  end

  config.vm.define :web2 do |node|
    node.vm.hostname = "web2"
    node.vm.network :private_network, ip: "192.168.34.11"
    node.vm.provision "shell" do |s|
      s.inline = $script_web1
      s.args   = "2"
    end
  end

end
