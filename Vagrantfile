
##########################################################
# Igor Azarny iazarny@yahoo.com
# k8s on Debian buster 
##########################################################

Vagrant.configure("2") do |config|

  config.vm.box = "debian/buster64"
  config.disksize.size = '100GB'
  config.vm.hostname = "k8sdeb"
  config.vm.boot_timeout = 120
  config.ssh.insert_key = false
  config.vm.network :private_network, type: "dhcp"

  config.vm.network "forwarded_port", guest: 8080, host: 8080
  config.vm.network "forwarded_port", guest: 8081, host: 8081
  config.vm.network "forwarded_port", guest: 8082, host: 8082
  config.vm.network "forwarded_port", guest: 8083, host: 8083
  config.vm.network "forwarded_port", guest: 8084, host: 8084
  config.vm.network "forwarded_port", guest: 8085, host: 8085
  config.vm.network "forwarded_port", guest: 8086, host: 8086
  config.vm.network "forwarded_port", guest: 8087, host: 8087

  config.vm.network "forwarded_port", guest: 8443, host: 8443
  config.vm.network "forwarded_port", guest: 80, host: 80
  config.vm.network "forwarded_port", guest: 443, host: 443
  config.vm.network "forwarded_port", guest: 22, host: 22
  config.vm.network "forwarded_port", guest: 5432, host: 5432
  
  config.vm.provider "virtualbox" do |vb|
        vb.name = "k8sdeb"
        vb.gui = true
        vb.memory = "8129"
        vb.cpus = 2
  end


  config.vm.provision "shell", inline: <<-SHELL
        swapoff -a
        apt-get update -y
        apt-get install -y git  wget curl  net-tools  mc unzip apt-transport-https ca-certificates gnupg-agent software-properties-common


        apt install -y conntrack
        apt-get install -y iptables
        sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config    
        systemctl restart sshd.service

        curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
        apt-key fingerprint 0EBFCD88  
        add-apt-repository \
           "deb [arch=amd64] https://download.docker.com/linux/debian \
            $(lsb_release -cs) \
            stable" 
        apt-get update
        apt-get install -y docker-ce docker-ce-cli containerd.io
        docker run hello-world

        groupadd docker
        usermod -aG docker vagrant
        newgrp docker

        curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
        chmod +x /usr/local/bin/docker-compose
        docker-compose --version

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
        sudo sysctl --system

curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
        sudo apt-get update
        sudo apt-get install -y kubelet kubeadm kubectl
        sudo apt-mark hold kubelet kubeadm kubectl

        echo "##################################################################"
        echo "# Please run kubeadm init under root and follow instructions     #"
        echo "##################################################################"        

  SHELL

end