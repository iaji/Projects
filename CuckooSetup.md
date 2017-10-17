# Cuckoo 2.0.4 Installation

## Getting Pre-Requisites

**This guide is for Ubuntu 16.04**
**All commands will be italicized**

*sudo apt-get update*

*sudo adduser cuckoo*

*su - 'HOSTUSER'* example su - aj

*sudo usermod -a -G sudo cuckoo*

*su - cuckoo*

*sudo apt-get install python python-pip python-dev libffi-dev libssl-dev git*

*sudo apt-get install libxml2-dev libxslt-dev*

*sudo apt-get install python-virtualenv python-setuptools libjpeg-dev zlib1g-dev swig*

*sudo apt-get install tcpdump apparmor-utils*

*sudo apt-get install mongodb*

*sudo apt-get install libcap2-bin*

*sudo apt-get install autoconf*

*sudo apt-get install libtool-bin*

*sudo apt-get install libjansson-dev*

*sudo apt-get install libmagic-dev*

*sudo apt-get install python3-pip python3-dev libssl-dev libtiff5-dev libjpeg8-dev zlib1g-dev libwebp-dev*

### Cuckoo Pre Setup

**“Due to Cuckoo now being an official Python Package we have a much tighter control on how its installed on users’ systems. No longer will users have incorrect versions of third party libraries installed breaking their setup.”**

*sudo -H pip install -U pip setuptools*

*sudo -H pip install -U cuckoo*

*pip show cuckoo*


### TCPDump Setup

*sudo aa-disable /usr/sbin/tcpdump*

*sudo setcap cap_net_raw,cap_net_admin=eip /usr/sbin/tcpdump*

*getcap /usr/sbin/tcpdump*


### Volatility Setup

**This analyzes memory dumps for cuckoo**

*git clone https://github.com/volatilityfoundation/volatility.git*

*cd volatility*

*sudo python setup.py install*

*cd ..*

### Distorm Setup

*wget https://github.com/gdabah/distorm/archive/v3.3.4.tar.gz*

*tar -xvzf v3.3.4.tar.gz*

