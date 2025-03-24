# DNS-Server-with-BIND9

# DNS Configuration for prasad.com

This repository contains the configuration files for setting up a **DNS server** using BIND9 for the domain `prasad.com`. The DNS server is set up to handle forward and reverse DNS lookups.

## Setup Overview

### 1. **Install BIND9 DNS Server**
   First, update your package list and install BIND9:
   
      sudo apt update
      sudo apt install bind9 bind9utils bind9-doc

### 2. **Create Zone Files**
We'll create two zone files:

        Forward Zone (Handles domain name to IP resolution)
        Reverse Zone (Handles IP to domain name resolution)

---



3. Configure BIND9 to Use the Zone Files
   
Now, tell BIND where to find these zone files. Open the BIND configuration file:


         sudo nano /etc/bind/named.conf.local

 Add these lines:

    zone "prasad.com" {
    type master;
    file "/etc/bind/zones/forward.zone";
    };

    zone "8.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/zones/reverse.zone";
    };
 Save and exit.

4. Restart the BIND9 Service
   
Apply the changes by restarting BIND9:

         sudo systemctl restart bind9
         sudo systemctl enable bind9
   
6. Test the DNS Server

Use nslookup or dig to check if the DNS server is working:

         nslookup www.prasad.com 192.168.8.101
    
 Expected output:

         Server:  192.168.8.101
         Address: 192.168.8.101#53

Name: www.prasad.com
Address: 192.168.8.101

6. Set DNS Server on Client Machine
If you're using a client machine, set 192.168.8.101 as the DNS server in /etc/resolv.conf:

         sudo nano /etc/resolv.conf
         nameserver 192.168.8.101




## Forward Zone File (forward.zone) and Reserve Zone File (reserve.zone)

Forward Zone File (forward.zone)

The forward zone file contains A records, NS records, and the SOA record. Here is the configuration for `prasad.com`:

```bash
$TTL 86400
@          IN            SOA             ns1.prasad.com.    root.prasad.com. (
           20240323      ; Serial
           3600          ; Refresh (1 hour)
           1800          ; Retry (30 minutes)
           604800        ; Expire (7 days)
           86400         ; Minimum TTL (1 day)
)

@          IN            NS             ns1.prasad.com.   ; Nameserver for the domain

ns1        IN            A              192.168.8.101     ; IP for ns1.prasad.com
www        IN            A              192.168.8.101     ; IP for www.prasad.com
mail       IN            A              192.168.8.101     ; IP for mail.prasad.com

---


Reverse Zone File (reserve.zone)

This file defines the reverse DNS records, mapping IP addresses back to domain names using PTR records.

```bash

$TTL 86400
@          IN            SOA             ns1.prasad.com.    root.prasad.com. (
           20240323      ; Serial
           3600          ; Refresh (1 hour)
           1800          ; Retry (30 minutes)
           604800        ; Expire (7 days)
           86400         ; Minimum TTL (1 day)
)

@          IN            NS             ns1.prasad.com.   ; Nameserver for the reverse zone

; Reverse DNS records for the IP 192.168.8.101
101        IN            PTR            ns1.prasad.com.   ; Reverse DNS for ns1.prasad.com
101        IN            PTR            www.prasad.com.   ; Reverse DNS for www.prasad.com
101        IN            PTR            mail.prasad.com.  ; Reverse DNS for mail.prasad.com











