Vagrant.configure("2") do |config|
  config.hostmanager.enabled = true 
  config.hostmanager.manage_host = true

  config.vm.define "acm" do |acm|
	acm.vm.box = "geerlingguy/ubuntu1804"
    acm.vm.hostname = "acm"
	acm.vm.network "private_network", ip: "192.168.3.80"
	acm.vm.provider "virtualbox" do |vb|
		vb.memory = "1024"
		vb.name = "acm"
		vb.cpus = "1"
	end
  end

  
### Web server VM ###
  config.vm.define "web01" do |web01|
    web01.vm.box = "geerlingguy/ubuntu1804"
    web01.vm.hostname = "web01"
	web01.vm.network "private_network", ip: "192.168.3.81"
	web01.vm.provider "virtualbox" do |vb|
     vb.memory = "1024"
	 vb.cpus = "1"
     vb.name = "class8_web01"
	end
  end
  config.vm.define "web02" do |web02|
    web02.vm.box = "geerlingguy/ubuntu1804"
    web02.vm.hostname = "web02"
	web02.vm.network "private_network", ip: "192.168.3.82"
	web02.vm.provider "virtualbox" do |vb|
     vb.memory = "1024"
	 vb.cpus = "1"
     vb.name = "class8_web02"
	end
  end
  
### DB vm  ####
  config.vm.define "db01" do |db01|
    db01.vm.box = "geerlingguy/centos7"
	db01.vm.hostname = "db01"
    db01.vm.network "private_network", ip: "192.168.3.83"
	db01.vm.provider "virtualbox" do |vb|
     vb.memory = "1024"
	 vb.cpus = "1"
     vb.name = "class8_db01"
	end
  end
end
