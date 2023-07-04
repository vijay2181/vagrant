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
C:\Users\Vanuganti>ping 192.168.50.100

Pinging 192.168.50.100 with 32 bytes of data:
Reply from 192.168.0.212: Destination host unreachable.
```
means that ip is not assigned to any other device in your network

## Vagrantfile with Port Forwarding

For example, to install Docker and Docker Compose, and run an Apache container inside your Vagrant VM, vagrant is installed on windows, you can modify your Vagrantfile as follows:

```
config.vm.network "forwarded_port", guest: 4000, host: 8000
```

This line sets up port forwarding, where port 4000 of the VM(guest machine) is forwarded to 8000 of windows(host machine). You can modify the host port to any available port on your host machine if necessary.

```
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/focal64"

  config.vm.network "private_network", ip: "192.168.50.100"

  config.vm.network "forwarded_port", guest: 4000, host: 8000

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

if the machine is already in up  and running and you changed port, you can use below command to reload the configurations

```
vagrant reload
vagrant ssh
```

![image](https://github.com/vijay2181/vagrant/assets/66196388/98e8d827-44b5-445c-8d2d-fbef00c67e5b)

   
```
# Run Apache container inside vagrant VM
sudo docker run -d -p 4000:80 --name apache httpd:latest
```
HERE, 
CONTAINER PORT(80) FORWORDED TO --> VM PORT(4000) FORWARDED TO --> WINDOWS HOST(8000)  --> http://localhost:8000  --> BROWSER

Once the VM is running, you can access the Apache server from your host machine by visiting **http://localhost:80** or **http://127.0.0.1:80/**
in a web browser. The traffic will be forwarded to port 80 of the container, where the Apache server is listening.

Please note that if port 8000 is already in use on your host machine, you'll need to choose a different port for the forwarding.

```
# To check open ports in linux machine
netstat -tulpn
```


```
#INSIDE VAGRANT VM
vagrant@ubuntu-focal:~$ curl http://localhost:4000
<html><body><h1>It works!</h1></body></html>
```
```
#INSIDE WINDOWS CMD PROMPT
C:\Users\vijay>curl http://127.0.0.1:8000
<html><body><h1>It works!</h1></body></html>
```

open browser in windows host machine **http://localhost:8000 **


![image](https://github.com/vijay2181/vagrant/assets/66196388/c8dda8c0-3cd8-4665-b1d6-54858120b683)

![image](https://github.com/vijay2181/vagrant/assets/66196388/19cfaad4-7895-458a-8e93-4fa06a902504)