*cd distorm-3.3.4/*

*sudo python setup.py install*

*cd ..*

### YARA Setup

*wget https://github.com/VirusTotal/yara/archive/v3.6.3.tar.gz*

*tar -xvzf v3.6.3.tar.gz*

*cd yara-3.6.3/*

*./bootstrap.sh*

*./configure --with-crypto --enable-magic --enable-cuckoo*

*make*

*sudo make install*

*sudo -H pip install yara-python*

### PyCrypto Setup

*wget https://pypi.python.org/packages/60/db/645aa9af249f059cc3a368b118de33889219e0362141e75d4eaf6f80f163/pycrypto-2.6.1.tar.gz*

*tar -xvzf pycrypto-2.6.1.tar.gz*

*cd pycrypto-2.6.1/*

*python setup.py build*

*sudo python setup.py install*

### MITM Proxy Setup

*sudo -H pip install ujson*

*sudo -H pip install jupyter*

*sudo pip3 install mitmproxy*

*cd .mitmproxy/*

*cp mitmproxy-ca-cert.p12 /home/cuckoo/.cuckoo/analyzer/windows/bin/cert.p12*

*mitmdump = /usr/local/bin/mitmdump*

### VirtualBox Setup

*sudo apt-add-repository "deb http://download.virtualbox.org/virtualbox/debian $(lsb_release -sc) contrib"*

*wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -*

*sudo apt-get update*

*sudo apt-get install virtualbox-5.1*

*wget http://download.virtualbox.org/virtualbox/5.1.26/Oracle_VM_VirtualBox_Extension_Pack-5.1.26-117224.vbox-extpack*

*sudo vboxmanage extpack install Oracle_VM_VirtualBox_Extension_Pack-5.1.26-117224.vbox-extpack*

*sudo usermod -a -G vboxusers cuckoo*

*vboxmanage hostonlyif create*

*vboxmanage hostonlyif ipconfig vboxnet0 --ip 192.168.56.1*

*vboxmanage createvm --name "Windows10VM" --ostype Windows10 --register*

*vboxmanage modifyvm "Windows10VM" --memory 2048 --acpi on --boot1 dvd --nic1 nat*

*vboxmanage createhd --filename "Windows10VM.vdi" --size 20000*

*vboxmanage storagectl "Windows10VM" --name "IDE Controller" --add ide --controller PIIX4*

*vboxmanage storageattach "Windows10VM" --storagectl "IDE Controller" --port 0 --device 0 --type hdd --medium "Windows10VM.vdi"*

*vboxmanage storageattach "Windows10VM" --storagectl "IDE Controller" --port 0 --device 1 --type dvddrive --medium PATH/TO/YOUR/WINDOWS/.ISO*

*vboxmanage modifyvm "Windows10VM" --hostonlyadapter1 vboxnet0*

*vboxmanage modifyvm "Windows10VM" --nic1 hostonly*

*virtualbox*
#### Inside The VM

**Open up your vm and install windows, there will be some things you have to do:**

* Shut down the firewall
* Shut down Windows Defender

#### Configure Network Settings

![](https://fud.community.services.support.microsoft.com/Fud/FileDownloadHandler.ashx?fid=3f57cbec-b300-4d94-be47-c0b902ba03e9)
![](https://fud.community.services.support.microsoft.com/Fud/FileDownloadHandler.ashx?fid=d3f2d78c-9a6e-488e-9245-50badf1020d7)
![](https://fud.community.services.support.microsoft.com/Fud/FileDownloadHandler.ashx?fid=579bcc7a-6a9d-4322-977e-685ab33f440e)
![](https://fud.community.services.support.microsoft.com/Fud/FileDownloadHandler.ashx?fid=da4c4608-aee7-46e3-9a5f-192075dd8602)

**Static IP - 192.168.56.101
DNS - any DNS server (8.8.8.8)
Default Gateway - 192.168.56.1
Validate on Exit**

#### Install VirtualBox Guest Additions

![](http://www.dedoimedo.com/images/computers/2008/virtualbox-install-guest-additions.jpg)

**Follow the install Prompt**

#### Setting up the Cuckoo Agent

* **Set up a shared folder from the VM to .cuckoo/agent**
* copy agent.py to the VM


#### Back to Ubuntu for VM internet

**These next commands will allow the VM to have internet access**

*iptables -A FORWARD -o “ACTIVEINTERFACE” -i vboxnet0 -s 192.168.56.0/24 -m conntrack --ctstate NEW -j ACCEPT* ACTIVEINTERFACE will be your connected interface when ifcongig is ran.  It will be something like wlps3 or eth0

*iptables -A FORWARD -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT*

*iptables -A POSTROUTING -t nat -j MASQUERADE*

*sudo su*

*echo 1 > /proc/sys/net/ipv4/ip_forward*

*exit*

#### Back to the VM

* **Install Python 2.7 to the VM from pythons website**
* **run agent.py with admin rights**

#### Back to Ubuntu to Snapshot VM

*vboxmanage snapshot "Windows10VM" take "snapshot01" --pause*



## Bringing It All Together

*cuckoo -d* This command will setup default config files

### Cuckoo Config Files

*cd .cuckoo/conf*

*nano cuckoo.conf*

* [cuckoo]
    * memory_dump = on
    * machinery = virtualbox
* [resultserver]
    * ip = 192.168.56.1

*nano auxiliary.conf*

* [mitm]
    * enable = yes
* [sniffer]
    * interface = vboxnet0

*nano virtualbox.conf*

* [virtualbox]
    * machines =  Windows10VM
    * interface = vboxnet0
    * [Windows10VM]
OUTDATED LINE -  vm_path = PATH/TO/VBOX/FILE

*nano processing.conf*

* [memory]
    * enable = yes

*nano memory.conf*

* [basic]
    * guest_profile = Win10x64

*nano reporting.conf*
    * [reporthtml]
        * enable = yes
    * [mongodb]
        * enable = yes

# !!!Experimental Section!!!


**Install and Configure InetSim**

*sudo su*

*echo "deb http://www.inetsim.org/debian/ binary/" > /etc/apt/sources.list.d/inetsim.list*

*wget -O - http://www.inetsim.org/inetsim-archive-signing-key.asc | apt-key add -*

*sudo apt-get update*

*sudo apt-get install inetsim*

*sudo nano /etc/default/inetsim*

Change ENABLED=0 to 1

exit

*sudo nano /etc/inetsim/inetsim.conf*

Find service_bind_address change, be SURE to uncomment!:

service_bind_address    192.168.56.1

Find dns_default_ip:

dns_default_ip    192.168.56.102

MORE TO COME



# WIP Tutorials

## Tor Routing Config

*sudo apt-get install tor*

*sudo nano /etc/tor/torrc*

Append to file:

*TransListenAddress 192.168.56.1
TransPort 9040
DNSListenAddress 192.168.56.1
DNSPort 5353*


## InetSim Config

Download latest Remnux VM: https://remnux.org/

**Set Static IP and DNS inside VM Preferences**

VM Machine Network Settings:
    * Adapter 1 nat
    * Adapter 2 hostonly, vboxnet0

VM Preferences:
    * Network adapter settings:
        * 192.168.56.1
    * Network DHCP:
        * Addr: 192.168.56.1
        * Mask: 255.255.255.0
        * Lower: 192.168.56.101
        * Upper: 192.168.56.254

**Setup Remnux network settings**

Inside Remnux:

*sudo nano /etc/network/interfaces*

Change:

    auto eth0
    iface eth0 inet dhcp

To:

    auto eth0
    iface eth0 inet static  
    address 192.168.56.102
    netmask 255.255.255.0
    network 192.168.56.0
    broadcast 192.168.56.255
    gateway 192.168.56.1
    dns-nameservers 8.8.8.8

*sudo service networking restart*

## Webserver Setup


*sudo apt-get install uwsgi uwsgi-plugin-python nginx*

**uwsgi**

*cuckoo web --uwsgi*

Copy the output of the command above

*sudo nano /etc/uwsgi/apps-available/cuckoo-web.ini*

Paste the output into this new file and save it

*sudo ln -s /etc/uwsgi/apps-available/cuckoo-web.ini /etc/uwsgi/apps-enabled/*

*sudo service uwsgi start cuckoo-web*

**nginx**

*cuckoo web --nginx*

Copy the output of the command above

*sudo nano /etc/nginx/sites-available/cuckoo-web*

Paste the output into this new file and save it

*sudo adduser www-data cuckoo*

*sudo ln -s /etc/nginx/sites-available/cuckoo-web /etc/nginx/sites-enabled/*


## Configure XRDP with xfce

*sudo apt-get install XRDP*

*sudo apt-get install xfce4*

*echo xfce4-session >~.xsession*

*sudo service xrdp restart*

## Install IDA Pro

*chmod +x IDAFILE.run*
*./IDAFILE.run*

## SSH into IDA Box
*sudo apt-get install openssh-server*
X11 forwarding built into ssh

## Hiding the VM

**Using github antivmdetection** This is not working well ATM
source: https://byte-atlas.blogspot.de/2017/02/hardening-vbox-win7x64.html

1) Download VolumeID (for x64)
2) Download DevManView (for x64)
3) # apt-get install acpidump (used by Python Script to fetch your system's parameters)
4) # apt-get install libcdio-utils (contains "cd-drive", used to read these params)
5) # apt-get install python-dmidecode (the pip-version of dmidecode is incompatible and useless for our purpose, so fetch the right one)
6) $ git clone https://github.com/nsmfoo/antivmdetection.git
7) $ cd antivmdetection :)
8) $ echo "some-username" > user.lst (with your desired in-VM username(s))
9) $ echo "some-computername" > computer.lst

**In VM Settings**
Windows 7 Enterprise
