
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
    
RPI-Monitor
<https://xavierberger.github.io/RPi-Monitor-docs/11_installation.html>

Rpi-monitor work around

sudo nano /etc/init.d/rpimonitor

To add the delay to the startup script, edit rpimonitor (sudo nano /etc/init.d/rpimonitor). Add the instruction "sleep 10" immediately before the "start" instruction in the case structure at tne end of the program. This will be immediately after line 93 in the current release. After editing, the section should look like this:
    case "$1" in
        start)
            sleep 10
            start
		
	
<https://github.com/XavierBerger/RPi-Monitor/issues/287>
