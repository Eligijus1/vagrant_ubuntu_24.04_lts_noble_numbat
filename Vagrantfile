# Require vagrant 1.8 or newer:
Vagrant.require_version ">= 1.8"

# BOX Configuration:
Vagrant.configure("2") do |config|
    config.ssh.forward_agent = true

    # Ubuntu 24.04 LTS (Noble Numbat) (https://portal.cloud.hashicorp.com/vagrant/discover/bento/ubuntu-24.04)
    config.vm.box = "bento/ubuntu-24.04"

    # The hostname the machine should have. Defaults to nil. If nil, 
    # Vagrant will not manage the hostname. If set to a string, the 
    # hostname will be set on boot.
    config.vm.hostname = "vagrant.test"

    # Configures networks on the machine:
    config.vm.network "private_network", ip: "172.28.128.30"
    
    if Vagrant.has_plugin?("vagrant-hostmanager")
        config.hostmanager.enabled = true
        config.hostmanager.manage_host = true
        config.hostmanager.manage_guest = true
        config.hostmanager.ignore_private_ip = false
        config.hostmanager.ip_resolver = proc do |vm, resolving_vm|
            if hostname = (vm.ssh_info && vm.ssh_info[:host])
                `vagrant ssh -c "hostname -I"`.split()[1]
            end
        end
    end

    config.vm.provider :virtualbox do |v|
	    v.customize ["modifyvm", :id, "--memory", 4096]
		v.customize ["modifyvm", :id, "--cableconnected1", "on"]
        v.customize ["modifyvm", :id, "--uart1", "0x3F8", "4"]
        v.customize ["modifyvm", :id, "--uartmode1", "file", File::NULL]
    end

    # Define shell:
    config.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"
	
	# Copy git configuration from host:
	config.vm.provision "file", source: "~/.gitconfig", destination: ".gitconfig"

	# Copy ssh keys from host:
	config.vm.provision "file", source: "~/.ssh/id_rsa", destination: ".ssh/id_rsa"
	config.vm.provision "file", source: "~/.ssh/id_rsa.pub", destination: ".ssh/id_rsa.pub"
	
    # Enable additional provisioning with a shell script:
    config.vm.provision "shell", inline: <<-SHELL
	    echo "# IPv4 and IPv6 localhost aliases:" | sudo tee /etc/hosts
	    echo "127.0.0.1 vagrant.test vagrant.test localhost" | sudo tee -a /etc/hosts
	    echo "::1       vagrant.test vagrant.test localhost" | sudo tee -a /etc/hosts
        echo "nameserver 8.8.8.8" | sudo tee -a /etc/resolv.conf
        echo "nameserver 8.8.4.4" | sudo tee -a /etc/resolv.conf
		sudo apt-get update
		sudo apt-get install -y nmap
		sudo apt-get install -y git
		sudo apt-get install -y zip unzip
		sudo apt-get install -y net-tools
		sudo apt-get install ca-certificates curl gnupg -y
		sudo apt-get install jq -y
		sudo apt-get install awscli -y
        sudo apt-get install php-cli -y
        sudo apt-get install php-sqlite3 -y
        sudo apt-get install php-dom -y
        sudo apt-get install php-cli unzip -y
        sudo apt-get install php-curl -y
        sudo apt-get install php-zip -y
        sudo apt-get install php-pdo -y
        sudo apt-get install php-pdo-mysql -y
        sudo apt-get install php-imap -y
		cd /tmp/
        curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer
		sudo apt remove needrestart -y
		sudo install -m 0755 -d /etc/apt/keyrings
		curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
		sudo chmod a+r /etc/apt/keyrings/docker.gpg
		echo "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
		sudo apt-get update
		sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
		sudo apt-get install docker-compose -y
		sudo groupadd docker
		sudo usermod -aG docker ubuntu
		sudo usermod -aG docker vagrant
	    echo "+-----------------------+"
	    echo "|  Installed versions:  |"
	    echo "+-----------------------+"
		lsb_release -a
		nmap -V
		git --version
		aws --version
		php -v
		composer -V
		docker -v
		docker-compose -v
		docker compose version
	    echo "+---------------------------------------+"
	    echo "|                                       |"
	    echo "|     Ubuntu 24.04 LTS (Noble Numbat)   |"
	    echo "|            setup completed            |"
	    echo "|             2024-11-17 01             |"
		echo "|                                       |"
	    echo "|  TODO manual:                         |"
		echo "|  ============                         |"
		echo "|  $ vagrant ssh                        |"
		echo "|  $ cd ~/.ssh/                         |"
		echo "|  $ cat id_rsa.pub >> authorized_keys  |"
	    echo "+---------------------------------------+"
    SHELL
end
