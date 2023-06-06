---
title: DIY Gartenbewässerung
subtitle: Mit ESP32 und toit
date: '2023-06-06'
categories:
  - content/data/categories/live.yaml
tags: []
excerpt: Mit ESP32 und toit
thumb_image_alt: DIY Gartenbewässerung
image_alt: DIY Gartenbewässerung
image_position: top
seo:
  title: DIY Gartenbewässerung
  description: Mit ESP32 und toit
  robots: []
  extra:
    - name: 'og:image'
      value: /images/DigitaleDinge58_ESP32Garten.png
      keyName: property
      relativeUrl: true
layout: post
thumb_image: /images/DigitaleDinge58_ESP32Garten.png
image: /images/DigitaleDinge58_ESP32Garten.png
author: content/data/team/rene-bohne.yaml
---
<iframe width="560" height="315"
src="https://www.youtube-nocookie.com/embed/s5HhuRkDpgw?modestbranding=1"
frameborder="0" allow="accelerometer; autoplay; encrypted-media;
gyroscope; picture-in-picture" allowfullscreen>\\\</iframe>

Wenn es im Sommer trocken wird, müssen auch Maker ihre Pflanzen und den Rasen bewässern. Um entscheiden zu können, ob der Boden zu trocken ist, kann ein Bodenfeuchtesensor verwendet werden. Um den Boden zu bewässern können Ventile und Pumpen genutzt werden. Wie man diese Sachen an einen ESP32 anschließt, besprechen wir in diesem Video!

## TOIT installieren

Zunächst sollte man die toit Programmiersprache installiert haben und folgendes Tutorial verstanden haben: https://www.digitale-dinge.de/blog/episode24_toit/

## ADC auslesen

Nun können wir den Bodenfeuchtesensor mit dem ADC des ESP32 auslesen. Der folgende Source-Code erledigt dies in wenigen Codezeilen:

```toit
import gpio
import gpio.adc show Adc
import encoding.json as json

pinnumber_soil ::= 34

main:
    pin_soil := gpio.Pin pinnumber_soil
    adc_soil := Adc (pin_soil)

    (Duration --s=1).periodic:
        soil := adc_soil.get

        json_str := "{'soil':'$soil'}"
        print json_str
```

Natürlich kann auch jeder andere ADC-Pin anstelle von Pin 34 verwendet werden. Das Beispiel gibt ein JSON Objekt auf der Kommandozeile aus.
Im Video zeige ich, wie man das Programm auf einen ESP32 lädt.

## MQTT

Im folgenden Beispiel werden die aktuellen Messwert von Bodenfeuchte und Batteriespannung jeweils in Volt an einen MQTT Broker geschickt. 
Die Adresse des Brokers steckt in der Konstante `HOST`und die MQTT topics sind in den Konstanten `TOPIC_SOIL, TOPIC_BATTERY, TOPIC_LED und TOPIC_VALVE` hinterlegt.
Die Pins von LED, Bodenfeuchtesensor und Ventil sind leicht über folgende Konstanten zu ändern: `pinnumber_led, pinnumber_soil, pinnumber_valve`
Da in diesem Beispiel ein Roboheart Hercules Board der Firma Augmented Robotics verwendet wird, enthält der Code weitere Konstanten für die Pins des Motortreibers und die Methoden `valve_init,valve_sleep,valve_close,valve_open` müssen für andere Boards mit anderen Motortreibern/ICs angepasst werden, um das Ventil zu steuern. 

Um die MQTT Library nutzen zu können, muss sie mit folgendem Befehl hinzugefügt werden: 
```bash 
jag pkg install github.com/toitware/mqtt
```

Hier der Beispielcode aus dem Livestream:

```toit
import gpio
import gpio.adc show Adc
import encoding.json as json

import net
//jag pkg install github.com/toitware/mqtt
import mqtt

OKAKI_DEVICE_ID ::= "device2"
CLIENT_ID ::= "okaki32-$(OKAKI_DEVICE_ID)"
HOST      ::= "192.168.100.110"

TOPIC_SOIL ::= "okaki/$(OKAKI_DEVICE_ID)/soil"
TOPIC_BATTERY ::= "okaki/$(OKAKI_DEVICE_ID)/battery"
TOPIC_LED ::= "okaki/$(OKAKI_DEVICE_ID)/led"
TOPIC_VALVE ::= "okaki/$(OKAKI_DEVICE_ID)/valve"

pinnumber_led ::= 14
pinnumber_soil ::= 34
pinnumber_battery ::= 36

GPIO_MA_PH_IN1 ::= 25  //  PHASE/IN1  //DAC1
GPIO_MA_EN_IN2 ::= 26  //  ENABLE/IN2  //DAC2
GPIO_MA_MODE ::= 2  //  MODE        
GPIO_MA_SLEEP ::= 0  //  nSLEEP

pin_ma_ph_in1 := gpio.Pin GPIO_MA_PH_IN1 --output
pin_ma_en_in2 := gpio.Pin GPIO_MA_EN_IN2 --output
pin_ma_mode := gpio.Pin GPIO_MA_MODE --output
pin_ma_sleep := gpio.Pin GPIO_MA_SLEEP --output

valve_init:  
  pin_ma_ph_in1.set 0
  pin_ma_en_in2.set 0
  pin_ma_mode.set 0
  pin_ma_sleep.set 1

valve_sleep:
  //coast mode
  pin_ma_ph_in1.set 0
  pin_ma_en_in2.set 0
  pin_ma_sleep.set 0

valve_close:
  pin_ma_sleep.set 1
  pin_ma_ph_in1.set 1
  pin_ma_en_in2.set 0
  sleep --ms=100
  valve_sleep

valve_open:
  pin_ma_sleep.set 1
  pin_ma_ph_in1.set 0
  pin_ma_en_in2.set 1  
  sleep --ms=100
  valve_sleep

main:
  pin_soil := gpio.Pin pinnumber_soil
  pin_battery := gpio.Pin pinnumber_battery
  pin_led := gpio.Pin pinnumber_led --output

  adc_soil := Adc (pin_soil)
  adc_battery := Adc (pin_battery)

  valve_init

  network := net.open
  transport := mqtt.TcpTransport network --host=HOST
  client := mqtt.Client --transport=transport
  client.start --client_id=CLIENT_ID

  // MQTT broker is now connected.

  client.subscribe TOPIC_LED:: | topic payload |
    print "Received: $topic: $payload.to_string_non_throwing"
    if payload.to_string_non_throwing == "1": 
      print "LED ON"
      pin_led.set 1
    else:
      print "LED OFF"
      pin_led.set 0 

  client.subscribe TOPIC_VALVE:: | topic payload |
    print "Received: $topic: $payload.to_string_non_throwing"
    if payload.to_string_non_throwing == "1": 
      print "VALVE ON"
      valve_open
    else:
      print "VALVE OFF"
      valve_close
  

  (Duration --s=10).periodic:
    soil := adc_soil.get
    battery := adc_battery.get

    json_str := "{'soil':'$soil','battery':'$battery'}"
    print json_str

    client.publish TOPIC_SOIL "$soil".to_byte_array
    client.publish TOPIC_BATTERY "$battery".to_byte_array
```

