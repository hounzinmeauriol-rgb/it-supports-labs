# Netzwerk-Fehlerdiagnose – Praxisübungen

🇩🇪 Deutsch | [🇬🇧 English Version](README_EN.md)

## Über dieses Projekt

Dieses Repository dokumentiert meine praktischen Übungen zu den Netzwerkgrundlagen für IT-Support und First-Level-Support.

Die Übungen wurden hauptsächlich mit Cisco Packet Tracer sowie mit Windows-Netzwerkbefehlen durchgeführt.

## Praktizierte Kenntnisse

- IPv4-Adressierung
- Subnetzmasken
- Kommunikation im LAN
- Ethernet-Switches
- MAC-Adressen und MAC-Adressentabellen
- Router und Standardgateway
- Kommunikation zwischen verschiedenen Netzwerken
- DHCP-Grundlagen
- DNS-Grundlagen
- Grundlegende Netzwerk-Fehlerdiagnose
- Windows-Netzwerkkonfiguration

---

# Labornetzwerk

Für die Übungen habe ich in Cisco Packet Tracer zwei getrennte lokale Netzwerke aufgebaut.

## LAN 1

- PC0: `192.168.10.10/24`
- PC1: `192.168.10.20/24`
- Standardgateway: `192.168.10.1`
- Switch0

## LAN 2

- PC2: `192.168.20.10/24`
- PC3: `192.168.20.20/24`
- Standardgateway: `192.168.20.1`
- Switch1

## Router

- Schnittstelle zu LAN 1: `192.168.10.1/24`
- Schnittstelle zu LAN 2: `192.168.20.1/24`

Der Router ermöglicht die Kommunikation zwischen den beiden unterschiedlichen IP-Netzwerken.

---

# Troubleshooting-Tickets

## Ticket 01 – Zwei PCs können nicht miteinander kommunizieren

### Problem

Zwei PCs konnten keine Verbindung miteinander herstellen.

### Fehlerdiagnose

Ich überprüfte:

- IPv4-Adressen
- Subnetzmasken
- physische Verbindung
- Zugehörigkeit zum gleichen IP-Netzwerk

Die Verbindung wurde mit folgendem Befehl getestet:

`ping`

### Ursache

Ein PC war für ein anderes IP-Netzwerk konfiguriert.

Beispiel:

PC0:

`192.168.1.10/24`

PC1:

`192.168.2.20/24`

Mit einer `/24`-Subnetzmaske befinden sich diese Geräte in unterschiedlichen Netzwerken.

### Lösung

Ich korrigierte die IP-Konfiguration, sodass sich beide PCs im gleichen Netzwerk befanden.

PC0:

`192.168.1.10/24`

PC1:

`192.168.1.20/24`

Danach war der Ping erfolgreich.

### Erkenntnis

Geräte innerhalb desselben LANs benötigen eine passende IP-Konfiguration, um direkt miteinander kommunizieren zu können.

---

## Ticket 02 – Kommunikation über einen Switch prüfen

### Problem

Mehrere PCs waren mit einem Switch verbunden. Die Kommunikation zwischen den Geräten sollte überprüft werden.

### Fehlerdiagnose

Ich überprüfte:

- Ethernet-Verbindungen
- IP-Konfiguration
- Verbindungsstatus
- Erreichbarkeit mit `ping`
- MAC-Adresstabelle des Switches

Auf dem Cisco-Switch verwendete ich:

`show mac address-table`

### Ergebnis

Der Switch lernte die MAC-Adressen der Geräte dynamisch, nachdem Netzwerkverkehr erzeugt wurde.

### Erkenntnis

Ein Switch verwendet MAC-Adressen, um Ethernet-Frames innerhalb eines LANs an den richtigen Port weiterzuleiten.

Außerdem bedeutet eine aktive physische Verbindung nicht automatisch, dass die IP-Konfiguration korrekt ist.

---

## Ticket 03 – Zwei unterschiedliche LANs können nicht kommunizieren

### Problem

Die Geräte innerhalb der jeweiligen LANs konnten miteinander kommunizieren.

Eine Kommunikation zwischen LAN 1 und LAN 2 war jedoch zunächst nicht möglich.

### Fehlerdiagnose

Ich überprüfte:

1. IP-Adressen der PCs
2. Subnetzmasken
3. Router-Schnittstellen
4. Standardgateways
5. Erreichbarkeit mit `ping`

### Ursache

Die beiden LANs gehörten zu unterschiedlichen IP-Netzwerken und benötigten einen korrekt konfigurierten Router.

### Lösung

Router-Konfiguration:

LAN-1-Schnittstelle:

`192.168.10.1/24`

LAN-2-Schnittstelle:

`192.168.20.1/24`

Standardgateway für LAN 1:

`192.168.10.1`

Standardgateway für LAN 2:

`192.168.20.1`

Nach der korrekten Konfiguration der Router-Schnittstellen und Standardgateways funktionierte die Kommunikation zwischen beiden Netzwerken.

### Erkenntnis

Ein Router verbindet unterschiedliche IP-Netzwerke.

Das Standardgateway eines Endgeräts muss über das lokale Netzwerk erreichbar sein.

---

## Ticket 04 – Falsche Router-Schnittstelle / falsches Standardgateway

### Problem

Ein PC konnte Geräte innerhalb seines eigenen LANs erreichen, jedoch kein Gerät in einem anderen Netzwerk.

### Fehlerdiagnose

Ich überprüfte schrittweise:

- IPv4-Konfiguration
- Subnetzmaske
- Standardgateway
- IP-Adressen der Router-Schnittstellen
- Erreichbarkeit mit `ping`

