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

**Response**

## User List

```json
{
  "type": "user-list",
  "users": [
    {
      "nickname": "fuck",
      "ip": "127.0.0.1",
      "port": 55555
    },
    ...
  ],
}
```

**Update**

Bei einer Update-Nachricht wird ebenfalls die User List versandt.

# Kommunikation von Client zu Client

## UDP Communication Request

Datenteil für die Anfrage: "Please talk to me baby on 1234 one more time.".

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
