---
title: GPS Tracker mit Mobilfunk
subtitle: Mobilfunk und GPS mit dem ESP32
date: '2022-01-11'
categories:
  - content/data/categories/live.yaml
tags: []
excerpt: Mobilfunk und GPS mit dem ESP32
thumb_image_alt: Mobilfunk GPS
image_alt: Mobilfunk GPS
image_position: top
seo:
  title: GPS Tracker mit Mobilfunk
  description: Mobilfunk und GPS mit dem ESP32
  robots: []
  extra:
    - name: 'og:image'
      value: /images/DigitaleDinge11MobilfunkGPSTracker.png
      keyName: property
      relativeUrl: true
layout: post
thumb_image: /images/DigitaleDinge11MobilfunkGPSTracker.png
image: /images/DigitaleDinge11MobilfunkGPSTracker.png
author: content/data/team/rene-bohne.yaml
---
<iframe width="560" height="315"
src="https://www.youtube.com/embed/V_0rPJ9W3eg?modestbranding=1"
frameborder="0" allow="accelerometer; autoplay; encrypted-media;
gyroscope; picture-in-picture" allowfullscreen>\\\</iframe>

Diese Woche wird gehackt! Ich nehme ein TTGO T-Call Board, das neben einem ESP32 auch ein SIM800 Mobilfunkmodem hat, und verbinde es mit einem GPS-Modul aus einer sensebox. Auf dem Modul sitzt ein u-blox CAM-M8Q und den werden wir via I2C auslesen, mit TinyGPS parsen und die Daten dann an tingg.io senden. Nächste Woche machen wir das gleiche dann hoffentlich mit LoRaWAN.

Wir sind jeden Dienstag um 21 Uhr live uns sprechen über digitale Dinge! Über einen Kommentar unter dem Video freuen wir uns sehr und noch schöner ist es, wenn wir uns im Live-Chat auf YouTube und Discord austauschen können. Du findest uns bei https://discord.digitale-dinge.de/