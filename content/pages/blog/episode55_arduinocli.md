---
title: arduino-cli
subtitle: mit Docker oder Github Actions
date: '2023-05-09'
categories:
  - content/data/categories/live.yaml
tags: []
excerpt: arduino-cli mit Docker oder Github Actions
thumb_image_alt: arduino-cli
image_alt: arduino-cli
image_position: top
seo:
  title: arduino-cli
  description: arduino-cli mit Docker oder Github Actions
  robots: []
  extra:
    - name: 'og:image'
      value: /images/DigitaleDinge55_Arduinocli.png
      keyName: property
      relativeUrl: true
layout: post
thumb_image: /images/DigitaleDinge55_Arduinocli.png
image: /images/DigitaleDinge55_Arduinocli.png
author: content/data/team/rene-bohne.yaml
---
<iframe width="560" height="315"
src="https://www.youtube-nocookie.com/embed/PDEqwAHVVzw?modestbranding=1"
frameborder="0" allow="accelerometer; autoplay; encrypted-media;
gyroscope; picture-in-picture" allowfullscreen>\\\</iframe>

Man kann den Arduino zwar auch lokal mit der Arduino IDE programmieren oder Visual Studio Code mit PlatformIO nutzen, aber spätestens bei automatischen Tests wird man über die Arduino-CLI stolpern. Wir schauen uns an, was man mit der Arduino-CLI lokal machen kann und wie man sie sinnvoll mit Docker oder Github Actions einsetzt.

Wir sind jeden Dienstag um 21 Uhr live uns sprechen über digitale Dinge! Über einen Kommentar unter dem Video freuen wir uns sehr und noch schöner ist es, wenn wir uns im Live-Chat auf YouTube und Discord austauschen können. Du findest uns bei http://discord.digitale-dinge.de/

# arduino-cli

Die offizielle Dokumentation zur arduino.cli befindet sich hier: https://arduino.github.io/arduino-cli/0.32/

Bei der arduino-cli handelt es sich um ein Werkzeug, das es ermöglicht, Arduino Code mit der Kommandozeile zu kompilieren und auf einen Arduino hochzuladen. Aber das Tool kann noch mehr!


## Installation

https://arduino.github.io/arduino-cli/0.32/installation/

```bash
curl -fsSL https://raw.githubusercontent.com/arduino/arduino-cli/master/install.sh | sh
```

Konfidurationsdatei anlegen 

```bash
arduino-cli config init
```


## Arduino anschließen

arduino-cli board list

## Core installieren

```bash
arduino-cli core update-index
```

Alle installierten Cores listet dieser Befehlt auf:
```bash
arduino-cli core list
```

Um einen neuen Core zu installieren, nutzt man core install. Um den arduino:avr Core zu installieren, nutzt man diesen Befehl:
```bash
arduino-cli core install arduino:avr
```

Um Cores von anderen Quellen zu installieren, muss man zunächst die passende json-Datei herunterladen. Unter Linux kann man z.B. die Package Definition der sensebox MCU mit curl herunterladen:

```bash
curl -o /root/.arduino15/package_sensebox_index.json https://raw.githubusercontent.com/sensebox/senseBoxMCU-core/master/package_sensebox_index.json
```

Um diese Datei dann nutzen zu können, muss diese Zeile ausgeführt werden:

```bash
arduino-cli --additional-urls https://raw.githubusercontent.com/sensebox/senseBoxMCU-core/master/package_sensebox_index.json core install sensebox:samd
```

Einfacher geht es, wenn die URL der zusätzlichen Board direkt in der arduino-cli.yaml Datei eingetragen werden. Dazu wird ein Abschnitt board_manager benötigt, der unter additional_urls alle URLs auflistet:

#### `arduino-cli.yaml:`
```yaml
...
board_manager:
  additional_urls:
    https://raw.githubusercontent.com/sensebox/senseBoxMCU-core/master/package_sensebox_index.json
    https://espressif.github.io/arduino-esp32/package_esp32_index.json
```

