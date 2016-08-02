---
layout: post
title:  "TCP/IP Kommunikation zwischen einem Host- und Gast-OS mit VirtualBox"
date:   2016-08-02 21:10:00
categories: linux, vm, virtualbox, ros, ethernet
---

Der Datenaustausch soll zwischen dem Gast-OS Ubuntu Linux 14.04 und dem Host-OS Windows 7 über Ethernet TCP/IP oder UDP erfolgen. 

Mit den Standard-Netzwerkeinstellungen (NAT) von VirtualBox ist der Gast erreichbar unter der IP *10.0.2.15*, der Host unter der IP *10.02.2*.
Eine Verbindung ist mit NAT nicht möglich:

```
ping 10.0.2.15
Ping-Statistik für 10.0.2.15:
    Pakete: Gesendet = 2, Empfangen = 0, Verloren = 2
    (100% Verlust),
```

# Host-only Kommunikation einrichten

Um eine Verbindung zwischen Host und Gast herzustellen, muss eine Host-only Kommunikation eingerichtet werden.

In VirtualBox in das Menü **Datei -> Einstellungen -> Netzwerk** navigieren und den Reiter **Host-only Netzwerke** auswählen.

![Host-only Netzwerk hinzufügen]({{ site.url }}/assets/Netzwerk.PNG)

In diesem Fenster ein neues Host-only Netzwerk hinzufügen und folgende Einstellungen vornehmen:

Im Tab **Adapter**:

* **IPv4-Adresse:** 192.168.100.1
* **IPv4-Netzmaske:** 255.255.255.0

![Adaptereinstellungen für das Host-only Netzwerk]({{ site.url }}/assets/Adapter.PNG)

Im Tab **DHCP Server** den Haken bei **Server aktivieren** entfernen. 

![DHCP deaktivieren]({{ site.url }}/assets/Adapter.PNG)

# Änderung des Netzwerk-Adapters

Im Anschluss muss die Netzwerkkonfiguration der virtuellen Maschine in VirtualBox angepasst werden:

* **Maschine -> Ändern...** und zum Reiter **Netzwerk** navigieren
* **Anschlossen an:** Host-only Adapter

![Netzwerkeinstellungen zu Host-only ändern]({{ site.url }}/assets/Netzwerkeinstellungen.PNG)

# Einrichtung einer statischen IP unter Ubuntu
Die Vergabe der IP Adresse erfolgt statisch und nicht dynamisch. 
Für die Einrichtung der statischen IP unter Ubuntu **Netzwerkverbindungen -> Verbindungen bearbeiten...** auswählen.

Im Anschluss auf **Bearbeiten** und folgende Einstellungen im Tab **IPv4-Einstellungen** vornehmen:

* **Methode:** Manuell
* **Adresse:** 192.168.100.2
* **Netzmaske:** 255.255.255.0

![Ubuntu statische IP anlegen]({{ site.url }}/assets/Ubuntu_IP.PNG)

# Test der Verbindung

Ein Ping vom Host aus an den Gast ist dann erfolgreich:

```
Ping wird ausgeführt für 192.168.100.2 mit 32 Bytes Daten:
Antwort von 192.168.100.2: Bytes=32 Zeit<1ms TTL=64
Antwort von 192.168.100.2: Bytes=32 Zeit<1ms TTL=64
Antwort von 192.168.100.2: Bytes=32 Zeit<1ms TTL=64
Antwort von 192.168.100.2: Bytes=32 Zeit<1ms TTL=64

Ping-Statistik für 192.168.100.2:
    Pakete: Gesendet = 4, Empfangen = 4, Verloren = 0
    (0% Verlust),
Ca. Zeitangaben in Millisek.:
    Minimum = 0ms, Maximum = 0ms, Mittelwert = 0ms
```

Ein Ping vom Gast zum Host sollte ebenfalls erfolgreich sein. Jetzt können Anwendungen wie z.B. ROS-Knoten Daten über TCP/IP an den Host versenden.
