# -*- mode: ruby -*-
# vi: set ft=ruby :

################################################################################################################
#                                                                                                              #
# Vagrantfile for provisioning ready-to-go HP Cloud Service Automation Community Edition 4.6.0 VM.#
#                                                                                                              #
# Author: Gilles Tosi                                                                                          #
#                                                                                                              #
# The up-to-date version and associated dependencies/project documentation is available at:                    #
#                                                                                                              #
# https://github.com/gilleslabs/learn-csa/                                                                     #
#                                                                                                              #
################################################################################################################



######################################################################################################
#                                                                                                    #
#      Setup of $csa variable which will be used for csa VM Shell inline provisioning     #
#                                                                                                    #
######################################################################################################



$csa = <<CSA
echo "Build start at    :" > /tmp/build
date >> /tmp/build 

	################     Installing Docker            ###################

sudo apt-get update
sudo apt-get install apt-transport-https ca-certificates
sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
sudo touch /etc/apt/sources.list.d/docker.list
sudo rm /etc/apt/sources.list.d/docker.list
sudo echo deb https://apt.dockerproject.org/repo ubuntu-trusty main > /etc/apt/sources.list.d/docker.list
sudo apt-get update -y
sudo apt-get purge lxc-docker
sudo apt-cache policy docker-engine
sudo apt-get upgrade
sudo apt-get install linux-image-extra-$(uname -r) -y
sudo apt-get install docker-engine -y
sudo service docker start
sudo groupadd docker
sudo usermod -aG docker vagrant

	################     Installing Docker-Compose            ###################

sudo apt-get -y install python-pip
sudo pip install docker-compose

	################     Updating host and ufw                ###################
	
sudo hostname 'csaserver.example.com'
echo "127.0.1.1 192.168.99.10 csaserver" | sudo tee -a /etc/hosts
sudo ufw enable
sudo sed -i 's|DEFAULT_FORWARD_POLICY="DROP"|DEFAULT_FORWARD_POLICY="ACCEPT"|g' /etc/default/ufw
sudo ufw reload
sudo ufw allow 22/tcp
sudo ufw allow 2375/tcp
sudo ufw allow 2376/tcp

	##################       docker-compose CSA               ###################

mkdir /tmp/csa
cd /tmp
curl -k -L https://github.com/HewlettPackard/csa-ce/raw/master/buildEnv-dockercompose.sh | bash /dev/stdin csaserver.example.com 192.168.99.10
echo "Build completed at      :" >> /tmp/build
date >> /tmp/build
cat /tmp/build
echo
echo Starting CSA...
sleep 5m
echo
echo "Please note the below URLs for your reference"
echo "CSA Management Console - https://192.168.99.10:18444/csa"
echo "MPP - https://192.168.99.10:18089/mpp"
echo "Operations Orchestration Central - https://192.168.99.10:18445/oo"
CSA


Vagrant.configure(2) do |config|

	config.vm.define "csa" do |csa|
        csa.vm.box = "ubuntu/trusty64"
			config.vm.provider "virtualbox" do |v|
				v.cpus = 4
				v.memory = 4096
			end
        csa.vm.network "private_network", ip: "192.168.99.10"
		csa.vm.provision :shell, inline: $csa
    end
end