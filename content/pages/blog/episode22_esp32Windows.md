---
title: Windows auf dem ESP32
subtitle: FabGL emuliert 8086 auf dem ESP32
date: '2022-04-05'
categories:
  - content/data/categories/live.yaml
tags: []
excerpt: FabGL emuliert 8086 auf dem ESP32
thumb_image_alt: Windows auf dem ESP32
image_alt: Windows auf dem ESP32
image_position: top
seo:
  title: Windows auf dem ESP32
  description: FabGL emuliert 8086 auf dem ESP32
  robots: []
  extra:
    - name: 'og:image'
      value: /images/DigitaleDinge22ESP32windows.png
      keyName: property
      relativeUrl: true
layout: post
thumb_image: /images/DigitaleDinge22ESP32windows.png
image: /images/DigitaleDinge22ESP32windows.png
author: content/data/team/rene-bohne.yaml
---
<iframe width="560" height="315"
src="https://www.youtube-nocookie.com/embed/D1EywsAzKfU?modestbranding=1"
frameborder="0" allow="accelerometer; autoplay; encrypted-media;
gyroscope; picture-in-picture" allowfullscreen>\\\</iframe>

Heute finden wir heraus, ob man Microsoft Windows auf einem ESP32 installieren kann. Um die Sache so einfach wie möglich zu gestalten, verwenden wir das TTGO VGA32, aber man kann auch fast jedes andere ESP32 Board nehmen. Möglich macht es die FabGL Arduino Library.

In der Sendung haben wir FabGL in der Version 1.0.6 verwendet. Damit das klappt, musste die Version 1.0.6 des esp32 Arduino Kerns im Boardverwalter der Arduino IDE eingestellt werden. Neuere Versionen haben wahrscheinlich andere Ansprüche.

Wir haben uns auch FreeDOS, GEOS und ELKS Linux auf dem ESP32 mit dem PCEmulator Arduino Sketch angeguckt. Wer einen Altair8800 oder einen VIC20 mit MOS 6502 CPU emulieren möchte, findet in dem Projekt die entsprechenden Beispiele.

Zum Flashen des PCEmulators ist folgendes zu beachten:

* ESP32 Dev Module auswählen
* Parition Scheme auf "Huge App" einstellen
* PSRAM "Disabled" auswählen

Wir sind jeden Dienstag um 21 Uhr live uns sprechen über digitale Dinge! Über einen Kommentar unter dem Video freuen wir uns sehr und noch schöner ist es, wenn wir uns im Live-Chat auf YouTube und Discord austauschen können. Du findest uns bei https://discord.digitale-dinge.de/


# Links zur Sendung

* [FabGL](https://github.com/fdivitto/FabGL)

