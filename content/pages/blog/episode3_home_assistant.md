---
title: 'Home Assistant: Erfahrungen'
subtitle: >-
  Fabian ist von FHEM auf Home Assistant umgestiegen und teilt seine Erfahrungen
  mit uns.
date: '2021-11-16'
categories:
  - content/data/categories/live.yaml
tags: []
excerpt: >-
  Home Assistant ist ein sehr modernes System mit einer großen Community. Man
  kann damit fast alles automatisieren und es gibt sogar eine eigene App. Wer
  neu im Thema Smart Home ist, sollte sich Home Assistant und iobroker unbedingt
  angucken. Eine einfache Installation auf einem Raspberry Pi oder in einer
  virtuellen Maschine ist schnell erledigt und reicht sicher aus, um einen
  ersten Eindruck zu gewinnen.
thumb_image_alt: Home Assistant
image_alt: Home Assistant
image_position: top
seo:
  title: 'Home Assistant: Erfahrungen'
  description: >-
    Home Assistant ist ein sehr modernes System mit einer großen Community. Man
    kann damit fast alles automatisieren und es gibt sogar eine eigene App. 
  robots: []
  extra:
    - name: 'og:image'
      value: /images/Copy of Digitale Dinge 3 Home Assistant.png
      keyName: property
      relativeUrl: true
layout: post
thumb_image: /images/Copy of Digitale Dinge 3 Home Assistant.png
image: /images/Copy of Digitale Dinge 3 Home Assistant.png
author: content/data/team/rene-bohne.yaml
---
Wer sein zuhause zu einem Smart Home machen möchte, braucht neben den vielen Geräten auch Software, welche die Intelligenz bereitstellt. In der Vergangenheit haben wir (bei der Online Lab Night) uns FHEM angeguckt und intensiv mit iobroker beschäftigt. Eine weitere beliebte Software ist Home Assistant (HA). HA kann hier kostenlos heruntergeladen werden: <https://www.home-assistant.io/>

Fabian hat vor einigen Wochen den Wechsel von FHEM zu Home Assistant vollzogen und berichtet von seinen Erfahrungen in unserer Live-Sendung:

<iframe width="560" height="315"
src="https://www.youtube-nocookie.com/embed/FqbyLXywrOk?modestbranding=1"
frameborder="0" allow="accelerometer; autoplay; encrypted-media;
gyroscope; picture-in-picture" allowfullscreen></iframe>

# Installation  

Die Installation ist genauso einfach wie bei anderen modernen Projekten (wie z.B. iobroker). Auf dieser Seite gibt es alle Informationen:  <https://www.home-assistant.io/installation/>

Prinzipiell kann man Home Assistant zu einem bestehenden Betriebssystem hinzufügen, ein spezielles Betriebssystem Image installieren (z.B. für den Raspberry Pi) oder man installiert ein Docker Image. Sehr beliebt ist auch die Installation in einer virtuellen Maschine wie z.B. Proxmox.

Nach der Installation muss das System konfiguriert werden. Das ist alles selbsterklärend: in den entsprechenden Dialogen wird man aufgefordert, einen Benutzer mit Passwort anzulegen, ein paar Informationen über den Standort einzutragen und dann kann es auch schon los gehen mit der Konfiguration von „Integrations“ (Plugins): Über 1800 Integrationen stehen zur Verfügung. Eine komplette Liste befindet sich hier:  <https://www.home-assistant.io/integrations/>

Im Live-Chat haben wir herausgefunden, dass diese große Anzahl an Integrations einer der wichtigsten Gründe ist, warum User sich für HA entscheiden. Für fast jedes Gerät gibt es bereits eine Integration und die Installation benötigt nicht viel mehr als einen Mausklick.

# Home Assistant Companion App

Viele Integrationen hat iobroker ebenfalls, aber ein weiteres wichtiges Feature von HA fehlt dem iobroker: eine eigene App! Mit der Home Assistant Companion App hat man nicht nur eine umfangreiche App, um alles im Haus zu steuern, sondern man kann sogar Shortcuts in das Menü des Smartphone-Betriebssystems ergänzen. Durch einfaches Klicken kann so sehr schnell ein Licht an/aus geschaltet werden. Hier gibt es mehr Infos zur App: <https://companion.home-assistant.io/>

# Fazit

Home Assistant ist ein sehr modernes System mit einer großen Community. Man kann damit fast alles automatisieren und es gibt sogar eine eigene App. Wer neu im Thema Smart Home ist, sollte sich Home Assistant und iobroker unbedingt angucken. Eine einfache Installation auf einem Raspberry Pi oder in einer virtuellen Maschine ist schnell erledigt und reicht sicher aus, um einen ersten Eindruck zu gewinnen. Wir bedanken uns bei Fabian für die Einblicke in sein System und freuen uns auf Fragen und Feedback in unserem Discord: <http://discord.digitale-dinge.de>
