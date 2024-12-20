
## Raspi erste Einrichtung

Image erstellen


### Datei SSH in boot directory

Beschreibung hinzufügen 

Datei mit namen "beschreibung<IP>.txt" in boot schreiben - zwecks Wiedererkennung falls Image erkannt oder SD Card in Notbook gelesen werden soll
Password ändern

    sudo raspi-config

### Statische IP adresse
<https://www.raspberrypi.org/documentation/configuration/tcpip/>

    sudo nano /etc/dhcpcd.conf

Neu mit 5:

    nmtui

auf cli ausfuehren
    

### System aktualisieren

    sudo apt-get update && sudo apt-get upgrade

Basis software

    sudo apt-get install git
    
# RPI-Monitor
<https://xavierberger.github.io/RPi-Monitor-docs/11_installation.html>

## !! Ab Bookworm oder so manuelle installation:
<https://xavierberger.github.io/RPi-Monitor-docs/12_custom_installation.html>
Fix: <https://github.com/XavierBerger/RPi-Monitor/issues/412>


## evtl besser:

    sudo apt-key del 2C0D3C0F
    gpg --keyserver keyserver.ubuntu.com --recv-keys E4E362DE2C0D3C0F
    gpg --export E4E362DE2C0D3C0F | sudo gpg --dearmor -o /usr/share/keyrings/rpimonitor.gpg
    echo "deb [signed-by=/usr/share/keyrings/rpimonitor.gpg] http://giteduberger.fr rpimonitor/" | sudo tee /etc/apt/sources.list.d/rpimonitor.list > /dev/null


restart rpi-monitor

    sudo  service rpimonitor restart
    


Rpi-monitor work around

    sudo nano /etc/init.d/rpimonitor

Add the instruction "sleep 10" immediately before the "start" instruction in the case structure at tne end of the program. This will be immediately after line 93 in the current release. After editing, the section should look like this:

    case "$1" in
        start)
            sleep 10
            start
            
Save the edited file and RPi-monitor should start without a problem on reboots and restarts.
	
<https://github.com/XavierBerger/RPi-Monitor/issues/287>



Edit network.conf

    sudo nano /etc/rpimonitor/template/network.conf


Edit version.conf

    sudo nano /etc/rpimonitor/template/version.conf

add

    static.5.name=model
    static.5.source=/proc/device-tree/model
    static.5.regexp=(.*)
    static.5.postprocess=

    web.status.1.content.1.line.6='Model: <b>' + data.model + '</b>'

ODER:

    https://github.com/XavierBerger/RPi-Monitor/issues/273

```
########################################################################
# Extract System information
#  Page: 1
#  Information               Status     Statistics
#  - distribution name       - yes      - no
#  - kernel version          - yes      - no
#  - firmware version        - yes      - no
#  - processor model         - yes      - no
#  - num of pkg upgradable   - yes      - no
#  - list of pkg upgradable  - yes      - no
########################################################################
static.1.name=distribution
static.1.source=/etc/os-release
static.1.regexp=PRETTY_NAME.\"(.*)\"
static.1.postprocess=

static.2.name=kernel_version
static.2.source=uname -msr
static.2.regexp=(.*)
static.2.postprocess=

static.3.name=firmware
static.3.source=/proc/version
static.3.regexp=(#\d+)
static.3.postprocess=

static.4.name=processor
static.4.source=pinout -m
static.4.regexp=(?:SoC)\s+: (.*)
static.4.postprocess=

static.5.name=revision
static.5.source=/proc/cpuinfo
static.5.regexp=(?:Revision)\s+: (.*)
static.5.postprocess=

static.6.name=model
static.6.source=/proc/cpuinfo
static.6.regexp=(?:Model)\s+: (.*)
static.6.postprocess=

static.7.name=memory
static.7.source=pinout -m
static.7.regexp=(?:RAM)\s+: (.*)
static.7.postprocess=

dynamic.1.name=upgrade
dynamic.1.source=/var/lib/rpimonitor/updatestatus.txt
dynamic.1.regexp=(\d+ upgradable\(s\)|.*&nbsp;.*)
dynamic.1.postprocess=
dynamic.1.rrd=

dynamic.2.name=packages
dynamic.2.source=/var/lib/rpimonitor/updatestatus.txt
dynamic.2.regexp=^\s+(.*)\s+\d+ upgradable
dynamic.2.postprocess=
dynamic.2.rrd=

web.status.1.content.1.name=Version
web.status.1.content.1.icon=version.png
web.status.1.content.1.line.1='Model: <b>' + data.model + ' (<i>' + data.revision + ')</i></b>'
web.status.1.content.1.line.2='Processor: <b>' + data.processor + '</b>   Memory : <b>' + data.memory + '</b>'
web.status.1.content.1.line.3='Distribution: <b>'+ data.distribution + '</b>'
web.status.1.content.1.line.4='Kernel version: <b>' + data.kernel_version + ' (<i>' + data.firmware + ')</i></b>'
web.status.1.content.1.line.5='Package(s): <b>' + data.upgrade + '</b>' + ShowInfo('packages','Package(s) upgradable(s)',data.packages)
```

    

