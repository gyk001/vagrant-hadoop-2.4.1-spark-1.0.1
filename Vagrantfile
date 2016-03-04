Vagrant.require_version ">= 1.4.3"
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
	numNodes = 3
	r = numNodes..1
	(r.first).downto(r.last).each do |i|
		config.vm.define "node#{i}" do |node|
			node.vm.box = "centos65"
			node.vm.box_url = "https://github.com/2creatives/vagrant-centos/releases/download/v6.5.1/centos65-x86_64-20131205.box"
			node.vm.provider "virtualbox" do |v|
			  v.name = "node#{i}"
			  if i == 2
			  	v.customize ["modifyvm", :id, "--memory", "1024"]
			  else
			  	v.customize ["modifyvm", :id, "--memory", "2048"]		
			  end			  
			end
			if i < 10
				node.vm.network :private_network, ip: "10.211.55.10#{i}"
			else
				node.vm.network :private_network, ip: "10.211.55.1#{i}"
			end
			# 设置虚拟机主机名
			node.vm.hostname = "node#{i}"
			# 关闭防火墙
			node.vm.provision "shell", path: "scripts/setup-centos.sh"
			# 添加其他主机的hosts记录
			node.vm.provision "shell" do |s|
				s.path = "scripts/setup-centos-hosts.sh"
				s.args = "-t #{numNodes}"
			end

			# 第2台主机可登录第3台及以后的机器
			if i == 2
				node.vm.provision "shell" do |s|
					s.path = "scripts/setup-centos-ssh.sh"
					#
					s.args = "-s 3 -t #{numNodes}"
				end
			end

			# 第1台主机可登录第2台及以后的机器
			if i == 1
				node.vm.provision "shell" do |s|
					s.path = "scripts/setup-centos-ssh.sh"
					s.args = "-s 2 -t #{numNodes}"
				end
			end
			# 安装java hadoop
			node.vm.provision "shell", path: "scripts/setup-java.sh"
			node.vm.provision "shell", path: "scripts/setup-hadoop.sh"
			# 安装hadoop slaves			
			node.vm.provision "shell" do |s|
				s.path = "scripts/setup-hadoop-slaves.sh"
				s.args = "-s 3 -t #{numNodes}"
			end
			
			node.vm.provision "shell", path: "scripts/setup-spark.sh"
			
			node.vm.provision "shell" do |s|
				s.path = "scripts/setup-spark-slaves.sh"
				s.args = "-s 3 -t #{numNodes}"
			end

			if i == 1
				node.vm.provision "shell", path: "scripts/init-start-all-services.sh"
			end
		end
	end
end