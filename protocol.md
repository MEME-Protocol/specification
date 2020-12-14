---
title: Modern End-to-End Messaging Exchange Protocol (MEME)
author: Benjamin Faller, Sascha Ivan & Sascha Villing
lang: de-DE
---
# Verbindungsaufbau mit dem Server

Der Server hat den TCP Port 2000 geöffnet. Unter diesem wird die Kommunikation
mit den verschiedenen Clients durchgeführt.

Alle TCP Nachrichten werden im JSON-Format übertragen. Das ermöglicht einfache
Serialisierung und Deserialisierung. Alle Nachrichten werden durch ihren Typ per
JSON identifiziert. Ist der Typ nicht bekannt oder es fehlen Angaben, wird die
Nachricht ignoriert.

Damit der Beginn und das Ende der Nachricht erkannt werden kann sind alle
Nachrichten in folgendem Rahmen aufgeteilt:

```text
<message size><json>
```

Die „message size“ gibt die folgende Länge der Nachricht in Bytes an. Sie wird
als uint 32 übertragen.

## Register Nachricht

Die `register` Nachricht ist wie folgt aufgebaut.

**Request**

```json
{
  "type": "register",
  "nickname": "asdf",
  "ip": "127.0.0.1",
  "port": 50000,
}
```

Der `port` muss hierbei im Wertebereich 50000–60000 liegen. Sonst wird die
Nachricht ignoriert. Die IP muss korrekt formatiert sein.

## Unregister Nachricht

Möchte sich ein Client abmelden, so wird folgende JSON Nachricht versandt:

```json
{
  "type": "unregister",
  "nickname": "asdf"
}
```

Der Server bestätigt den Erhalt durch Schließen der TCP Verbindung.

**Response**

## User List

```json
{
  "type": "user-list",
  "users": {
    "added": [
      {
        "nickname": "fuck",
        "ip": "127.0.0.1",
        "port": 55555
      },
      ...
    ],
    "removed": [
      {
        "nickname": "asdf"
      },
      ...
    ]
  }
}
```

**Registration**

Bei der erstmaligen Registrierung werden alle dem Server bekannten User in
„users-added“ geliefert. „users-removed“ ist hierbei leer.

**Update**

Bei einer Update-Nachricht wird ebenfalls die User List versandt. Es werden
lediglich die geänderten Clients in jeweils „users-added“ und „users-removed“
mitgeliefert.

# Kommunikation von Client zu Client

## UDP Communication Request

Datenteil für die Anfrage: "Please talk to me baby on 1234 one more time.".

Der Erhalt wird durch die Öffnung der TCP Verbindung bestätigt. Wird keine
Verbindung geöffnet, so wird maximal 3 mal die UDP Nachricht versandt, bevor der
Verbindungsaufbau vollständig abgebrochen wird.

## Message Communication

```json
{
  "type": "message",
  "message": "Hey, I'm talking to you over MEME."
}
```

# Broadcast

Der Broadcast wird an den Server per TCP gesendet.

```json
{
  "type": "broadcast",
  "message": "MEME is such a cool protocol!!11!!!"
}
```

Die Nachricht wird dann an alle Clients über die bestehende TCP-Verbindung
verbreitet.