## OKAKI nutzen

Das OKAKI Open-Source Projekt ist noch nicht fertig, aber folgender Beispielcode sendet Messwerte an die REST API der Okaki-Device-Bridge:

```toit
import gpio
import gpio.adc show Adc
import encoding.json as json

//jag pkg install github.com/toitware/http
import http
import net
import certificate_roots
import esp32
import http.headers

CERTIFICATE ::= certificate_roots.ISRG_ROOT_X1
network := net.open
client := http.Client.tls network --root_certificates=[CERTIFICATE]

URL ::= "appwrite.okaki.org"
PORT ::= 8079
PATH ::= "/measurements"

DEVICE_ID ::= "device2"
KEY ::= "my-secret"

pinnumber_led ::= 14
pinnumber_soil ::= 34
pinnumber_battery ::= 36
GPIO_MA_PH_IN1 ::= 25  //  PHASE/IN1  //DAC1
GPIO_MA_EN_IN2 ::= 26  //  ENABLE/IN2  //DAC2
GPIO_MA_MODE ::= 2  //  MODE        
GPIO_MA_SLEEP ::= 0  //  nSLEEP
pin_ma_ph_in1 := gpio.Pin GPIO_MA_PH_IN1 --output
pin_ma_en_in2 := gpio.Pin GPIO_MA_EN_IN2 --output
pin_ma_mode := gpio.Pin GPIO_MA_MODE --output
pin_ma_sleep := gpio.Pin GPIO_MA_SLEEP --output

valve_init:  
  pin_ma_ph_in1.set 0
  pin_ma_en_in2.set 0
  pin_ma_mode.set 0
  pin_ma_sleep.set 1

valve_sleep:
  //coast mode
  pin_ma_ph_in1.set 0
  pin_ma_en_in2.set 0
  pin_ma_sleep.set 0

valve_close:
  pin_ma_sleep.set 1
  pin_ma_ph_in1.set 1
  pin_ma_en_in2.set 0
  sleep --ms=100
  valve_sleep

valve_open:
  pin_ma_sleep.set 1
  pin_ma_ph_in1.set 0
  pin_ma_en_in2.set 1  
  sleep --ms=100
  valve_sleep

main:
  pin_soil := gpio.Pin pinnumber_soil
  pin_battery := gpio.Pin pinnumber_battery
  pin_led := gpio.Pin pinnumber_led --output

  adc_soil := Adc (pin_soil)
  adc_battery := Adc (pin_battery)

  valve_init

  
  (Duration --s=60).periodic:
    soil := adc_soil.get
    battery := adc_battery.get
    json_str := {"deviceID":"$DEVICE_ID","key":"$KEY","measurements":[{"sensorID":"647f3cf0e1c62af05311","sensorTypeID":"soilmoisture","value":"$soil"},{"sensorID":"647f3d06599e879074f8","sensorTypeID":"battery","value":"$battery"}]}

    print "sending POST REQUEST TO $URL:$PORT$PATH"
    print json_str
    response := client.post_json --host=URL --port=PORT --path=PATH json_str
  
    print response.status_code
    print response.status_message
    data := response.body.stringify
    //data := json.decode_stream response.body
    client.close
    print data
```

Damit das Beispiel funktionieren kann, müssen alle Pakete aus dieser package.yaml Datei installiert sein:

```yaml
dependencies:
  certificate_roots:
    url: github.com/toitware/toit-cert-roots
    version: ^1.4.0
  dhtxx:
    url: github.com/toitware/toit-dhtxx
    version: ^1.3.1
  http:
    url: github.com/toitlang/pkg-http
    version: ^2.1.0
  mqtt:
    url: github.com/toitware/mqtt
    version: ^2.2.0
```

Um die Packete zu installieren, genügt dieser Befehl:
```bash
jag pkg install
```

# Links zur Sendung

* github mit Sourcecode aus der Sendung und mehr: https://github.com/okaki-gardening/toit-firmware
* Okaki Webseite: https://okaki.org/
* toit Programmiersprache: https://toit.io/
* toit Dokumentation: https://docs.toit.io/