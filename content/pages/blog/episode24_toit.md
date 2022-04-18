---
title: toit Grundlagen
subtitle: IoT Plattform und Programmiersprache
date: '2022-04-19'
categories:
  - content/data/categories/live.yaml
tags: []
excerpt: IoT Plattform und Programmiersprache
thumb_image_alt: toit
image_alt: toit
image_position: top
seo:
  title: toit Grundlagen
  description: IoT Plattform und Programmiersprache
  robots: []
  extra:
    - name: 'og:image'
      value: /images/DigitaleDinge24toitGrundlagen.png
      keyName: property
      relativeUrl: true
layout: post
thumb_image: /images/DigitaleDinge24toitGrundlagen.png
image: /images/DigitaleDinge24toitGrundlagen.png
author: content/data/team/rene-bohne.yaml
---
<iframe width="560" height="315"
src="https://www.youtube.com/embed/koCoiTrq82I?modestbranding=1"
frameborder="0" allow="accelerometer; autoplay; encrypted-media;
gyroscope; picture-in-picture" allowfullscreen>\\\</iframe>

toit ist eine IoT Plattform und eine Open Source Programmiersprache. Das ist auf den ersten Blick etwas verwirrend, da man beide Projekte im Kopf trennen muss obwohl sie vom selben Team erschaffen wurden.

In dieser Sendung schauen wir uns beide Projekte an, installieren toit und jag und lernen die Grundlagen der Programmiersprache. In den nächsten Wochen werden wir uns dann die IoT Plattform in Detail angucken und auch andere IoT Plattformen mit MQTT und HTTP verwenden.

# toit IoT Plattform

Bei der toit IoT Plattform handelt es sich um eine kommerzielle Plattform, mit der man IoT Geräte verwalten und Daten verarbeiten kann. Es ist sogar möglich, neue Firmware direkt im Webbrowser zu schreiben und auf ausgewählte Geräte über das Internet zu verteilen.

https://docs.toit.io/getstarted/installation

Wir schauen uns diese Woche nur kurz an, wie man ein neues Gerät mit der Web Plattform in Betrieb nimmt. Die Details der Plattform sehen wir uns in einer zukünftigen Folge an. Dann wird es auch um PubSub gehen und die REST API, womit man die Daten in eigenen Anwendungen nutzen kann.

Diese Woche wollen wir uns auf die Programmiersprache und die Nutzung ohne Cloud konzentrieren.

# toit Open Source Programmiersprache

Aktuell kann man die toit Programmiersprache nur mit einem ESP32 verwenden und sie ist für diesen Mikrocontroller und das Internet der Dinge optimiert.

Das besondere an toit ist, dass jedes Programm in einem Container - also einem Sandkasten - läuft und somit vom Kernel und von anderen Programmen isoliert ist. Wenn ein Programm abstürzt, bleibt nicht das gesamte System hängen. 

Die moderne Programmiersprache kennt Objekte und Klassen und hat einen optimierten Garbagecollector für embedded systems. Neben command-line-interface (CLI) Werkzeugen gibt es auch eine Extension für Visucal Studio Code.

Der Quellcode befindet sich in diesem github Repository: https://github.com/toitlang/toit

Es ist wichtig zu verstehen, dass die Versionen der open source Programmiersprache sich unterscheiden von den Versionen, die man über toit.io installiert. Die Version von toit.io beinhaltet zusätzliche Bibliotheken, um mit der Plattform zu kommunizieren und dafür fehlen andere Elemente. Aktuell nutzt die toit.io Version die Release 1.6.9 und die toitlang Version aus github die Release 1.6.10 - Die Version 1.6.9 ist allerdings nicht in github zu finden, da sie scheinbar eine unterschiedliche Codebase verwendet. Die kommerzielle Version von toit.io verwendet folgende Standardbibliothek: https://libs.toit.io/

Der Syntax von toit ähnelt Python. Es wird kein Semikolon verwendet und anstelle von geschweiften Klammern rückt man seinen Quellcode ein.

## Jaguar

Bevor wir uns die Programmiersprache genauer angucken, müssen wir das jag Tool installieren, das unsere Programme kompiliert und auf den Mikrocontroller flasht. Alle Details inkl. Installationsanleitung findet man hier (und in unserem Video):
https://github.com/toitlang/jaguar

## Funktionsdefinitionen

Funktionen haben mindestens einen Funktionsnamen, auf den ein Doppelpunkt folgt. In den darauf folgenden, eingerückten Zeilen steht der Funktionskörper. Wenn eine Funktion Parameter verwendet, stehen diese vor dem Doppelpunkt. Um eine Funktion mit Parametern aufzurufen, benutzt man keine runden Klammern, sondern trent die Parameter nur durch Leerzeichen vom Funktionsnamen.

```toit
hi:
  print "Hello World!"

hello name:
  print "Hello $name!"

main: 
  hi
  hello "World"
```

## Variablen und Konditionen

toit kennt Variablen, die mit dem Operator := definiert werden. Konstanten werden mit dem Operator ::= definiert.