Edit SDcard.conf

    sudo nano /etc/rpimonitor/template/sdcard.conf


change 
    /boot

to
    /boot/firmware

(4 times)





# Addons:

<https://xavierberger.github.io/RPi-Monitor-docs/25_addons.html>

    sudo nano /etc/rpimonitor/data.conf

about auskommentieren

einfügen:



    web.addons.1.name=Top3
    web.addons.1.addons=top3
    
    web.addons.2.name=ShelleInABox
    web.addons.2.addons=custom
    web.addons.2.showtitle=false
    web.addons.2.url=https://192.168.1.69:4200/
    web.addons.2.allowupdate=false
    # Achtung IP einsetzen. / läuft auf Iphone erst nach direkter eingabe in die URL Zeile
    web.addons.2.allowupdate=false

## shellinabox

    sudo apt-get install shellinabox

### top3

    sudo cp /usr/share/rpimonitor/web/addons/top3/top3.cron /etc/cron.d/top3



------------------------------------------------

## Pip installieren (lite)

    sudo apt install python3-pip
    
<https://www.raspberrypi.org/documentation/linux/software/python.md>

## mqtt python client 

    pip install paho-mqtt
    

## smb installieren

    sudo apt-get update
    sudo apt-get install samba samba-common smbclient
    
    
    
    
Dann prüfen wir, ob Samba läuft.

     sudo service smbd status
     
Userpwd anlegen

     sudo smbpasswd -a pi
     
     
<https://www.elektronik-kompendium.de/sites/raspberry-pi/2007071.htm>


## rpi-clone - Image auf SD karte


    git clone https://github.com/billw2/rpi-clone.git 
    cd rpi-clone
    sudo cp rpi-clone rpi-clone-setup /usr/local/sbin

testen

    sudo rpi-clone-setup -t testhostname

Typical two partition clones - SD card or USB disk to USB disk:
    
    rpi-clone sda
	
	
	
Wann wurde das letzte image gemacht? ("cat" statt "tail -1" listet alle)
    lastclone.sh anlegen:
	
    #!/bin/bash
    tail -1 /var/log/rpi-clone.log 


<https://github.com/billw2/rpi-clone>


## r-backup - pi Ordner sichern

    nano r-backup.sh

Input: 
    #!/bin/bash
    rsync -Pav  /home/pi /mnt/backup/backup/<NAME>
    
backup pfad anpassen anpassen 
   
    chmod +x r-backup.sh
    
kopieren nach root:
    sudo cp r-backup.sh /root/
    

eintragung in root crontab
    sudo crontab -e
    
eintragen
    0 1 * * * /root/r-backup.sh
    

## NAS Laufwerke mappen
	
    sudo nano /etc/fstab	
	
    //192.168.1.5/web  /mnt/nas  cifs  username=pi,password=XXXXXXX,comment=systemd.automount  0  0
    //192.168.1.5/backup /mnt/backup  cifs  username=admin,password=XXXXXX,comment=systemd.automount  0  0

Pfade erstellen und freigeben
    
    sudo mkdir /mnt/nas
    sudo mkdir /mnt/backup
    sudo chmod 777 /mnt/nas
    sudo chmod 777 /mnt/backup
    sudo mount -a
	
# NFS Verbindung mit Synology
	
	
	<https://jsx.red/synology-nas-mit-dem-raspberry-verbinden/>
	
Update r-backup.sh:
	
	sudo rsync -Pav --no-o --no-g  /home/pi /mnt/pibackup/strompi-51
	
fstab ändern:

	192.168.1.XXX:/volume1/pi /mnt/pibackup nfs defaults,rw,users  0  0
	

	
	
	
## Zero ethernet

<https://tutorials-raspberrypi.de/raspberry-pi-zero-ethernet-verbindung-herstellen-enc28j60/>

    