**WICHTIG**: nachdem die Datei verändert wurde, müssen die neuen Cores heruntergeladen und installiert werden. Dafür erneut diesen Befehl ausführen:

```bash
arduino-cli core update-index
```

Da wir in diesem Beispiel die Package Informationen für ESP32 Boards hinzugefügt haben, können wir nach diesem Core nun suchen:

```bash
arduino-cli core search esp32
```

Und natürlich können wir diesen Core nun auch installieren:

```bash
arduino-cli core install esp32:esp32
```




## Neuen Sketch erstellen

```bash
arduino-cli sketch new Blink
```

Der neue Sketch beinhaltet eine setup() und eine loop() Funktion:

```bash
cat Blink.ino
```

```Arduino
void setup() {
}

void loop() {
}
```

Hier können wir nun wie gewohnt unseren Arduino-Programmcode eingeben:

```Arduino
void setup() {
    pinMode(LED_BUILTIN, OUTPUT);
}

void loop() {
    digitalWrite(LED_BUILTIN, HIGH);
    delay(1000);
    digitalWrite(LED_BUILTIN, LOW);
    delay(1000);
}
```


## Sketch kompilieren

Möchte man den Sketch für den Arduino UNO kompilieren, muss dieser Befehl verwendet werden:

```bash
arduino-cli compile --fqbn arduino:avr:uno Blink.ino
```
## Probleme beim Kompilieren für ESP32

Um den selben Sketch für ein ESP32 Nodemcu zu kompilieren, kann der Befehl leicht angepasst werden:

```bash
arduino-cli compile --fqbn arduino:avr:uno Blink.ino
```

Da Python eine Katastophe ist, muss man ggf. folgende Befehle unter Linux ausführen, damit nicht die falsche Python Version genutzt wird, was in der Regel immer zu Problemen mit dem esptool führen wird:

```bash
sudo apt install python-is-python3
```

Damit niemals wieder diese Python2-Katastrophe passieren kann, sollte man die Installation von Python2 verbieten:

```bash
sudo apt-mark hold python2 python2-minimal python2.7 python2.7-minimal libpython2-stdlib libpython2.7-minimal libpython2.7-stdlib
```

Wer diese Probleme hat, wird auch diesen Befehl wahrscheinlich einmal ausführen müssen, um pyserial passend zu installieren:

```bash
pip install pyserial
```

## LED_BUILTIN und Compiler Flags

Nicht bei jedem Board ist LED_BUILTIN definiert. Wenn man z.B. ein generisches ESP32 Board verwendet, an dem die LED an Pin 13 angeschlossen ist, kann man Compiler Flags über den --build-property Parameter anhängen:

```bash
  arduino-cli compile -b esp32:esp32:esp32 --build-property "build.extra_flags=-DLED_BUILTIN=13" Blink.ino
```

## Wo sind die Binaries?

Um die Binaries (hex, elf, usw.) ins Sketchverzeichnis zu bekommen, muss der Parameter `-e` hinzugefügt werden. Für unser Blink Beispiel also:

```bash
arduino-cli compile --fqbn arduino:avr:uno -e Blink.ino
```

Die Binaries befinden sich dann im `build` Unterverzeichnis.

## Sketch auf Board flashen

Für den Arduino UNO können wir das zuvor kompilierte Sketch jetzt mit diesem Befehl auf das Board flashen - passe den seriellen Port (hier /dev/ttyACM0) ggf. an:

```bash
arduino-cli upload -p /dev/ttyACM0 --fqbn arduino:avr:uno .
```

Mein NodeMCU ESP32S3 wurde als /dev/ttyUSB0 erkannt und ich kann das Programm entsprechend auf das DevBoard laden:

```bash
arduino-cli upload -p /dev/ttyUSB0 --fqbn sp32:esp32:esp32s3 Blink.ino
```

## Falls das ESP32 Board nicht erkannt wird

