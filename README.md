# 🧠 BIND9 DNS Server Setup

This repository contains step-by-step instructions and configuration files to set up a **BIND9 DNS server** for the custom domain `prasad.com`, including **forward** and **reverse DNS** lookups.

---

## 📦 Prerequisites

- A Linux server (e.g., Ubuntu)
- Static IP address: `192.168.8.101`
- Root or sudo privileges

  
![Ubuntu VM ip address](https://github.com/user-attachments/assets/678d55eb-0fdd-4293-b79e-0b209406b04e)


---

## ⚙️ Installation

### 1. Update and Install BIND9

```bash
sudo apt update
sudo apt install bind9 bind9utils bind9-doc
```
![Install-bind9](https://github.com/user-attachments/assets/6fc0f1bf-9716-4bdf-af57-b2561ce9eff0)

---

## 📁 BIND Configuration Steps

### 2. Configure Global Options

Edit the global options file:

```bash
sudo nano /etc/bind/named.conf.options
```

Add or update with:

```bash
options {
    directory "/var/cache/bind";

    recursion yes;
    allow-query { any; };

    forwarders {
        8.8.8.8;
        8.8.4.4;
    };

    dnssec-validation auto;
    listen-on-v6 { any; };
};
```
![named conf options](https://github.com/user-attachments/assets/79b5ec98-a8f6-450f-9e13-a47129f0e69c)


---

### 3. Define Zone Files

Edit BIND’s local configuration:

```bash
sudo nano /etc/bind/named.conf.local
```

Add the following zone entries:

```bash
zone "prasad.com" {
    type master;
    file "/etc/bind/zones/forward.zone";
};

zone "8.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/zones/reverse.zone";
};
```
![named conf local](https://github.com/user-attachments/assets/ac508b61-3e7b-4bef-a8cd-5e6d56c2e062)


---

### 4. Create Zone Directory

```bash
sudo mkdir -p /etc/bind/zones
```

---

## 🗂️ Zone File Setup

### 5. Forward Zone – `forward.zone`

```bash
sudo nano /etc/bind/zones/forward.zone
```

Paste this content:

```bash
$TTL 86400
@    IN    SOA     ns1.prasad.com. root.prasad.com. (
            20240323 ; Serial
            3600     ; Refresh
            1800     ; Retry
            604800   ; Expire
            86400 )  ; Minimum TTL

@    IN    NS      ns1.prasad.com.
ns1  IN    A       192.168.8.101
www  IN    A       192.168.8.101
mail IN    A       192.168.8.101
```
![forward zone](https://github.com/user-attachments/assets/ee62afc2-e0f3-4b97-a2ef-2030011db6e6)


---

### 6. Reverse Zone – `reverse.zone`

```bash
sudo nano /etc/bind/zones/reverse.zone
```

Paste this content:

```bash
$TTL 86400
@    IN    SOA     ns1.prasad.com. root.prasad.com. (
            20240323 ; Serial
            3600     ; Refresh
            1800     ; Retry
            604800   ; Expire
            86400 )  ; Minimum TTL

@    IN    NS      ns1.prasad.com.
101  IN    PTR     ns1.prasad.com.
101  IN    PTR     www.prasad.com.
101  IN    PTR     mail.prasad.com.
```
![reserve zone](https://github.com/user-attachments/assets/8070f80d-f5b1-4dfb-af23-c55186985e5a)


---

## 🔄 Restart and Enable BIND9

```bash
sudo systemctl restart bind9
sudo systemctl enable bind9
```
![config](https://github.com/user-attachments/assets/8e5b2af6-39f2-45fd-9f84-50c8bf6165b2)


---

## 🔄 Adding Firewall rules for BIND9

```bash
sudo ufw status
sudo ufw enable
sudo ufw status
sudo ufw allow bind9
sudo ufw reload
sudo ufw status

```
![firewall-rules](https://github.com/user-attachments/assets/bcd6778c-eefc-4979-8be3-9abfd0b22a73)


---

## ✅ Testing the DNS Server

Use `nslookup` to check DNS resolution:

```bash
nslookup www.prasad.com 192.168.8.101
```

Expected Output:

```
Server:  192.168.8.101
Address: 192.168.8.101#53

Name:    www.prasad.com
Address: 192.168.8.101
```
![test 01](https://github.com/user-attachments/assets/af28cc7b-257d-4219-b1a0-e2d2d1026160)


---

## 💻 Set DNS Server on Client Machine

Edit your client's resolver configuration:

```bash
sudo nano /etc/resolv.conf
```

Add the following:

```
nameserver 192.168.8.101
```

---

## 🧪 Verify Reverse Lookup

```bash
nslookup 192.168.8.101
```

![Client Test 01](https://github.com/user-attachments/assets/1e27f148-5f56-450e-bd0a-8f09ea00a1fe)
![Client Test 02](https://github.com/user-attachments/assets/239ec477-25a8-4100-ad7e-404cef8c6470)


---
