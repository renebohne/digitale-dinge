---
title: Switchbot API
subtitle: Wir nutzen die REST API mit cURL und Javascript
date: '2021-11-09'
categories: []
tags: []
excerpt: lorem-ipsum
thumb_image_alt: lorem-ipsum
image_alt: lorem-ipsum
image_position: top
seo:
  title: ''
  description: ''
  robots: []
  extra: []
layout: post
thumb_image: /images/DigitaleDinge_2_Switchbot_API.png
image: /images/DigitaleDinge_2_Switchbot_API.png
---
<iframe width="560" height="315"
src="https://www.youtube.com/embed/A5F7fOnhNvg?modestbranding=1"
frameborder="0" allow="accelerometer; autoplay; encrypted-media;
gyroscope; picture-in-picture" allowfullscreen></iframe>

# Einleitung

Letzte Woche haben wir uns einen Überblick über das Switchbot Ökosystem verschafft. Nun wäre der nächste Schritt, die Firmware der Geräte zu verändern, aber da andere Mikrocontroller als erhofft zum Einsatz kommen, haben wir uns etwas anderes einfallen lassen: wir schauen uns die Switchbot REST API an, mit der man auf die Daten der Geräte dank Cloud von überall auf der Welt zugreifen kann. Da die Dokumentation sehr gut ist, haben wir hier ein super Beispiel, um zu lernen, wie man eine echte REST API benutzt. Um den Einstieg so einfach wie möglich zu halten, nutzen wir erst cURL in einem Terminal und nutzen im zweiten Schritt node.js - aber es gibt auch fertige Adapter für iobroker und alle anderen Smart Home Systeme. 

# Die Switchbot REST API

Zunächst müssen wir verstehen, was die Switchbot REST API überhaupt ist. Die Dokumentation befindet sich frei zugänglich auf https://github.com/OpenWonderLabs/SwitchBotAPI

Dort steht beschrieben, wie man an das "Open Token" gelangt, mit dem man Zugriff auf die Daten der Geräte erhält. Wer das Token hat, kann auf die Daten zugreifen, also sollte man es geheim halten! Und anders herum: wer das Token nicht hat, kann nicht auf die Daten meiner Geräte zugreifen. In Kombination mit der Verschlüsselung ist das ein recht guter Sicherheitsstandard.


# Beispiele mit cURL

Um alle Geräte aufzulisten, kann dieser cURL Befehl im Terminal verwendet werden:

curl -H "Authorization: MEINGEHEIMESTOKEN" https://api.switch-bot.com/v1.0/devices/

Natürlich muss MEINGEHEIMESTOKEN durch das tatsächliche Open Token ersetzt werden.

Um mehr Details über ein 


# Wie geht das mit node.js?

