---
title: GPS-Tracking mit traccar
subtitle: Geräte kostenlos und sicher tracken mit Open-Source Software
date: '2022-01-04'
categories:
  - content/data/categories/live.yaml
tags: []
excerpt: >-
  traccar ist eine open source GPS-Tracking Lösung. Wir zeigen, wie man traccar
  lokal installiert, für Geräte im Internet zugänglich macht, Daten an traccar
  sendet und wie man es in iobroker integriert.
thumb_image_alt: traccar
image_alt: traccar
image_position: top
seo:
  title: GPS-Tracking mit traccar
  description: traccar ist eine open source GPS-Tracking Lösung. Wir zeigen, wie man traccar lokal installiert und verwendet.
  robots: []
  extra:
    - name: 'og:image'
      value: /images/DigitaleDinge10traccar.png
      keyName: property
      relativeUrl: true
layout: post
thumb_image: /images/DigitaleDinge10traccar.png
image: /images/DigitaleDinge10traccar.png
author: content/data/team/rene-bohne.yaml
---
<iframe width="560" height="315"
src="https://www.youtube-nocookie.com/embed/W2CZtm3aU2M?modestbranding=1"
frameborder="0" allow="accelerometer; autoplay; encrypted-media;
gyroscope; picture-in-picture" allowfullscreen>\\\</iframe>


traccar ist eine open source GPS-Tracking Lösung. Wir zeigen, wie man traccar lokal installiert, für Geräte im Internet zugänglich macht, Daten an traccar sendet und wie man es in iobroker integriert.

# Installation von Traccar

LXC Container kreieren Ubuntu 20.4 Template, 1 Core, 1024 MB RAM, 8GB HDD, DHCP

```
apt update && apt upgrade -y
apt install unzip
wget https://www.traccar.org/download/traccar-linux-64-latest.zip
unzip traccar-linux-64-latest.zip
chmod +x traccar.run
./traccar.run
service traccar start
ip a
http://IP_Adresse:8082/
```

Damit ist Traccar Server lauffähig.

# 4 Dinge die man wissen sollte auf der Konsole machen zu können

1. Behebung durch Einrichtung Accurate Filter:

```
nano /opt/traccar/conf/traccar.xml
(unter     <entry key='database.password'></entry> einfügen)

<entry key='filter.enable'>true</entry>
<entry key='filter.maxSpeed'>25000</entry>
<entry key='filter.accuracy'>500</entry>
```

2. Traccar V4.15 hat bereits die Funktion Reverse Geocoding in Kooperation mit 
locationIQ integriert mit 1000 Abfragen/Tag frei. Bei eigenem (kostenlosem) Account 5000/Tag
https://locationiq.com/register Token holen. Hierzu den Access Token austauschen in:

```
sudo nano /opt/traccar/conf/default.xml

    <entry key='geocoder.key'>pk.b19bxxxxxxxxxxxxxxxxxxxxxxxxx</entry>
```

gegen eigenen Token tauschen.

3. Server Neustart (nach jeder Konfig Änderung nötig)
```
systemctl restart traccar.service
```

4. Log beobachten
```
tail -f /opt/traccar/logs/tracker-server.log
```

# Erste Schritte auf der Weboberfläche

Umstellung kn auf kmh: Zahnrad (oben rechts) Server, Eigenschaften (unten links) Name (auf Pfeil zum Auswählen) Geschwindigkeitseinheit Wert (auswählen) km/h

Auf das linke Zahnrad klicken und Device hinzufügen
(Beispiel Handy APP) 
Device Name und ID frei wählbar

Client (hier Mobiltelefon)
Für Android und Ios sind Traccar Clients verfügbar
Einzutragen ist die DynDNS-Adresse (z.B. myFritz), oder IP Adresse des eigenen Routers
Empfohlene Werte zeit 3600 sec., Entfernung 100m, Winkel 30 Grad
Bei Ios sollte eingestellt sein, dass immer gesendet wird (Einstellungen, Datenschutz, Ortungsdienste, Traccar Immer)
Bei Android Akku Einstellungen von automatisch auf manuell, dann Traccar Client immer erlauben

# Router Einstellungen

Am Router muss (für Mobiltelefone) der Port 5055 zum Traccar Server geöffnet und weitergeleitet werden.

Damit ist der Traccar Server einsatzbereit, die Positionen sollten auf der Karte dargestellt werden.
Der nächste Schritt wäre die einrichtung von GeoFences und deren Aktivierung in den Device-Einstellungen. 


# Verbindung zu externen Systemen

Nützliche API Aufrufe:

```
/api/geofences?deviceId=1
/api/devices/1?all=true
/api/devices/2?all=true
/api/positions?deviceId=1
/api/positions?deviceId=2
```

https://www.traccar.org/api-reference


# Verbindung von Traccar zu Iobroker

Es gibt einen Iobroker Traccar-Adapter, der die elementaren Daten in Iobroker holt.

Ich empfehle die Nutzung dieser Blockly-Script-Lösung, da der Funktionsumfang erweitert wird.
(Zeiterfassung, Anwensheitskontrolle, konfigurierbare Info-Zusendung, LoraWAN Unterstützung uvm.)

Nach Import des Blockly-Scripts (für jedes Device 1x) muss oben die DeviceID, die Traccar_IP:Port, sowie
der Benutzer:Passwort  eingetragen werden.
(Meist deviceID 1 für das erste Device und 2 für das zweite usw.) Nach dem Speichern, werden alle erforderlichen 
Datenpunkte unter 0_userdata.0.Traccar2 angelegt. Beim ersten speichern des Blocklys werden Warn-Meldungen
ausgegeben, einfach noch einmal speichern, Fertig. 
Im Ordner 0_userdata.0.Traccar2.Konfiguration können optional die API-Keys für 3Words und 
OpenDataCage hinterlegt werden. Dann werden die DP unter 
0_userdata.0.Traccar2.X.3Words und 
0_userdata.0.Traccar2.1.adress auch gefüllt.

Im Ordner 0_userdata.0.Traccar2.1.Konfiguration kann (optinal) das erweiterte Info Senden/Abruf und 
erweiterte Infos für die Ordner Zuhause und Arbeit eingestellt werden.

Erweiterte Infos für das (Sende-) Device finden sich unter
0_userdata.0.Traccar2.1.DeviceInfo.LoraWan

Die restlichen DP sollten selbsterklärend sein.

# Links

Hier der Link zum Import des Blockly-Scripts:

https://paste.hafenmeister.de/?ee76cff0ccc5b08c#DpEgHRh7MXe73kvxH1bcUDFTLqJEbzSDHsYKEkXuLfpF

