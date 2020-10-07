require 'yaml'

$SETUP = <<-SHELL
sudo su
echo -e "change_me\nchange_me" | passwd root
echo "PermitRootLogin yes" >> /etc/ssh/sshd_config
sed -in 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
systemctl restart sshd
yum install net-tools wget -y
if [ $(hostname) == "uf1.local.com" ] || [ $(hostname) == "uf2.local.com" ]
then
cd /opt &&  wget -O splunkforwarder-8.0.3-linux-2.6-x86_64.rpm 'https://www.splunk.com/bin/splunk/DownloadActivityServlet?architecture=x86_64&platform=linux&version=8.0.3&product=universalforwarder&filename=splunkforwarder-8.0.3-a6754d8441bf-linux-2.6-x86_64.rpm&wget=true'
cd /opt && yum install -y splunkforwarder-8.0.3-linux-2.6-x86_64.rpm && /opt/splunkforwarder/bin/splunk start --accept-license --answer-yes --no-prompt --seed-passwd change_me
else
cd /opt &&  wget -O splunk-8.0.3-linux-2.6-x86_64.rpm 'https://www.splunk.com/bin/splunk/DownloadActivityServlet?architecture=x86_64&platform=linux&version=8.0.3&product=splunk&filename=splunk-8.0.3-a6754d8441bf-linux-2.6-x86_64.rpm&wget=true'
yum install -y splunk-8.0.3-linux-2.6-x86_64.rpm && /opt/splunk/bin/splunk start --accept-license --answer-yes --no-prompt --seed-passwd change_me
fi

SHELL

yaml = YAML.load_file("machines.yml")

Vagrant.configure("2") do |config|
  yaml.each do |server|

    config.vm.define server["name"] do |srv|
      srv.vm.box = server["system"]
      srv.vm.network "private_network", ip: server["ip"]
      srv.vm.hostname = server["hostname"]
      srv.vm.provision "shell", privileged: true,inline: $SETUP
      srv.vm.provider "virtualbox" do |vb|
      srv.vm.disk :disk, size: "30GB", primary: true
        vb.name = server["name"]
        vb.memory = server["memory"]
        vb.cpus = server["cpus"]
      end
    end
  end
end
