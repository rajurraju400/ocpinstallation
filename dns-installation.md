# DNS server installation and it's configuration


## Introduction

An important part of managing server configuration and infrastructure includes maintaining an easy way to look up network interfaces and IP addresses by name, by setting up a proper Domain Name System (DNS). Using fully qualified domain names (FQDNs), instead of IP addresses, to specify network addresses eases the configuration of services and applications, and increases the maintainability of configuration files. Setting up your own DNS for your private network is a great way to improve the management of your servers.

In this tutorial, we will go over how to set up an internal DNS server, using the BIND name server software (BIND9) on CentOS 7, that can be used by your Virtual Private Servers (VPS) to resolve private host names and private IP addresses. This provides a central way to manage your internal hostnames and private IP addresses, which is indispensable when your environment expands to more than a few hosts.

## Install BIND on DNS Servers

install BIND with yum:
```
sudo yum install bind bind-utils
```
## Configure Bind

BIND’s process is known as named. As such, many of the files refer to “named” instead of “BIND”.

open the [named.conf](./files/named.conf) file for editing:



```
sudo vi /etc/named.conf
//
// named.conf
//
// Provided by Red Hat bind package to configure the ISC BIND named(8) DNS
// server as a caching only nameserver (as a localhost DNS resolver only).
//
// See /usr/share/doc/bind*/sample/ for example named configuration files.
//
// See the BIND Administrator's Reference Manual (ARM) for details about the
// configuration located in /usr/share/doc/bind-{version}/Bv9ARM.html

options {
	listen-on port 53 { 127.0.0.1; 192.168.0.125; };
#	listen-on-v6 port 53 { ::1; };
	directory 	"/var/named";
	dump-file 	"/var/named/data/cache_dump.db";
	statistics-file "/var/named/data/named_stats.txt";
	memstatistics-file "/var/named/data/named_mem_stats.txt";
	recursing-file  "/var/named/data/named.recursing";
	secroots-file   "/var/named/data/named.secroots";
	allow-query     { any; };
#	allow-query     { localhost; 192.168.0.0/24; };

	/*
	 - If you are building an AUTHORITATIVE DNS server, do NOT enable recursion.
	 - If you are building a RECURSIVE (caching) DNS server, you need to enable
	   recursion.
	 - If your recursive DNS server has a public IP address, you MUST enable access
	   control to limit queries to your legitimate users. Failing to do so will
	   cause your server to become part of large scale DNS amplification
	   attacks. Implementing BCP38 within your network would greatly
	   reduce such attack surface
	*/
	recursion yes;

	dnssec-enable yes;
	dnssec-validation yes;

	# Using Google DNS
	forwarders {
                8.8.8.8;
                8.8.4.4;
        };

	/* Path to ISC DLV key */
	bindkeys-file "/etc/named.root.key";

	managed-keys-directory "/var/named/dynamic";

	pid-file "/run/named/named.pid";
	session-keyfile "/run/named/session.key";
};

logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};

zone "." IN {
	type hint;
	file "named.ca";
};

# Include ocp zones

zone "tnc.bootcamp.lab" {
    type master;
    file "/etc/named/zones/db.tnc.bootcamp.lab";
};

zone "125.168.192.in-addr.arpa" {
    type master;
    file "/etc/named/zones/db.192.168.125";
};


include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";

```

## Create Forward / reverse Zone File

The forward zone file is where we define DNS records for forward DNS lookups. That is, when the DNS receives a name query, “host1.nyc3.example.com” for example, it will look in the forward zone file to resolve host1’s corresponding private IP address.

 [forward file](./files/named/zones/db.tnc.bootcamp.lab) and [reverse file](./files/named/zones/db.192.168.125)

```
sudo chmod 755 /etc/named
sudo mkdir /etc/named/zones
```

Now let’s edit our forward zone file:


```
cat > db.tnc.bootcamp.lab
$TTL 86400
@   IN  SOA     ns1.tnc.bootcamp.lab. root.tnc.bootcamp.lab. (
                2024080501  ; Serial
                3600        ; Refresh
                1800        ; Retry
                1209600     ; Expire
                86400 )     ; Minimum TTL
;
@       IN  NS      ns1.tnc.bootcamp.lab.
@       IN  A       192.168.125.1
ns1     IN  A       192.168.125.1

api.hub         IN  A       192.168.125.100
api-int.hub     IN  A       192.168.125.100
*.apps.hub      IN  A       192.168.125.100
quay            IN  A       192.168.125.1


cat > db.192.168.125
$TTL 86400
@   IN  SOA     ns1.tnc.bootcamp.lab. root.tnc.bootcamp.lab. (
                2024080501  ; Serial
                3600        ; Refresh
                1800        ; Retry
                1209600     ; Expire
                86400 )     ; Minimum TTL
;
@       IN  NS      ns1.tnc.bootcamp.lab.
@       IN  PTR     tnc.bootcamp.lab.

100     IN  PTR     api.hub.tnc.bootcamp.lab.
100     IN  PTR     api-int.hub.tnc.bootcamp.lab.
100     IN  PTR     apps.hub.tnc.bootcamp.lab.
1       IN  PTR     quay.tnc.bootcamp.lab.


```


## verification 

configure the dns server ip address on your `/etc/resolv.conf `

```
venkatapathirajr@M-J00JD2MD27 ocpinstallation % cat /etc/resolv.conf 
#
# macOS Notice
#
# This file is not consulted for DNS hostname resolution, address
# resolution, or the DNS query routing mechanism used by most
# processes on this system.
#
# To view the DNS configuration used by this system, use:
#   scutil --dns
#
# SEE ALSO
#   dns-sd(1), scutil(8)
#
# This file is automatically generated.
#
nameserver 192.168.0.125
venkatapathirajr@M-J00JD2MD27 ocpinstallation % 
```

try doing some nslookup or dig for some names we configured

```
venkatapathirajr@M-J00JD2MD27 ocpinstallation % nslookup api.hub.tnc.bootcamp.lab
Server:		192.168.0.125
Address:	192.168.0.125#53

Name:	api.hub.tnc.bootcamp.lab
Address: 192.168.125.100

venkatapathirajr@M-J00JD2MD27 ocpinstallation % nslookup quay.tnc.bootcamp.lab   
Server:		192.168.0.125
Address:	192.168.0.125#53

Name:	quay.tnc.bootcamp.lab
Address: 192.168.125.11

venkatapathirajr@M-J00JD2MD27 ocpinstallation % nslookup bastion.tnc.bootcamp.lab
Server:		192.168.0.125
Address:	192.168.0.125#53

Name:	bastion.tnc.bootcamp.lab
Address: 192.168.125.10

venkatapathirajr@M-J00JD2MD27 ocpinstallation %
```