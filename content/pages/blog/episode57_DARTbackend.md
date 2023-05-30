---
title: Backend mit DART
subtitle: Server und Services
date: '2023-05-30'
categories:
  - content/data/categories/live.yaml
tags: []
excerpt: Server und Services
thumb_image_alt: Backend mit DART
image_alt: Backend mit DART
image_position: top
seo:
  title: Backend mit DART
  description: Server und Services
  robots: []
  extra:
    - name: 'og:image'
      value: /images/DigitaleDinge57_DARTbackend.png
      keyName: property
      relativeUrl: true
layout: post
thumb_image: /images/DigitaleDinge57_DARTbackend.png
image: /images/DigitaleDinge57_DARTbackend.png
author: content/data/team/rene-bohne.yaml
---
<iframe width="560" height="315"
src="https://www.youtube-nocookie.com/embed/CWp-6FMM8Fg?modestbranding=1"
frameborder="0" allow="accelerometer; autoplay; encrypted-media;
gyroscope; picture-in-picture" allowfullscreen>\\\</iframe>

Die Dart Programmiersprache wurde entwickelt, um UIs mit Flutter zu entwickeln. 

Man kann Dart aber auch für Backends benutzen. Diese Woche schauen wir uns an, wie einfach das ist und wie man einen Webservice mit Dart entwickelt und ihn bei Bedarf sogar sehr effizient in Docker Containern betreiben kann!

Wir sind jeden Dienstag um 21 Uhr live uns sprechen über digitale Dinge! Über einen Kommentar unter dem Video freuen wir uns sehr und noch schöner ist es, wenn wir uns im Live-Chat auf YouTube und Discord austauschen können. Du findest uns bei http://discord.digitale-dinge.de/

# Einen Server mit Dart erstellen

In diesem Tutorial gehen wir davon aus, dass Dart 3.0.2 installiert ist.

Es gibt ein Template, das shelf verwendet, um einen Server mit Dart zu bauen:
```bash
dart create -t server-shelf meinserver
```

Der Befehl erzeugt nicht nur ein Dart Projekt mit dem Nanmen 'meinserver', sondern auch ein passendes Dockerfile. Damit kann ein Docker Image erzeugt werden:


```bash
cd meinserver
docker build -t dart-server .
```

Auf der Grundlage dieses 'dart-server' Images können wir einen Docker Container namens 'meinserver' erzeugen und ihn starten:

```bash
docker run -it --rm -p 8080:8080 --name meinserver dart-server
```

Das Beispiel besteht im Wesentlichen aus der Datei `server.dart` im Verzeichnis `bin`:

```dart
import 'dart:io';

import 'package:shelf/shelf.dart';
import 'package:shelf/shelf_io.dart';
import 'package:shelf_router/shelf_router.dart';

// Configure routes.
final _router = Router()
  ..get('/', _rootHandler)
  ..get('/echo/<message>', _echoHandler);

Response _rootHandler(Request req) {
  return Response.ok('Hello, World!\n');
}

Response _echoHandler(Request request) {
  final message = request.params['message'];
  return Response.ok('$message\n');
}

void main(List<String> args) async {
  // Use any available host or container IP (usually `0.0.0.0`).
  final ip = InternetAddress.anyIPv4;

  // Configure a pipeline that logs requests.
  final handler = Pipeline().addMiddleware(logRequests()).addHandler(_router);

  // For running in containers, we respect the PORT environment variable.
  final port = int.parse(Platform.environment['PORT'] ?? '8080');
  final server = await serve(handler, ip, port);
  print('Server listening on port ${server.port}');
}
```

Es wird ein HTTP Server auf Port 8080 gestartet mit zwei Routen:

* **`/`** gibt den String 'Hello, World' zurück und 
* **`/echo`** gibt den auf die URI folgenden Parameter zurück.

Um den Docker Container zu beenden, muss in einem weiteren Terminal dieser Befehl ausgeführt werden:

```bash
docker kill meinserver
```
Das ist auch schon alles!

Wer auf Docker verzichten möchte, kann einfach diesen Befehl aufrufen:

```bash
dart run bin/server.dart
````

## HTTPS

Der Server kann bisher nur HTTP.
Um HTTPS zu aktivieren, müssen folgende Dateien erzeugt/kopiert werden:

* `certificates/cert.pem` (Bei Let's Encrypt heißt die Datei meist `fullchain.pem`)
* `certificates/key.pem` (Bei Let's Encrypt heißt die Datei meist `privkey.pem`)

Für einen einfachen Test genügt es, lokale, selbstsignierte Zertifikate im Ordner `bin/certificates` zu erzeugen:

```bash
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -sha256 -days 3650 -nodes -subj "/C=DE/ST=Berlin/L=Berlin/O=digitale-dinge/OU=dev/CN=localhost"
```

Für einen öffentlichen Webserver kann man aber besser Dienste wie Let's Encrypt nutzen.

Wir müssen nun eine neue Methode erzeugen, die diese Dateien lesen kann und einen `SecurityContext` zurückgibt:

```dart
SecurityContext getSecurityContext() {
  final chain =
      Platform.script.resolve('certificates/cert.pem').toFilePath();
  final key =
      Platform.script.resolve('certificates/key.pem').toFilePath();

  return SecurityContext()
    ..useCertificateChain(chain)
    ..usePrivateKey(key);
}
```

Um den `SercurityContext` nutzen zu können, muss diese Zeile gefunden werden:

```dart
final server = await serve(handler, ip, port);
```

und wie folgt erweitert werden:
```dart
final server = await serve(handler, ip, port, securityContext: getSecurityContext());
```

# Links zur Sendung

* Offizielle Dart Webseite: https://dart.dev/
* Offizielles Dart Docker Image: https://hub.docker.com/_/dart
* Github von okaki-backend: https://github.com/okaki-gardening/okaki-backend
* Github von okaki-device-bridge: https://github.com/okaki-gardening/okaki-device-bridge


