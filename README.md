Muh Hilmy Thoriq YR (05111942000012)<br>
Salsabila Irbah (05111942000007)<br>
Nadhif Bhagawanta Hadiprayitno (05111942000029)

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
**Difficulties:**
- No problem

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

**Difficulties:**
- No Problem

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

**Difficulties:**
- No Problem

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

Next we change the network config of skypie to <br>
![network config skypie](https://user-images.githubusercontent.com/81411468/141282340-50f3389a-51e4-45d4-8c72-36b6092adec8.PNG)

Then we can restart the DHCP server using `service isc-dhcp-server restart` and restart the skypie

**Screenshot** <br>
![Skypie 69](https://user-images.githubusercontent.com/81411468/141282640-73681a4d-e435-40de-88e3-8db18c1a6254.jpg)

**Difficulties:**
- No Problem

# Problem 8
We want to make the water7 as our proxy server and set it in Loguetown

First we need to edit `squid.conf` file.
```
// in Water7
http_port 5000
visible_hostname jualbelikapal.iup4.com

http_access allow all
```

Then we need to edit `named.conf.options` file.
```
// in enieslobby
forwarders {
        192.168.122.1
};

// dnssec-validation auto;
allow-query { any; };
```

Finally we can run `export http_proxy=http://192.210.2.3:5000` in loguetown and `lynx http://its.ac.id`<br>
![lynx benar](https://user-images.githubusercontent.com/81411468/141505925-53d2de18-844d-46ea-ab79-86f5c7d42768.PNG)

**Difficulties:**
- No Problem

# Problem 9
We want to make username and password for using the link.

First we need to input the username and password we want, (using MD5).
```
// in water7 run
htpasswd -m /etc/squid/passwd luffybelikapaliup4
luffy_iup4
htpasswd -m /etc/squid/passwd zorobelikapaliup4
zoro_iup4
```

Then we add the `squid.conf`.
```
// in water7
http_port 5000
visible_hostname jualbelikapal.iup4.com

auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic children 5
auth_param basic realm Proxy
auth_param basic credentialsttl 2 hours
auth_param basic casesensitive on
acl USERS proxy_auth REQUIRED
http_access allow USERS
```
<br>
![bukti minta username pass](https://user-images.githubusercontent.com/81411468/141506499-96f2fece-3ba9-4e5a-96f1-32c825505abd.PNG)

**Difficulties:**
- No Problem

# Problem 10
We want to make the link can be access in desired time.

First we want to edit `acl.conf` file.
```
// in water7
acl AVAILABLE_WORKING_1 time MTWH 07:00-11:00
acl AVAILABLE_WORKING_2 time TWHF 17:00-24:00
acl AVAILABLE_WORKING_3 time WHFA 00:00-03:00
```

next we need to add something in `squid.conf`
```
// in water7
http_access allow USERS AVAILABLE_WORKING_1
http_access allow USERS AVAILABLE_WORKING_2
http_access allow USERS AVAILABLE_WORKING_3
```

**When time allowed**<br>
![lynx benar](https://user-images.githubusercontent.com/81411468/141507162-2f997c0e-621c-47d3-8117-e10ab4a945eb.PNG)

**When time not allowed**<br>
![lynx error](https://user-images.githubusercontent.com/81411468/141507200-0637005e-bf18-4c4a-abdd-46cd4e8ac109.PNG)

**Difficulties:**
- Try hard so the syntax not too long

# Problem 11
We want to make when we open google.com it redirect to super.franky.iup4.com

First we need to edit `named.conf.local` file.
```
// in Enieslobby
zone "super.franky.iup4.com" {
    type master;
    file "/etc/bind/kaizoku/super.franky.iup4.com";
    allow-transfer { 192.210.3.69; }; // Masukan IP Skypie tanpa tanda petik
};

zone "3.210.192.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/3.210.192.in-addr.arpa";
};
```
Next we need to make the directory for both zone, using `mkdir /etc/bind/jarkom` and `mkdir /etc/bind/kaizoku`<br>

Next we have to edit the settings for both zone.
```
// in Enieslobby /etc/bind/kaizoku/super.franky.iup4.com
$TTL    604800
@       IN      SOA     super.franky.iup4.com. root.super.franky.iup4.com. (
                     2021192210         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      super.franky.iup4.com.
@       IN      A       192.210.3.69
www     IN      CNAME   super.franky.iup4.com.
```
<br>
```
// in Enieslobby /etc/bind/jarkom/3.210.192.in-addr.arpa
$TTL    604800
@       IN      SOA     super.franky.iup4.com. root.super.franky.iup4.com. (
                              2021192210                ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
3.210.192.in-addr.arpa. IN      NS      super.franky.iup4.com.
69      IN      PTR     192.210.3.69
```
Next we want make directory in skypie using `mkdir /var/www/super.franky.iup4.com` and download and unzip the file in there.<br>

After that we want add  in `apache2.conf`.
```
// in skypie
ServerName 192.210.3.69
```

Next we have to edit `super.franky.iup4.com.conf` file.
```
// in skypie
<VirtualHost *:80>
    ServerName super.franky.iup4.com
    ServerAlias www.super.franky.iup4.com

    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/super.franky.iup4.com

    <Directory /var/www/super.franky.iup4.com>
        Options +Indexes
        AllowOverride All
    </Directory>

    <Directory /var/www/super.franky.iup4.com/public>
        Options +Indexes
    </Directory>

    Alias "/js" "/var/www/super.franky.iup4.com/public/js"

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    ErrorDocument 404 /error/404.html
</VirtualHost>
```
Next we want to add something in `squid.conf`.
```
// in Water7
acl GOOGLE dstdomain [-n] .google.com
deny_info http://www.super.franky.iup4.com GOOGLE
http_access deny GOOGLE
http_reply_access deny GOOGLE
```

Finally we need to restart both apache2 ,squid, and bind. Last we need to check it using `lynx google.com`
![Lynx google](https://user-images.githubusercontent.com/81411468/141509423-2c752681-4dff-4498-a1b7-d5372481762e.PNG)

**Difficulties:**
- At first, confuse to make it redirect. Then using reverse dns to solve the problem.


# Problem 12,13
We want to limit the bandwidth when download using luffy username, and not limit the bandwidth when using zoro username

first we need to edit `acl-bandwidth.conf`
```
// in water7
acl download url_regex -i \.jpg$ \.png$

auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd

acl luffy proxy_auth luffybelikapaliup4
acl zoro proxy_auth zorobelikapaliup4

delay_pools 2
delay_class 1 1
delay_parameters 1 1250/1250
delay_access 1 deny zoro
delay_access 1 allow download
delay_access 1 deny all

delay_class 2 1
delay_parameters 2 -1/-1
delay_access 2 allow zoro
delay_access 2 deny luffy
delay_access 2 deny all
```

Then we need to add in `squid.conf` (add at the top)
```
// in water7
include /etc/squid/acl-bandwidth.conf
```
**Luffy Bandwidth**<br>
![12](https://user-images.githubusercontent.com/81411468/141510413-90c7ef4f-48c8-46a5-9ea7-8c094664a6d1.PNG)

**Zoro Bandwidth**<br>
![13](https://user-images.githubusercontent.com/81411468/141510446-83d9eda4-7626-4633-851a-a198d7120c31.PNG)

**Difficulties:**
- No Problem