### Ursache

Eine Router-Schnittstelle war mit einer IP-Adresse aus dem falschen Netzwerk konfiguriert.

Eine Router-Schnittstelle im Netzwerk:

`192.168.10.0/24`

benötigt beispielsweise eine passende Adresse wie:

`192.168.10.1`

und nicht:

`192.168.20.1`

### Lösung

Ich korrigierte die IP-Adresse der Router-Schnittstelle und überprüfte anschließend erneut die Standardgateways der PCs.

### Erkenntnis

Jede Router-Schnittstelle muss passend zum direkt angeschlossenen IP-Netzwerk konfiguriert sein.

---

## Ticket 05 – DHCP-Fehler / APIPA-Adresse

### Problem

Ein Client sollte seine Netzwerkkonfiguration automatisch über DHCP erhalten.

Statt einer gültigen IPv4-Adresse erhielt er jedoch eine Adresse aus dem Bereich:

`169.254.x.x`

### Fehlerdiagnose

Ich überprüfte:

- DHCP-Konfiguration
- DHCP-Dienst
- zugewiesene IP-Adresse
- Subnetzmaske
- Standardgateway
- DHCP-Adresspool

### Diagnose

Eine Adresse aus dem Bereich `169.254.x.x` kann darauf hinweisen, dass ein Windows-Client keine IPv4-Konfiguration von einem DHCP-Server erhalten konnte.

### Lösung

Ich überprüfte die DHCP-Konfiguration und den Adresspool.

Beispiel:

Netzwerk:

`192.168.10.0/24`

Standardgateway:

`192.168.10.1`

Beginn des DHCP-Adressbereichs:

`192.168.10.10`

### Erkenntnis

DHCP kann Clients automatisch mit wichtigen Netzwerkinformationen versorgen.

Eine APIPA-Adresse (`169.254.x.x`) ist ein wichtiger Hinweis bei der Fehlersuche.

---

## Ticket 06 – IP-Adresse erreichbar, Hostname funktioniert nicht

### Problem

Ein Server war über seine IP-Adresse erreichbar:

`ping 192.168.10.2`

Die Kommunikation über den Hostnamen funktionierte jedoch nicht:

`ping www.monserveur.com`

### Erste Hypothese

Da die Kommunikation über die IP-Adresse funktionierte, war die grundlegende Netzwerkverbindung vorhanden.

Der nächste Verdacht war deshalb die DNS-Namensauflösung.

### Fehlerdiagnose

Nützliche Befehle:

`nslookup www.monserveur.com`

`ipconfig /all`

Mit `nslookup` kann die DNS-Namensauflösung überprüft werden.

Mit `ipconfig /all` kann unter anderem kontrolliert werden, welcher DNS-Server auf dem Windows-Client konfiguriert ist.

### Erkenntnis

Wenn eine Ressource über ihre IP-Adresse erreichbar ist, aber nicht über ihren Hostnamen, sollte die DNS-Konfiguration bzw. Namensauflösung überprüft werden.

---

## Ticket 07 – DNS-Konfiguration und Namensauflösung

### Konfiguration

In Cisco Packet Tracer konfigurierte ich einen DNS-Dienst.

DNS-Server:

`192.168.10.2`

Anschließend erstellte ich einen A-Record:

`www.monserveur.com -> 192.168.10.2`

Der Client wurde so konfiguriert, dass er folgenden DNS-Server verwendet:

`192.168.10.2`

### Test

Ich führte folgenden Test durch:

`ping www.monserveur.com`

Der Hostname wurde erfolgreich zu:

`192.168.10.2`

aufgelöst und der Server antwortete auf die Ping-Anfragen.

### Erkenntnis

DNS ermöglicht die Auflösung von Hostnamen in IP-Adressen.

Der Client muss einen DNS-Server kennen, der die benötigte Namensauflösung durchführen kann.

---

# Verwendete Befehle

## Windows

`ipconfig`

Zeigt die grundlegende IP-Konfiguration an.

`ipconfig /all`

Zeigt detaillierte Netzwerkinformationen wie IP-Adresse, Subnetzmaske, Standardgateway, DHCP- und DNS-Informationen an.

`ping <Ziel>`

Überprüft die IP-Erreichbarkeit eines Ziels.

`nslookup <Hostname>`

Überprüft die DNS-Namensauflösung.

## Cisco IOS

`show mac address-table`

Zeigt die vom Switch gelernten MAC-Adressen und die zugehörigen Ports an.

---

# Meine Vorgehensweise bei der Fehlerdiagnose

Bei den Übungen habe ich eine systematische Vorgehensweise trainiert:

1. Problem des Benutzers bestätigen und verstehen.
2. Physische bzw. Netzwerkverbindung überprüfen.
3. IP-Adresse und Subnetzmaske überprüfen.
4. Lokale Erreichbarkeit testen.
5. Standardgateway überprüfen.
6. Erreichbarkeit der Ziel-IP testen.
7. DNS überprüfen, wenn die IP erreichbar ist, aber der Hostname nicht funktioniert.
8. Änderungen kontrolliert und schrittweise durchführen.
9. Nach jeder Änderung erneut testen.

---

# Aktueller Lernstand

Diese Übungen dokumentieren meine praktischen Netzwerkgrundlagen für den Einstieg in IT-Support und First-Level-Support.

Als nächste Themen bearbeite ich Windows-Fehlerdiagnose, Linux/SSH, Ports, Dienste und weitere Helpdesk-Szenarien.
