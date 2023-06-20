---
title: Raspberry Pi Pico Displays
subtitle: Mit Arduino oder Pico-SDK
date: '2023-06-20'
categories:
  - content/data/categories/live.yaml
tags: []
excerpt: Mit Arduino oder Pico-SDK
thumb_image_alt: DIY Poolthermometer
image_alt: Raspberry Pi Pico Displays
image_position: top
seo:
  title: Raspberry Pi Pico Displays
  description: Mit Arduino oder Pico-SDK
  robots: []
  extra:
    - name: 'og:image'
      value: /images/DigitaleDinge60_PicoDisplays.png
      keyName: property
      relativeUrl: true
layout: post
thumb_image: /images/DigitaleDinge60_PicoDisplays.png
image: /images/DigitaleDinge60_PicoDisplays.png
author: content/data/team/rene-bohne.yaml
---
<iframe width="560" height="315"
src="https://www.youtube-nocookie.com/embed/kZM1ey6nx0g?modestbranding=1"
frameborder="0" allow="accelerometer; autoplay; encrypted-media;
gyroscope; picture-in-picture" allowfullscreen>\\\</iframe>

Wir gucken uns an, wie man mit dem Raspberry Pi Pico ein OLED Display oder ein LCD Display in Betrieb nimmt. Dafür verwenden wir einmal das Pico-SDK und einmal das Arduino Framework. 

Das geht natürlich mit jedem Board mit RP2040 Mikrocontroller. Du kannst das Arduino Beispiel sogar mit fast jedem Arduino-kompatiblem Boards nachbauen, inklusive ESP32.

Wir sind jeden Dienstag um 21 Uhr live uns sprechen über digitale Dinge! Über einen Kommentar unter dem Video freuen wir uns sehr und noch schöner ist es, wenn wir uns im Live-Chat auf YouTube und Discord austauschen können. Du findest uns bei http://discord.digitale-dinge.de/

# Vorbereitungen

Für Variante 1 musst Du das Pico-SDK auf Deinem Computer installiert haben.

Für Varianten 2 und 3 solltest Du Visual Studio Code installiert haben und die PlatformIO Extension aktiviert haben. 

# Variante 1: PICO-SDK

Für dieses Beispiel musst Du die vier Pins des Displays mit dem Raspberry Pi Pico wie folgt verbinden:

| OLED DISPLAY I2C | Raspberry Pi Pico GPIO Pin |
| ----------- | ----------- | 
| GND | GND | 
| VCC | VCC (3V3) | 
| SDA | GP2 | 
| SCL | GP3 | 

Den Source Code findest Du im unten verlinkten github Repository im Unterordner `oled_picosdk`. 
Damit das Beispiel funktioniert, musst Du das pico-sdk installiert haben!

# Variante 2: Arduino mit LovyanGFX

Du kannst wie in Variante 3 einen Raspberry Pi Pico verwenden, aber für die Variante 2 wollen wir ein XIAO RP2040 betrachten, da es viel kleiner ist und gut zu dem kleinen SSD1306 OLED Display passt. Folgende Pins müssen verbunden werden:

| OLED DISPLAY I2C | XIAO GPIO Pin |
| ----------- | ----------- | 
| GND | GND | 
| VCC | VCC (3V3) | 
| SDA | P6 | 
| SCL | P7 | 

Du benötigst Visual Studio Code und PlatformIO für dieses Beispiel!


# Variante 3: Arduino mit Adafruit GFX

In diesem Beispiel wollen wir nicht mehr das kleine SSD1306 OLED verwenden, sondern ein 1.13" LCD mit 240x135 Pixeln nehmen. Konkret verwende ich dieses Board: https://www.waveshare.com/pico-lcd-1.14.htm

Das Display verwendet den ST7789 Treiber. Bei Adafruit gibt es eine passende Arduino Library, die wir in diesem Beispiel verwenden. PlatformIO installiert sie automatisch für uns.

Das Display kann einfach auf den Raspberry Pi Pico gesteclt werden. Es kann dann der Beispielcode aus dem Unterordner `lcd_arduino_adafruitGFX` mit VisualStudio Code auf den Pico geladen werden.

# Links zur Sendung

* github Repo mit dem Beispielcode: https://github.com/renebohne/pico-displays
* XIAO RP2040 Wiki mit Pinout: https://wiki.seeedstudio.com/XIAO-RP2040/
* PlatformIO Raspberry Pi Pico Platform: https://github.com/maxgerhardt/platform-raspberrypi
* Arduino Framework für RP2040: https://github.com/earlephilhower/arduino-pico
* SSD1306 pico-sdk: https://github.com/daschr/pico-ssd1306
* LovyanGFX für Raspberry Pi Pico: https://github.com/stonedev-app/picoLGFX