# ANSWER

# PROBLEM 1
First we need to install to Enieslobby as DNS server, Jipangu as DHCP server, and Water7 as Proxy server
```
// in EniesLobby
apt-get update
apt-get install bind9 -y
```

```
// in Jipangu
apt-get update
apt-get install isc-dhcp-server -y
```

```
// in Water7
apt-get update
apt-get install squid -y
```

Then we need to setting the DHCP server so that can connect to foosha as mentioned in `Problem 2` 
we change the interfaces in `/etc/default/isc-dhcp-server` 
```
// in Jipangu
INTERFACES="eth0"
```

# Problem 2
We want to set the foosha as DHCP relay, first we need to install
```
// in Foosha
apt-get update
apt-get install isc-dhcp-relay -y
```
After we install using `apt-get install isc-dhcp-relay -y` there will be some settings we need to input so we can input
```
192.210.2.4
eth1 eth2 eth3

```
`192.210.2.4` means the ip of our DHCP server `jipangu`, `eth1 eth2 eth3` means the interfaces of our dhcp relay that connect to switch 1, 2 and 3. the last part is the `option` that we can leave it blank

# Problem 3,4,5,6
Here we need to setting the subnet in our DHCP server, we can edit the file in `/etc/dhcp/dhcpd.conf`
```
// in Jipangu
subnet 192.210.1.0 netmask 255.255.255.0 {
    range 192.210.1.20 192.210.1.99;
    range 192.210.1.150 192.210.1.169;
    option routers 192.210.1.1;
    option broadcast-address 192.210.1.255;
    option domain-name-servers 192.210.2.2;
    default-lease-time 360;
    max-lease-time 7200;
}

subnet 192.210.3.0 netmask 255.255.255.0 {
    range 192.210.3.30 192.210.3.50;
    option routers 192.210.3.1;
    option broadcast-address 192.210.3.255;
    option domain-name-servers 192.210.2.2;
    default-lease-time 720;
    max-lease-time 7200;
}

subnet 192.210.2.0 netmask 255.255.255.0 {
        option routers 192.210.2.1;
}
```
For `Problem 3` we change the switch 1 ( subnet 192.210.1.0 ) range to `192.210.1.20 until 192.210.1.99` and `192.210.1.150 until 192.210.1.169`

For `Problem 4` we change the switch 3 ( subnet 192.210.3.0 ) range to `192.210.3.30 until 192.210.3.50`

For `Problem 5` we change all the `option domain-name-servers` into our DNS IP

For `Problem 6` we change the `default-lease-time` to the question asked, for switch 1 we change it to `360` or 6 minutes and switch 3 to `720` or 12 minutes and change the `max-lease-time` in both switch to `7200` or 120 minutes

next we need to change the network configuration of our client to
```
auto eth0
iface eth0 inet dhcp
```
then we can restart our DHCP server using `service isc-dhcp-server restart` and our client

**Screenshot**<br>
Problem 3, Problem 5
![Ip a loguetown](https://user-images.githubusercontent.com/81411468/141279923-653e6f64-81f0-480a-be3c-0caeccbe0ae9.jpg)

Problem 4, Problem 5
![Ip a Totto](https://user-images.githubusercontent.com/81411468/141280084-d18a7866-50a4-4080-9772-492c3981e8ee.jpg)

# Problem 7
We want to make the skypie ip to be fixed at `192.210.3.69` <br>
first we need to check the skypie physical address, we check in `ip a`
![Skypie hwadd](https://user-images.githubusercontent.com/81411468/141281265-9cd6ddbb-2e19-4e1c-a889-78de91992224.jpg)

Next we need to setting the DHCP server, we can edit in `/etc/dhcp/dhcpd.conf`
```
// in Jipangu
host skypie{
  hardware ethernet 62:b4:f1:09:ca:82;
  fixed-address 192.210.3.69;
}
```

We change the `hardware ethernet` to our skypie's physical address, and change the `fixed-address` to what the question want<br>

Next we change the network config of skypie to 
![network config skypie](https://user-images.githubusercontent.com/81411468/141282340-50f3389a-51e4-45d4-8c72-36b6092adec8.PNG)

Then we can restart the DHCP server using `service isc-dhcp-server restart` and restart the skypie

**Screenshot** <br>
![Skypie 69](https://user-images.githubusercontent.com/81411468/141282640-73681a4d-e435-40de-88e3-8db18c1a6254.jpg)

# Problem 8
We want to make the water7 as our proxy server and set it in Loguetown







