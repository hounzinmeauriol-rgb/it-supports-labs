# Linux, SSH & Netzwerkdienste – Troubleshooting Labs

🇩🇪 Deutsch | [🇬🇧 English Version](README_EN.md)

Praktische Übungen zu Linux-Support, SSH, Netzwerkdiensten, Berechtigungen sowie DNS- und DHCP-Fehlerdiagnose.

Ziel dieser Labs war es, typische First-Level-Support-Probleme systematisch zu analysieren und dabei nicht nur die Erreichbarkeit einer Maschine, sondern auch Dienste, Ports, Logs und Berechtigungen zu überprüfen.

---

## Ticket 1 – SSH-Verbindung funktioniert nicht über Port 22

### Problem
Eine SSH-Verbindung zu einem Linux-Server über den Standardport 22 funktioniert nicht.

### Diagnose

Zuerst wurden die Listening Ports überprüft:

```bash
ss -tuln
```

Dabei wurde festgestellt, dass kein Dienst auf Port 22 lauscht, aber Port 2222 geöffnet ist.

Für zusätzliche Informationen über den zugehörigen Prozess:

```bash
sudo ss -tulnp
```

Anschließend wurde die SSH-Konfiguration überprüft:

```bash
grep -i "port" /etc/ssh/sshd_config
```

Die Konfiguration zeigte:

```text
Port 2222
```

### Lösung

Die SSH-Verbindung wurde mit dem konfigurierten Port hergestellt:

```bash
ssh -p 2222 user@192.168.10.50
```

### Verwendete Befehle
`ss -tuln`  
`sudo ss -tulnp`  
`grep -i "port" /etc/ssh/sshd_config`  
`ssh -p 2222 user@192.168.10.50`

---

## Ticket 2 – SSH-Service erscheint als inaktiv, Port 22 ist aber geöffnet

### Problem

Der SSH-Service wurde überprüft:

```bash
systemctl status ssh
```

Ergebnis:

```text
Active: inactive (dead)
TriggeredBy: ssh.socket
```

Trotzdem war eine SSH-Verbindung möglich.

### Diagnose

Die Listening Ports und Prozesse wurden überprüft:

```bash
sudo ss -tulnp
```

Port 22 wurde von `systemd` bereitgestellt.

Danach wurde eine lokale SSH-Verbindung getestet:

```bash
ssh eddy@localhost
```

Nach der Verbindung wurde der Service erneut überprüft:

```bash
systemctl status ssh
```

Jetzt war der SSH-Service aktiv.

### Lösung

Es wurde erkannt, dass SSH über systemd Socket Activation gestartet werden kann. Ein inaktiver `ssh.service` bedeutet deshalb nicht automatisch, dass keine SSH-Verbindung möglich ist.

### Verwendete Befehle
`systemctl status ssh`  
`sudo ss -tulnp`  
`ssh eddy@localhost`

---

## Ticket 3 – SSH-Anmeldung schlägt fehl

### Problem

Der Server ist erreichbar und SSH läuft, aber ein Benutzer kann sich nicht anmelden.

### Diagnose

Die letzten SSH-Logs wurden überprüft:

```bash
journalctl -u ssh -n 15
```

Die Logs zeigten unter anderem:

```text
authentication failure
Failed password
```

Dadurch konnte das Problem auf die Authentifizierung eingegrenzt werden.

Der Status des Benutzerpassworts kann zusätzlich überprüft werden:

```bash
sudo passwd -S paul
```

Ein Status `L` weist auf ein gesperrtes Passwort hin, während `P` ein verwendbares Passwort kennzeichnet.

### Lösung

Nach Prüfung der Berechtigung bzw. Unternehmensrichtlinie kann ein gesperrtes Passwort bei Bedarf entsperrt werden:

```bash
sudo passwd -u paul
```

Danach wird die SSH-Anmeldung erneut getestet.

### Verwendete Befehle
`journalctl -u ssh -n 15`  
`sudo passwd -S paul`  
`sudo passwd -u paul`  
`ssh user@server`

---

## Ticket 4 – Benutzer hat keinen Zugriff auf eine Linux-Datei

### Problem

Ein Benutzer erhält beim Zugriff auf eine Datei:

```text
Permission denied
```

Beispiel:

```text
-rw-r----- root root rapport.txt
```

### Diagnose

Die Dateiberechtigungen und Eigentümer wurden überprüft:

```bash
ls -l rapport.txt
```

Anschließend wurden die Gruppen des Benutzers überprüft:

```bash
groups eddy
```

Dadurch konnte festgestellt werden, ob der Benutzer Eigentümer der Datei ist, zur Dateigruppe gehört oder unter die Berechtigungen für „others" fällt.

### Lösung

Je nach Ursache wurden unterschiedliche Lösungen getestet.

Eigentümer und Gruppe ändern:

```bash
sudo chown eddy:eddy rapport.txt
```

Schreibrecht für die Gruppe hinzufügen:

```bash
chmod g+w projet.txt
```

Leserecht für „others" entfernen:

