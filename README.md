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
