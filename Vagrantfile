$script_ha = <<-SCRIPT_HA
set -e
echo 1 > /proc/sys/net/ipv4/ip_nonlocal_bind
apt-get update && apt-get install -y haproxy keepalived
cp /usr/local/copy/haproxy.cfg /etc/haproxy/haproxy.cfg
cp /usr/local/copy/haproxy.conf /etc/rsyslog.d/haproxy.conf
restart rsyslog
haproxy -f /etc/haproxy/haproxy.cfg -D
service keepalived restart
SCRIPT_HA

$script_web = <<-SCRIPT_WEB
set -e
apt-get update && apt-get install -y nginx
echo "I'm web $1" > /usr/share/nginx/html/index.html
service nginx restart
SCRIPT_WEB

Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/trusty64"

  (1..2).each do |i|
    config.vm.define "ha#{i}" do |node|
      node.vm.hostname = "ha#{i}"
      node.vm.network :private_network, ip: "192.168.33.1#{i}"
      node.vm.synced_folder "files/ha#{i}", "/etc/keepalived"
      node.vm.synced_folder "files/copy", "/usr/local/copy"
      node.vm.provision "shell", inline: $script_ha
    end
  end

  (1..2).each do |i|
    config.vm.define "web#{i}" do |node|
      node.vm.hostname = "web#{i}"
      node.vm.network :private_network, ip: "192.168.34.1#{i}"
      node.vm.provision "shell" do |s|
        s.inline = $script_web
        s.args   = "#{i}"
      end
    end
  end

end