Häufig kommt es vor, dass man den seriellen Port des ESP32 Boards nicht aufgelistet bekommt, weil der BRLTTY Service schneller war. Das erkennt man leicht daran, dass eine ähnliche Ausgabe wie die folgende auf den Befehl `sudo dmesg` folgt:

```bash
usb 2-3: ch341-uart converter now attached to ttyUSB0
input: BRLTTY 6.5 Linux Screen Driver Keyboard as /devices/virtual/input/input38
usb 2-3: usbfs: interface 0 claimed by ch341 while 'brltty' sets config #1
```

Wenn Du nicht blind bist, kannst Du den BRLTTY Service einfach deaktivieren. Ich mache dazu folgende Eingaben:

```
for f in /usr/lib/udev/rules.d/*brltty*.rules; do
    sudo ln -s /dev/null "/etc/udev/rules.d/$(basename "$f")"
done
sudo udevadm control --reload-rules
```

Und wenn das nicht hilft, kann man auch diesen Befehl probieren:

```
sudo systemctl mask brltty.path
```

Das ESP32 Board sollte nun als /dev/ttyUSB0 o.ä. identifiziert werden können.

## Libraries installieren

Um Libraries aus "unsicheren" Quellen wie git oder zip installieren zu können, muss man diese Funktion zunächst in der Config aktivieren:
```bash
 arduino-cli config set library.enable_unsafe_install true
 ```

Um nun z.B. die TinyGSM Library in Version v0.11.5 zu installieren, führt man diese Zeile aus:

```bash
arduino-cli lib install --git-url https://github.com/vshymanskyy/TinyGSM.git#v0.11.5
```

Wenn man zuvor eine Library (z.B. für den BMP280 Sensor) als zip-Datei heruntergeladen hat, kann man sie mit diesem Befehl installieren:

```bash
arduino-cli lib install --zip-path BMP280.zip
```


## Ein Beispiel: Morsen mit der LED

Alle offiziellen Libraries befinden sich in diesem Repository: `https://github.com/arduino/library-registry`

Wenn man dort nach "Morse" sucht, findet man u.a. die MorseArduino Library. Wir wissen bereits, dass wir sie über ihre git-URL wie folgt installieren können:

```bash
arduino-cli lib install --gir-url https://github.com/etherkit/MorseArduino.git
```
Da es sich jedoch um eine Library aus dem offiziellen Repo handelt, können wir sie auch einfach so installieren:

```bash
arduino-cli lib install "Etherkit Morse"
```

Wenn wir dann noch die uralte SimpleTimer Library via git installieren:

```bash
arduino-cli lib install --gir-url https://github.com/schinken/SimpleTimer.git
```

können wir folgendes Sketch erstellen:

```bash
arduino-cli sketch new MorseDinge
cd MorseDinge
```

Und dann kopieren wir folgendne Code in das neue Sketch:

```Arduino
#include <SimpleTimer.h> // https://github.com/schinken/SimpleTimer.git
#include <Morse.h> // https://github.com/etherkit/MorseArduino

#define SPEED 15

Morse morse(LED_BUILTIN, SPEED);
SimpleTimer timer;

void repeatMe()
{
    morse.update();
}

void setup()
{
    pinMode(LED_BUILTIN, OUTPUT);
    timer.setInterval(1, repeatMe);

    delay(1000);
    morse.send("DIGITALE DINGE");
}

void loop()
{
    timer.run();
}
```

Jetzt noch schnell den Sketch für den Arduino UNO kompilieren:

```bash
arduino-cli compile -b arduino:avr:uno MorseDinge.ino 
```

Und auf das Board hochladen:

```bash
arduino-cli upload -p /dev/ttyACM0 --fqbn arduino:avr:uno .
```

# Arduino-CLI mit Docker

Heutzutage installiert man ja nichts mehr lokal, sondern verwendet für alles einen Container. Docker bietet sich da an!

Wer Docker noch nicht installiert hat, folgt bitte dieser Anleitung: https://docs.docker.com/get-docker/

