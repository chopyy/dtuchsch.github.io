---
layout: post
title:  "TCP/IP Kommunikation zwischen einem Host- und Gast-OS mit VirtualBox"
date:   2016-08-02 21:10:00
categories: linux, vm, virtualbox, ros, ethernet
---

Der Datenaustausch soll zwischen dem Gast-OS Ubuntu Linux 14.04 und dem Host-OS Windows 7 über Ethernet TCP/IP oder UDP erfolgen. 

Mit den Standard-Netzwerkeinstellungen (NAT) von VirtualBox ist der Gast erreichbar unter der IP *10.0.2.15*, der Host unter der IP *10.02.2*.

Eine Verbindung ist ohne weitere Einstellungen nicht möglich:

```
ping 10.0.2.15
Ping-Statistik für 10.0.2.15:
    Pakete: Gesendet = 2, Empfangen = 0, Verloren = 2
    (100% Verlust),
```

Eine Kommunikation zwischen Host und Gast kann durch folgende Konfiguration hergestellt werden:

* NAT: Hier besitzt der Gast dieselbe IP wie der Host und die Dienste des Gasts sind über localhost zugänglich. Ein auf dem Gastsystem installierter Apacheserver ist unter 127.0.0.1:80 im Browser erreichbar. Damit sich Host und Gast sehen, müssen Ports weitergeleitet werden.
* Host-only: Dabei wird ein virtuelles Ethernet-Interface auf dem Hostrechner von VirtualBox eingerichtet.
* Bridge: Hierbei wird die Netzverbindung des Gasts direkt auf eine physikalisch vorhandene Ethernetschnittstelle überbrückt. Dem Gast wird dann vom anliegenden Netzwerk eine IP zugewiesen. Diese Variante kommt für mich persönlich nicht in Frage, weil ich zum Teil im Firmennetzwerk unterwegs bin. Somit kann bei Überbrückung dem Gast keine IP zugewiesen werden.

# Variante 1: NAT und Port-Forwarding

Die einfachste der drei Varianten stellt das Belassen der NAT-Netzwerkkonfiguration dar. Es müssen lediglich die gewünschten Ports freigeschaltet werden.

* Die Einstellungen des Gasts in VirtualBox über das Menü **Maschine -> Ändern** aufrufen.
* Den Reiter **Netzwerk** auswählen.
* **Angeschlossen an:** NAT
* Auf die Schaltfläche **Port-Weiterleitung** klicken und die gewünschten Ports freischalten.

Erst dann ist es möglich auf die Dienste des Gasts über die jeweiligen Ports zuzugreifen. Z.B. sind dann über Port 80 auf dem Host Webseiten im Browser (localhost:80) aufrufbar, die auf einem Apacheservers des Gasts laufen. Weitere Ports sind weiterleitbar, wie der folgende Screenshot zeigt:

![Port-Forwarding um Daten über TCP/IP und UDP/IP zwischen Host und Gast auszutauschen]({{ site.url }}/assets/Port-Forwarding.PNG)

# Variante 2: Host-only

## Host-only Interface einrichten

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

## Änderung des Netzwerk-Adapters

Im Anschluss muss die Netzwerkkonfiguration der virtuellen Maschine in VirtualBox angepasst werden:

* **Maschine -> Ändern...** und zum Reiter **Netzwerk** navigieren
* **Anschlossen an:** Host-only Adapter

![Netzwerkeinstellungen zu Host-only ändern]({{ site.url }}/assets/Netzwerkeinstellungen.PNG)

## Einrichtung einer statischen IP unter Ubuntu
Die Vergabe der IP Adresse erfolgt statisch und nicht dynamisch. 
Für die Einrichtung der statischen IP unter Ubuntu **Netzwerkverbindungen -> Verbindungen bearbeiten...** auswählen.

Im Anschluss auf **Bearbeiten** und folgende Einstellungen im Tab **IPv4-Einstellungen** vornehmen:

* **Methode:** Manuell
* **Adresse:** 192.168.100.2
* **Netzmaske:** 255.255.255.0

![Ubuntu statische IP anlegen]({{ site.url }}/assets/Ubuntu_IP.PNG)

## Test der Verbindung

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
