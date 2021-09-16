
# Raspi erste Einrichtung

Image erstellen


Datei SSH in boot directory

Beschreibung hinzufügen 

Datei mit namen "beschreibung.txt" in boot schreiben - zwecks Wiedererkennung falls Image erkannt oder SD Card in Notbook gelesen werden soll
Password ändern

    sudo raspi-config

Statische IP adresse
<https://www.raspberrypi.org/documentation/configuration/tcpip/>

    sudo nano /etc/dhcpcd.conf
    

System aktualisieren

    sudo apt-get update && sudo apt-get upgrade

Basis software

    sudo apt-get install git
    
# RPI-Monitor
<https://xavierberger.github.io/RPi-Monitor-docs/11_installation.html>

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


## rpi-clone - imagingin auf SD karte


    git clone https://github.com/billw2/rpi-clone.git 
    cd rpi-clone
    sudo cp rpi-clone rpi-clone-setup /usr/local/sbin

testen

    sudo rpi-clone-setup -t testhostname

Typical two partition clones - SD card or USB disk to USB disk:
    
    rpi-clone sda


<https://github.com/billw2/rpi-clone>


## r-backup - pi ordener sichern

    nano r-backup.sh

Input: 
    #!/bin/bash
    rsync -Pav  /home/pi /mnt/backup/backup/PhotonPi-55
    
backup pfad anpassen anpassen 
   
    chmod +x r-backup.sh
    
kopieren nach root:
    sudo cp r-backup.sh /root/
    

eintragung in root crontab
    sudo crontab -e
    
eitragen
    0 1 * * * /root/r-backup.sh
    
    
    
## Zero ethernet

<https://tutorials-raspberrypi.de/raspberry-pi-zero-ethernet-verbindung-herstellen-enc28j60/>

    
