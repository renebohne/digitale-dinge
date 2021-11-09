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

Zunächst müssen wir verstehen, was die Switchbot REST API überhaupt ist. Die Dokumentation befindet sich frei zugänglich auf <https://github.com/OpenWonderLabs/SwitchBotAPI>

Dort steht beschrieben, wie man an das "Open Token" gelangt, mit dem man Zugriff auf die Daten der Geräte erhält. Wer das Token hat, kann auf die Daten zugreifen, also sollte man es geheim halten! Und anders herum: wer das Token nicht hat, kann nicht auf die Daten meiner Geräte zugreifen. In Kombination mit der Verschlüsselung ist das ein recht guter Sicherheitsstandard.

# REST API mit cURL benutzen

Um alle Geräte aufzulisten, kann dieser cURL Befehl im Terminal verwendet werden:

```
curl -H "Authorization: MEINGEHEIMESTOKEN" https://api.switch-bot.com/v1.0/devices/
```

Natürlich muss MEINGEHEIMESTOKEN durch das tatsächliche Open Token ersetzt werden.

Um mehr Details über ein Gerät abzurufen, kann dieser Befehl verwendet werden:

```
curl -H "Authorization: MEINGEHEIMESTOKEN" https://api.switch-bot.com/v1.0/devices/MEINEDEVICEID/status
```

MEINEDEVICEID muss dabei durch die DeviceID des Geräts ersetzt werden, für welches die Details abgerufen werden sollen. Die DeviceId steht z.B. in der langen Liste, die von dem vorherigen Befehl zurückgegeben wurde.

Es ist sogar möglich, spezielle Geräte, wie z.B. den Switchbot Bot zu steuern. Dafür wird nicht mehr die HTTP GET Methode verwendet, sondern der Befehl wird als JSON im Body eines HTTP POST Befehlt übermittelt. Einsen Switchbot Bot kann man z.B. wie folgt auslösen:

```
curl -H "Authorization: MEINGEHEIMESTOKEN" -d '{"command": "turnOn", "parameter": "default", "commandType": "command"}' -H "Content-Type: application/json" -X POST https://api.switch-bot.com/v1.0/devices/MEINEDEVICEID/commands
```

# Wie geht das mit node.js?

Leider gibt es noch keine Javascript Library für die Switchbot REST API. Als Inspiration kann dieser ioBroker Adapter dienen:
https://github.com/DrozmotiX/ioBroker.switchbot-hub

Um die Switchbot REST API mit node.js zu verwenden, legt man zunächst ein neues Verzeichnis an und ruft dann npm init auf. Dann hat man die Qual der Wahl, aber um dem Beispiel des ioBroker Adapters zu folgen, wollen wir ebenfalls die axios Library verwenden. Diese muss wie folgt installiert werden:

```
npm install --save axios
```

Aus der main.js des genannten Adapters haben wir etwas Code entnommen und ihn für dieses Tutorial vereinfacht, bitte in (eine neu anzulgende Datei) index.js speichern:

```index.js
const axios = require('axios');

var config = require('./config');

devices = {};


 function apiCall(url, data) {

    if (!url) throw new Error(`No URL provided, cannot make API call`);
    if (!data) {
        return axios(url, {
            baseURL: 'https://api.switch-bot.com',
            url: url,
            timeout: 5000,
            headers: {'Authorization': config.openToken}
        })
            .then(response => response.data)
            .catch(error => {
                throw new Error(`Cannot handle API call : ${error}`);
            });

    } else {
        return axios.post(url, data, {
            baseURL: 'https://api.switch-bot.com',
            url: url,
            timeout: 5000,
            headers: {
                'Content-Type': 'application/json;charset=UTF-8',
                'Authorization': config.openToken,
            }
        })
            .then(response => response.data)
            .catch(error => {
                throw new Error(`Cannot handle API call : ${error}`);
            });
    }
}

//used in loadDevices
const arrayHandler = async (deviceArray) => {
    for (const device in deviceArray) {
        devices[deviceArray[device].deviceId] = deviceArray[device];

        console.log(`GET status for: ${JSON.stringify(devices[deviceArray[device].deviceId].deviceName)}`);
        await getDeviceStatus(deviceArray[device].deviceId);    
    }
};

//gets all devices and theis status
async function getDevices() {
    try {
        const resp = await apiCall(`/v1.0/devices`);
        console.log(`GET /v1.0/devices response: ${JSON.stringify(resp)}`);
        if (!resp) {
            console.log(`Error: no devices received`);
            return;
        }

        const deviceList = resp.body.deviceList;
    
        console.log(`Found ${deviceList.length} devices via SwitchBot API`);

        try {
            if (deviceList) {
                await arrayHandler(deviceList);
            } else {
                console.log(`Can not handle device list from SwitchBot API`);
            }
        } catch (error) {
            console.log(error);
        }
    } catch (error) {
        console.log(error);
    }
}

//gets device status by deviceId
async function getDeviceStatus(deviceId) {
    try {

        const resp = await apiCall(`/v1.0/devices/${deviceId}/status`);
        const deviceStatus = resp.body;
        console.log(`STATUS: ${JSON.stringify(resp)}`);
        if (!deviceStatus || Object.keys(deviceStatus).length === 0) {
            console.log(`This devicetype has no states: ${devices[deviceId].deviceType}`);
            return;
        }
    } catch (error) {
        console.log(error);
    }
}

getDevices();
```

Der Code geht davon aus, dass im selben Verzeichnis eine Datei namens config.js zu finden ist mit folgendem Inhalt:

```config.js
var config = {};

config.openToken = 'MEINGEHEIMESTOKEN';

module.exports = config;
```

Natürlich muss in der Datei der Text MEINGEHEIMESTOKEN durch das zuvor bestimmte Open Token ersetzt werden.

Nun kann das Programm gestartet werden mit diesem Befehl:

```
node index.js
```