Wie jede objektorientierte Programmiersprache kennt auch toit if-Statements.

```toit
main:
  c ::= 1
  x := 0
  if x != c:
    x = 42
  
  y := ?
  if (random 2) == 0:
    y = 1
  else:
    y = 42
```

## Typen

Auch primitive Typen wie int sind Klassen in toit. Sie haben also auch Methoden, wie das folgende Beispiel zeigt.

```toit
//gibt den absoluten Wert von x zurück
foo x/int -> int:
  return x.abs
```

Sogar Zahlen können als Objekte betrachtet werden und sie haben z.B. die Funktion repeat.

```toit
main:
  10.repeat:
    print "."
```

Steht hinter einer Variablen oder eine Funktionsdefinition ein Slash (/), so kann der Typ der Variablen definiert werden. Der Rückgabewert einer Funktion steht nach dem -> Operator.

## Blöcke

Blöcke werden sehr häufig in toit verwendet. Es sind Callbacks zu Funktionen. Hier zwei wichtige Beispiele:

```toit
main:
  // Gibt die Zahlen 1 bis 10 aus, eine pro Zeile.
  10.repeat:
    print it + 1

  // Gibt die Elemente der Liste aus, eins pro Zeile.
  ["digitale", "dinge", "dienstags", "21 Uhr"].do:
    print it
```

Das Blockelement "it" wird als Variable genutzt, das den aktuellen Wert des zu iterierenden Elements beinhaltet.
Man kann das Blockelement aber auch benennen oder mehrere verwenden, wie das folgende Beispiel zeigt:

```toit
main:
  map ::= {
    1234: "Peter",
    5678: "Paul",
    9: "Maria"
    }
  map.do | id name |
    print "$name hat die ID $id"
```

Mehr über Blöcke erfahrt ihr hier: https://docs.toit.io/language/blocks


## Klassen und Objekte

```toit
class Point: 
  x := 42
  y/int := 103
  static ZERO/int ::= 0

main:
  //erzeugt eine neue Instanz der Klasse Point
  p := Point
  //gibt den Wert der Klassenvariablen x (42) aus. 
  print p.x
  //gibt den Wert der Klassenvariablen y (103) aus. 
  print p.y
```

Private Klassenvariablen nutzen einen Underscore "_" am Ende des Variablennamens. Es bietet sich an, in diesem Fall auf Getter und Setter zurückzugreifen, wie man sie auch aus anderen objektorientierten Programmiersprachen kennt:

```toit
class Point:
  x_ /int := ? // Privates Feld.
  y/int := 0 //public Field.

  constructor .x_:

  // Getter, returnt ein int.
  x -> int:
    return x_

  // Setter, nimmt ein int, returnt nichts.
  x= value/int -> none:
    x_ = value
    print "x_ hat nun den Wert $x_"
```

## Vererbung

Eine Klasse kann mit dem Schlüsselwort extends erweitert werden.

```toit
class Point:
  x /int := 0
  y /int := 0

class Point3D extends Point:
  z /int := 0

main:
  p = Point3D
  p.x = 42   
  p.y = 13
  p.z = 103  
```

Natürlich gibt es auch Interfaces und Super-Konstruktoren und vieles mehr. Details stehen in der toit Language Dokumentation in diesem Kapitel:
https://docs.toit.io/language/objects-constructors-inheritance-interfaces

## Der Paketmanager

Es gibt mindestens zwei Registries, die toit Pakete anbieten und man kann dem Paketmanager auch direkt eine URL (z.B. zu einem github Repository) übergeben.

Wenn man die toit CLI verwendet (von der toit IoT Plattform), kann man ein Paket wie folgt installieren:
```
toit pkg install github.com/toitware/toit-morse
```

Wer hingegen Jaguar, also das jag cli Tool verwendet, ruft diesen Befehl auf:
```
jag pkg install github.com/toitware/toit-morse
```

Der Befehlsumfang von toit pkg und jag pkg scheint identisch zu sein, könnte sich aber im Laufe der Zeit verändern. Aktuell verwenden beide Tools die selbe Registry, welche man mit diesem Befehl ermitteln kann:
```
toit pkg registry list
```

oder mit Jaguar:
```
jag pkg registry list
```

Es gibt auch ein Webinterface für die Registry, was die Suche nach passenden Paketen sehr vereinfacht:
https://pkg.toit.io/


Wir sind jeden Dienstag um 21 Uhr live uns sprechen über digitale Dinge! Über einen Kommentar unter dem Video freuen wir uns sehr und noch schöner ist es, wenn wir uns im Live-Chat auf YouTube und Discord austauschen können. Du findest uns bei http://discord.digitale-dinge.de/


# Links zur Sendung

Die Open-Source Programmiersprache und ihre Tools:

* https://toitlang.org/
* https://github.com/toitlang/toit
* https://github.com/toitlang/jaguar

Die kommerzielle IoT Programmiersprache und ihre Tools:

* https://toit.io
* https://github.com/toitware/