```bash
chmod o-r rapport.txt
```

Benutzer zu einer benötigten zusätzlichen Gruppe hinzufügen:

```bash
sudo usermod -aG compta eddy
```

Nach einer Änderung der Gruppenmitgliedschaft muss sich der Benutzer gegebenenfalls neu anmelden, damit die neue Gruppenmitgliedschaft wirksam wird.

### Verwendete Befehle
`ls -l`  
`groups eddy`  
`sudo chown eddy:eddy rapport.txt`  
`chmod g+w projet.txt`  
`chmod o-r rapport.txt`  
`sudo usermod -aG compta eddy`

---

## Ticket 5 – DNS-Auflösung funktioniert nicht

### Problem

Ein Benutzer kann keine Webseiten über Domainnamen erreichen.

Die grundlegende IP-Verbindung funktioniert jedoch.

### Diagnose

Zuerst wurde die IP-Konnektivität getestet:

```bash
ping 8.8.8.8
```

Der Ping war erfolgreich.

Danach wurde die DNS-Auflösung getestet:

```bash
nslookup google.com
```

Ergebnis:

```text
DNS request timed out
```

Der konfigurierte DNS-Server war `192.168.10.2`.

Die Erreichbarkeit des DNS-Servers wurde getestet:

```bash
ping 192.168.10.2
```

Der Server war erreichbar.

Auf dem DNS-Server wurde anschließend der DNS-Dienst überprüft:

```bash
systemctl status bind9
```

Ergebnis:

```text
Active: inactive (dead)
```

Zusätzlich wurde überprüft, ob ein Dienst auf DNS-Port 53 lauscht:

```bash
sudo ss -tulnp
```

Es war kein Listener auf Port 53 vorhanden.

### Lösung

Der DNS-Dienst wurde gestartet:

```bash
sudo systemctl start bind9
```

Danach:

```bash
systemctl status bind9
sudo ss -tulnp
```

Port 53 war anschließend wieder verfügbar.

Zum Schluss wurde die ursprüngliche DNS-Auflösung erneut getestet:

```bash
nslookup google.com
```

Die Namensauflösung funktionierte wieder.

### Verwendete Befehle
`ping 8.8.8.8`  
`nslookup google.com`  
`ping 192.168.10.2`  
`systemctl status bind9`  
`sudo systemctl start bind9`  
`sudo ss -tulnp`

---

## Ticket 6 – Windows-PC erhält eine APIPA-Adresse

### Problem

Ein Windows-PC erhält keine normale Unternehmens-IP-Adresse.

`ipconfig /all` zeigt:

```text
DHCP Enabled: Yes
IPv4 Address: 169.254.87.34
Subnet Mask: 255.255.0.0
Default Gateway:
DNS Servers:
```

### Diagnose

Eine Adresse im Bereich `169.254.x.x` ist ein Hinweis darauf, dass der PC für DHCP konfiguriert ist, aber keine gültige DHCP-Konfiguration erhalten konnte.

Eine neue DHCP-Konfiguration wurde angefordert:

```cmd
ipconfig /release
ipconfig /renew
```

`ipconfig /renew` konnte zunächst keinen DHCP-Server erreichen.

Andere Mitarbeiter im gleichen Unternehmen erhielten jedoch normale DHCP-Adressen.

Danach wurde die lokale Netzwerkverbindung überprüft.

Der betroffene PC war mit folgendem WLAN verbunden:

```text
ENTREPRISE-GUEST
```

Die Kollegen waren mit folgendem WLAN verbunden:

```text
ENTREPRISE-STAFF
```

### Lösung

Der PC wurde mit dem richtigen WLAN `ENTREPRISE-STAFF` verbunden.

Danach:

```cmd
ipconfig /renew
```

Der PC erhielt anschließend eine gültige Konfiguration, zum Beispiel:

```text
IPv4 Address: 192.168.10.42
Default Gateway: 192.168.10.1
```

Die neue Konfiguration wurde abschließend überprüft:

```cmd
ipconfig /all
```

### Verwendete Befehle
`ipconfig /all`  
`ipconfig /release`  
`ipconfig /renew`

---

## Ticket 7 – Netzwerkpfad mit tracert untersuchen

### Problem

Der Netzwerkpfad zu einem entfernten Ziel soll untersucht werden.

### Diagnose

Unter Windows wurde folgender Befehl verwendet:

```cmd
tracert 8.8.8.8
```

Dadurch konnten die einzelnen Hops zwischen dem eigenen PC und dem Ziel betrachtet werden.

Ein Hop zeigte:

```text
*    *    *
```

Spätere Hops und das Ziel antworteten jedoch weiterhin.

### Ergebnis

`* * *` bedeutet nicht automatisch, dass der entsprechende Router ausgefallen ist. Ein Router kann beispielsweise die verwendeten Diagnosepakete nicht beantworten und den normalen Datenverkehr trotzdem weiterleiten.

### Verwendete Befehle
`tracert 8.8.8.8`  
`ping 8.8.8.8`
