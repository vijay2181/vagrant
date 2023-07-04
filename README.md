# vagrant

If you need both a static IP address and internet access for your Vagrant virtual machine, you can configure a private network with a static IP

By specifying a static IP address in the Vagrantfile, your Ubuntu virtual machine will be assigned the provided IP address within the private network.

After making these changes, you can bring up the virtual machine using the vagrant up command. The virtual machine will have the static IP address you configured, and it should also have internet access through your host machine's network connection.

Remember that using a static IP address may require additional configuration on your local network, such as ensuring that the chosen IP address is within the range of your router's subnet and not conflicting with other devices on the network.

```
Wireless LAN adapter WiFi:

   Connection-specific DNS Suffix  . :
   Link-local IPv6 Address . . . . . : fe80::edf8:bbfc:49a9:9eeb%39
   IPv4 Address. . . . . . . . . . . : 192.168.0.212
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 192.168.0.1

```

For example, you can choose an IP address like 192.168.0.100 or 10.0.0.50, depending on the subnet range of your local network.

```
C:\Users>ping 192.168.50.50

Pinging 192.168.50.50 with 32 bytes of data:
Request timed out.
```
it means any other devcie in your network is assigned with that ip and it is timedout, may not not running

```
C:\Users\Vanuganti>ping 192.168.0.100

Pinging 192.168.0.100 with 32 bytes of data:
Reply from 192.168.0.212: Destination host unreachable.
```
means that ip is not assigned to any other device in your network

##Vgarntfile with Port Forwarding

For example, to install Docker and Docker Compose, and run an Apache container inside your Vagrant VM, you can modify your Vagrantfile as follows:

To access the Apache server running inside the VM from your host machine, you can set up port forwarding in the Vagrantfile.

```
config.vm.network "forwarded_port", guest: 80, host: 8080
```

This line sets up port forwarding, where port 80 of the VM (guest) is mapped to port 8080 of the host machine. You can modify the host port to any available port on your host machine if necessary.

Once the VM is running, you can access the Apache server from your host machine by visiting **http://localhost:8080** or **http://127.0.0.1:8080/**
in a web browser. The traffic will be forwarded to port 80 of the VM, where the Apache server is listening.

Please note that if port 8080 is already in use on your host machine, you'll need to choose a different port for the forwarding.

```
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/focal64"

  config.vm.network "private_network", ip: "192.168.0.100"

  config.vm.network "forwarded_port", guest: 80, host: 8080

  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--memory", "2048"]
    vb.customize ["modifyvm", :id, "--cpus", "2"]
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
    vb.name = "VAGRANT-BOX"
  end

  config.vm.provision "shell", inline: <<-SHELL
   sudo apt-get update -y
   sudo apt-get -y upgrade

   # Install python3
   sudo apt install -y python3-pip
   sudo apt install -y build-essential libssl-dev libffi-dev python3-dev
   sudo apt install -y python3-venv

   # Install Docker
   curl -fsSL https://get.docker.com -o get-docker.sh
   sudo sh get-docker.sh
   sudo usermod -aG docker vagrant

   # Install Docker Compose
   sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
   sudo chmod +x /usr/local/bin/docker-compose

  SHELL
end
```

run docker container inside virtualmachine

```
# Run Apache container
sudo docker run -d -p 8080:80 --name apache httpd:latest
```
