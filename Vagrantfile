home = ENV['HOME']
ENV["LC_ALL"] = "en_US.UTF-8"

Vagrant.configure("2") do |config|

    config.vm.provider :virtualbox do |v|
      v.memory = 512
    end

    config.vm.define "freeipa-server" do |fs|
        fs.vm.box = "centos/7"
        fs.vm.hostname = "freeipa-server"
        fs.vm.network :private_network, ip: "192.168.11.101"

        fs.vm.provision "shell", inline: <<-SHELL
            mkdir -p ~root/.ssh
            cp ~vagrant/.ssh/auth* ~root/.ssh
            hostnamectl set-hostname "ipa.freeipa-hw.lan"
            yum -y update
            echo -e "192.168.11.101\ipa.freeipa-hw.lan\t ip" >> /etc/hosts
            yum install ipa-server ipa-server-dns -y
            systemctl start firewalld
            SHELL
        
    end

    config.vm.define "freeipa-client" do |fc|
        fc.vm.box = "centos/7"
        fc.vm.hostname = "freeipa-client"
        fc.vm.network :private_network, ip: "192.168.11.102"

        fc.vm.provision "shell", inline: <<-SHELL
            mkdir -p ~root/.ssh
            cp ~vagrant/.ssh/auth* ~root/.ssh
            hostnamectl set-hostname "client.freeipa-hw.lan"
            echo "192.168.11.101 ipa.freeipa-hw.lan ipa" >> /etc/hosts
            echo "192.168.11.102 client.freeipa-hw.lan" >> /etc/hosts
            yum install freeipa-client -y
            SHELL
    end
end