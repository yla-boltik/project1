# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.

Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  
  config.vm.box = "bertvv/centos72"

  config.ssh.username = "vagrant"
  config.ssh.password = "vagrant"

  config.vm.provider "virtualbox" do |vb|
     vb.gui = true
     vb.memory = "1024"
     vb.cpus = 2
     vb.customize ['modifyvm', :id, '--cableconnected1', 'on']
   end


   config.vm.define "apache" do |apache|
       apache.vm.hostname = "apache"
       apache.vm.network "forwarded_port", guest: 80, host: 20080
       apache.vm.network "private_network", ip: "192.168.33.15"

       apache.vm.provision "shell", inline: <<-SHELL       
       sudo yum -y install httpd
       sudo systemctl start httpd
       sudo systemctl enable httpd
       sudo cp /vagrant/mod_jk.so /etc/httpd/modules/
       
       sudo cd /etc/httpd/conf/
       sudo touch workers.properties
       sudo echo "worker.list=lb" >> workers.properties 
       sudo echo "worker.lb.type=lb" >> workers.properties
       sudo echo "worker.lb.balance_workers=myworker, other" >> workers.properties
       sudo echo "worker.myworker.host=localhost" >> workers.properties
       sudo echo "worker.myworker.port=8009" >> workers.properties
       sudo echo "worker.myworker.type=ajp13" >> workers.properties

       sudo echo "LoadModule jk_module modules/mod_jk.so" >> httpd.conf 
       sudo echo "JkWorkersFile conf/workers.properties" >> httpd.conf
       sudo echo "JkShmFile /tmp/shm" >> httpd.conf
       sudo echo "JkLogFile logs/mod_jk.log" >> httpd.conf
       sudo echo "JkLogLevel info" >> httpd.conf
       sudo echo "JkMount /test*lb" >> httpd.conf

       sudo systemctl restart httpd
       SHELL
   end

   
   config.vm.define "tomcat1" do |tomcat1|
       tomcat1.vm.hostname = "tomcat1"
       tomcat1.vm.network "forwarded_port", guest: 80, host: 21080
       tomcat1.vm.network "private_network", ip: "192.168.33.16"

       tomcat1.vm.provision "shell", inline: <<-SHELL       
       sudo yum -y install java-1.8.0-openjdk
       sudo yum -y install tomcat tomcat-webapps tomcat-admin-webapps
       sudo systemctl start tomcat
       sudo systemctl enable tomcat         

       sudo mkdir /usr/share/tomcat/webapps/new
       sudo cd /usr/share/tomcat/webapps/new
       sudo echo "tomcat1" > /usr/share/tomcat/webapps/new/index.html

       SHELL
   end

   config.vm.define "tomcat2" do |tomcat2|
       tomcat2.vm.hostname = "tomcat2"
       tomcat2.vm.network "forwarded_port", guest: 80, host: 22080
       tomcat2.vm.network "private_network", ip: "192.168.33.17"

       tomcat2.vm.provision "shell", inline: <<-SHELL
       sudo yum -y install java-1.8.0-openjdk
       sudo yum -y install tomcat tomcat-webapps tomcat-admin-webapps
       sudo systemctl start tomcat
       sudo systemctl enable tomcat
       sudo mkdir /usr/share/tomcat/webapps/new
       sudo cd /usr/share/tomcat/webapps/new
       sudo echo "tomcat2" > /usr/share/tomcat/webapps/new/index.html  
       SHELL
   end




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
  # config.vm.network "private_network", ip: "192.168.33.10"

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
  config.vm.provision "shell", inline: <<-SHELL
       sudo firewall-cmd --zone=public --add-port=80/tcp --permanent
       sudo firewall-cmd --reload
       sudo systemctl stop firewalld
       sudo systemctl disable firewalld
       sudo systemctl restart network
  SHELL
end