Das Ziel ist, einen Docker Container zu erstellen, in dem unser Arduino Sketch mit Hilfe von arduino-cli kompiliert wird.

Fangen wir mit einem neuen Verzeichnis an und nennen es HalloDinge und erstellen wir darin ein Unterverzeichnis mit dem Namen build: 

```bash
mkdir HalloDinge
cd HalloDinge
mkdir build
```

Im Verzeichnis HalloDinge erstellen wir die bereits bekannte Sketch-Datei HalloDinge.ino:

#### `HalloDinge/HalloDinge.ino:`
```Arduino
#include <SimpleTimer.h> // https://github.com/schinken/SimpleTimer.git
#include <Morse.h> // https://github.com/etherkit/MorseArduino

#define SPEED 15

Morse morse(LED_BUILTIN, SPEED);
SimpleTimer timer;

void repeatMe()
{
    morse.update();
}

void setup()
{
    pinMode(LED_BUILTIN, OUTPUT);
    timer.setInterval(1, repeatMe);

    delay(1000);
    morse.send("DIGITALE DINGE");
}

void loop()
{
    timer.run();
}
```


Im Verzeichnis HalloDinge erstellen wir nun eine Datei `Dockerfile` mit dem folgenden Inhalt:

#### `HalloDinge/Dockerfile:`
```Dockerfile
FROM ubuntu:23.04

RUN apt-get update
RUN apt-get install -y curl

# install arduicno-cli
RUN curl -fsSL https://raw.githubusercontent.com/arduino/arduino-cli/master/install.sh | BINDIR=/usr/local/bin sh

RUN arduino-cli config init

# aloow unsafe sources (zip, git)
RUN arduino-cli config set library.enable_unsafe_install true

# update arduino-cli
RUN arduino-cli core update-index

# install arduino avr
RUN arduino-cli core install arduino:avr

RUN arduino-cli lib install --git-url https://github.com/etherkit/MorseArduino.git

RUN arduino-cli lib install --git-url https://github.com/schinken/SimpleTimer.git

RUN mkdir /build 

WORKDIR /HalloDinge/

COPY ./HalloDinge.ino /HalloDinge

RUN arduino-cli compile -b arduino:avr:uno -e HalloDinge.ino 

CMD ["bash"]
```

Wir können basierend auf diesem Dockerfile ein Docker Image bauen mit diesem Befehl:

```bash
docker build -t arduino-build .
```

Und jetzt kommt der wichtigste Teil: wir wollen einen Container erstellen, der dieses Image benutzt. Der Container soll nur solange exisiteren bis er beendet wird und dann soll er automatisch gelöscht werden. Das erledigt der Parameter `--rm`. Da wir mit der Shell des Containers interagieren müssen, fügen wir den Parameter `-it` hinzu. Um die kompilierten Binaries zu extrahieren, mounten wir den Unterordner /build aus unserem HalloDinge Ordner mit dem Ordner /build im Docker Container. Das passiert durch den Paramter `-v $(pwd)/build:/build/` und das ganze sieht dann so aus:

```bash
docker run --rm -it -v $(pwd)/build:/build/ arduino-build
```

Der Container wird gestartet und wir sehen die Shell des Containers. In dieser Shell können wir uns wie in jedem normalen Ubuntu umschauen, etwa das aktuelle Verzeichnis listen:

```bash
ls -la
```

Wir sehen, dass es einen Unterordner build gibt. Den Inhalt dieses Ordners wollen wir auf unseren Host kopieren. Das geht ganz einfach in dem Container:

```bash
cp -R build/* /build/
```

wir können nur mit `exit` den Container verlassen und dieser wird automatisch gelöscht.

Die extrahierten Binaries liegen auf unserem Host Computer im Unterordner /build 

**TIPP**: Auch wenn wir hier im Beispiel den Container automatisch löschen lassen, bietet sich an, von Zeit zu Zeit alls Container, die nicht mehr laufen, zu entfernen. Das geht am einfachsten mit dem Befehl `docker container prune`

