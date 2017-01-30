# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  
  config.vm.define "kube-master" do |master|
    master.vm.box = "centos/7"
    master.vm.hostname = "kube-master"
    master.vm.network "private_network", ip: "192.168.56.10"
    master.vm.synced_folder ".", "/vagrant", disabled: true
    master.ssh.forward_agent = true

    master.vm.provision "shell", inline: <<-SHELL
      ifup eth1
      yum update -y
      yum install -y ntp
      systemctl enable ntdp && sudo systemctl start ntpd
      echo -e "192.168.56.10 kube-master\n192.168.56.11 kube-minion1\n192.168.56.12 kube-minion2\n192.168.56.13 kube-minion3"  >>  /etc/hosts
      echo -e "[virt7-docker-common-release]\nname=virt7-docker-common-release\nbaseurl=http://cbs.centos.org/repos/virt7-docker-common-release/x86_64/os/\ngpgcheck=0" > /etc/yum.repos.d/virt7-docker-common-release.repo
      yum update -y
      yum install -y --enablerepo=virt7-docker-common-release kubernetes docker 
      cat /etc/kubernetes/config | sed 's/127.0.0.1/kube-master/g' >> /etc/kubernetes/config
      echo 'KUBE_ETCD_SERVERS="--etcd-servers=http://kube-master:2379"' >> /etc/kubernetes/config

      #master only; maybe
      yum install -y etcd
      sed -i 's/localhost/0.0.0.0/g' /etc/etcd/etcd.conf 
      sed -i 's/KUBE_API_ADDRESS=".*"/KUBE_API_ADDRESS="--address=0.0.0.0"/g' /etc/kubernetes/apiserver 
      sed -i 's/# KUBE_API_PORT/KUBE_API_PORT/g' /etc/kubernetes/apiserver
      sed -i 's/# KUBELET_PORT/KUBELET_PORT/g' /etc/kubernetes/apiserver
      sed -i 's/KUBE_ADMISSION_CONTROL/# KUBE_ADMISSION_CONTROL/g' /etc/kubernetes/apiserver 
      systemctl enable etcd kube-apiserver kube-controller-manager kube-scheduler
      systemctl start etcd kube-apiserver kube-controller-manager kube-scheduler

    SHELL
  end

  config.vm.define "kube-minion1" do |minion1|
    minion1.vm.box = "centos/7"
    minion1.vm.hostname = "kube-minion1"
    minion1.vm.network "private_network", ip: "192.168.56.11"
    minion1.vm.synced_folder ".", "/vagrant", disabled: true
    minion1.ssh.forward_agent = true

    minion1.vm.provision "shell", inline: <<-SHELL
      ifup eth1
      yum update -y
      yum install -y ntp
      systemctl enable ntdp && sudo systemctl start ntpd
      echo -e "192.168.56.10 kube-master\n192.168.56.11 kube-minion1\n192.168.56.12 kube-minion2\n192.168.56.13 kube-minion3"  >>  /etc/hosts
      echo -e "[virt7-docker-common-release]\nname=virt7-docker-common-release\nbaseurl=http://cbs.centos.org/repos/virt7-docker-common-release/x86_64/os/\ngpgcheck=0" > /etc/yum.repos.d/virt7-docker-common-release.repo
      yum update -y
      yum install -y --enablerepo=virt7-docker-common-release kubernetes docker 
      cat /etc/kubernetes/config | sed 's/127.0.0.1/kube-master/g' >> /etc/kubernetes/config
      echo 'KUBE_ETCD_SERVERS="--etcd-servers=http://kube-master:2379"' >> /etc/kubernetes/config

      #minion only
      sed -i 's/127.0.0.1/kube-master/g' /etc/kubernetes/config
      echo 'KUBE_ETCD_SERVERS="--etcd-servers=http://centos-master:2379"' >> /etc/kubernetes/config
      sed -i 's/127.0.0.1/0.0.0.0/g' /etc/kubernetes/kubelet 
      sed -i 's/# KUBELET_PORT/KUBELET_PORT/g' /etc/kubernetes/kubelet 
      sed -i 's/hostname-override=0.0.0.0/hostname-override=kube-minion1/g' /etc/kubernetes/kubelet 
      sed -i 's/0.0.0.0:8080/kube-master:8080/g' /etc/kubernetes/kubelet
      sed -i 's/KUBELET_POD_INFRA/# KUBELET_POD_INFRA/g' /etc/kubernetes/kubelet
      systemctl enable kube-proxy kubelet docker
      systemctl start kube-proxy kubelet docker

    SHELL
  end

  config.vm.define "kube-minion2" do |minion2|
    minion2.vm.box = "centos/7"
    minion2.vm.hostname = "kube-minion2"
    minion2.vm.network "private_network", ip: "192.168.56.12"
    minion2.vm.synced_folder ".", "/vagrant", disabled: true
    minion2.ssh.forward_agent = true

    minion2.vm.provision "shell", inline: <<-SHELL
      ifup eth1
      yum update
      yum install -y ntp
      systemctl enable ntdp && sudo systemctl start ntpd
      echo -e "192.168.56.10 kube-master\n192.168.56.11 kube-minion1\n192.168.56.12 kube-minion2\n192.168.56.13 kube-minion3"  >>  /etc/hosts
      echo -e "[virt7-docker-common-release]\nname=virt7-docker-common-release\nbaseurl=http://cbs.centos.org/repos/virt7-docker-common-release/x86_64/os/\ngpgcheck=0" > /etc/yum.repos.d/virt7-docker-common-release.repo
      yum update -y
      yum install -y --enablerepo=virt7-docker-common-release kubernetes docker 
      cat /etc/kubernetes/config | sed 's/127.0.0.1/kube-master/g' >> /etc/kubernetes/config
      echo 'KUBE_ETCD_SERVERS="--etcd-servers=http://kube-master:2379"' >> /etc/kubernetes/config

      #minion only
      sed -i 's/127.0.0.1/kube-master/g' /etc/kubernetes/config
      echo 'KUBE_ETCD_SERVERS="--etcd-servers=http://centos-master:2379"' >> /etc/kubernetes/config
      sed -i 's/127.0.0.1/0.0.0.0/g' /etc/kubernetes/kubelet 
      sed -i 's/# KUBELET_PORT/KUBELET_PORT/g' /etc/kubernetes/kubelet 
      sed -i 's/hostname-override=0.0.0.0/hostname-override=kube-minion2/g' /etc/kubernetes/kubelet 
      sed -i 's/0.0.0.0:8080/kube-master:8080/g' /etc/kubernetes/kubelet
      sed -i 's/KUBELET_POD_INFRA/# KUBELET_POD_INFRA/g' /etc/kubernetes/kubelet
      systemctl enable kube-proxy kubelet docker
      systemctl start kube-proxy kubelet docker

    SHELL
  end

  config.vm.define "kube-minion3" do |minion3|
    minion3.vm.box = "centos/7"
    minion3.vm.hostname = "kube-minion3"
    minion3.vm.network "private_network", ip: "192.168.56.13"
    minion3.vm.synced_folder ".", "/vagrant", disabled: true
    minion3.ssh.forward_agent = true
    
    minion3.vm.provision "shell", inline: <<-SHELL
      ifup eth1
      yum update
      yum install -y ntp
      systemctl enable ntdp && sudo systemctl start ntpd
      echo -e "192.168.56.10 kube-master\n192.168.56.11 kube-minion1\n192.168.56.12 kube-minion2\n192.168.56.13 kube-minion3"  >>  /etc/hosts
      echo -e "[virt7-docker-common-release]\nname=virt7-docker-common-release\nbaseurl=http://cbs.centos.org/repos/virt7-docker-common-release/x86_64/os/\ngpgcheck=0" > /etc/yum.repos.d/virt7-docker-common-release.repo
      yum update -y
      yum install -y --enablerepo=virt7-docker-common-release kubernetes docker 
      cat /etc/kubernetes/config | sed 's/127.0.0.1/kube-master/g' >> /etc/kubernetes/config
      echo 'KUBE_ETCD_SERVERS="--etcd-servers=http://kube-master:2379"' >> /etc/kubernetes/config

      #minion only
      sed -i 's/127.0.0.1/kube-master/g' /etc/kubernetes/config
      echo 'KUBE_ETCD_SERVERS="--etcd-servers=http://centos-master:2379"' >> /etc/kubernetes/config
      sed -i 's/127.0.0.1/0.0.0.0/g' /etc/kubernetes/kubelet 
      sed -i 's/# KUBELET_PORT/KUBELET_PORT/g' /etc/kubernetes/kubelet 
      sed -i 's/hostname-override=0.0.0.0/hostname-override=kube-minion3/g' /etc/kubernetes/kubelet 
      sed -i 's/0.0.0.0:8080/kube-master:8080/g' /etc/kubernetes/kubelet
      sed -i 's/KUBELET_POD_INFRA/# KUBELET_POD_INFRA/g' /etc/kubernetes/kubelet
      systemctl enable kube-proxy kubelet docker
      systemctl start kube-proxy kubelet docker
    SHELL
  end

end



  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