# Github Actions

Ein noch einfacherer Weg, um Arduino Sourcecode automatisch kompilieren zu lassen, ist es, Github Actions zu verwenden.

Dazu legen wir in github ein neues Repo an und clonen es lokal.

In dem leeren Repo erzeugen wir einen Unterordner `HalloDinge` und erstelllen darin die Datei `HalloDinge.ino` mit dem selben Inhalt wie bisher:

#### `meinRepo/HalloDinge/HalloDinge.ino:`
```Arduino
#include <SimpleTimer.h> // https://github.com/schinken/SimpleTimer.git
#include <Morse.h> // https://github.com/etherkit/MorseArduino

#define SPEED 15

Morse morse(LED_BUILTIN, SPEED);
SimpleTimer timer;

void repeatMe()
{
    morse.update();
}

void setup()
{
    pinMode(LED_BUILTIN, OUTPUT);
    timer.setInterval(1, repeatMe);

    delay(1000);
    morse.send("DIGITALE DINGE");
}

void loop()
{
    timer.run();
}
```

Wir benötigen für Github Actions ein Unterverzeichnis workflows im Unterverzeichnis .github:

```bash
mkdir .github
cd .github
mkdir workflows
cd workflows
``` 
Jetzt übertragen wir unser Wissen aus dem Dockerfile in eine Datei namens main.yml in diesem neuen Verzeichnis:

#### `meinRepo/.github/workflows.main.yml:`
```yaml
name: build and release for Arduino UNO

on: [workflow_dispatch]

jobs:
  compile:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Setup Arduino CLI
        uses: arduino/setup-arduino-cli@v1

      - name: Install platform and libraries
        run: |
          arduino-cli config init
          arduino-cli config set library.enable_unsafe_install true
          arduino-cli core update-index
          arduino-cli core install arduino:avr
          arduino-cli lib install --git-url https://github.com/etherkit/MorseArduino.git
          arduino-cli lib install --git-url https://github.com/schinken/SimpleTimer.git

      - name: Compile Sketch
        run: arduino-cli compile --fqbn arduino:avr:uno -e ./HalloDinge
```

Der Workflow erhält den Namen "build and release for Arduino UNO" und wird nur manuell ausgeführt (workflow_dispatch). Hier könnte man alternativ auch `push` oder `pull_request` eintragen. Bei `push` kann man zusätzlich die branches festlegen, die überwacht werden sollten, etwa in dieser Form:

```yaml
...
on:
  push:
    branches:
      - 'main'
...
```

Dieser Workflow besitzt nur einen einzigen Job mit dem Namen 'compile'. Für diesen Job wird Ubuntu verwendet und es folgen unter `steps:` die einzelnen Schritte, analog zu dem, was wir im Dockerfile gemacht hatten.

Diese Datei ist in github zu commiten und zu pushen. 

Um die Action auszuführen, muss man in der github Web-UI in dem Repo auf den Reiter "Actions". Dort ist die "build and release for Arduino UNO" Action zu sehen und man kann rechts im Menü auf "Run workflow" clicken, nachdem man die Action durch einen Click ausgewählt hat.

## Push to Releases

Ohne hier ins Detail gehen zu wollen, möchte ich noch erwähnen, dass es möglich ist, die kompilierten Binaries automatisch einem neuen Release zuzuordnen. Dazu muss die main.yml Datei um folgende Einträge ergänzt werden:

```yaml
      - name: Push to Releases
        uses: ncipollo/release-action@v1
        with:
          artifacts: "HalloDinge/build/arduino.avr.uno/*"
          tag: v0.1.${{ github.run_number }}
          token: ${{ secrets.TOKEN }}
```

secrets.TOKEN ist dabei ein deploy Token, das man extra für diese Action in seinem User Account angelegt hat. Ohne dieses Token hat die release-action keine Berechtigung, eine neue Release anzulegen.

Das komplette Repo gibt es hier: https://github.com/renebohne/arduino-github-actions-tutorial