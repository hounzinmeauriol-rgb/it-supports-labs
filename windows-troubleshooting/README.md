
# Windows-Fehlerdiagnose – Praxisübungen

🇩🇪 Deutsch | [🇬🇧 English Version](README_EN.md)

## Überblick

Dieses Repository dokumentiert praktische Übungen zur Fehlerdiagnose im Windows-Umfeld.

Ziel ist es, typische First-Level-Support-Probleme systematisch zu analysieren, die Ursache einzugrenzen und geeignete Lösungen anzuwenden.

---

## Ticket 1 – Langsamer Windows-PC

### Problem

Ein Benutzer meldet, dass sein Windows-PC ungewöhnlich langsam ist.

### Diagnose

Zuerst wird der **Task-Manager** mit `Ctrl + Shift + Esc` geöffnet.

Anschließend werden folgende Ressourcen überprüft:

- CPU
- Arbeitsspeicher
- Datenträger
- laufende Prozesse

Die Prozesse werden nach Ressourcenverbrauch untersucht.

### Lösung

Ein Prozess mit hoher Ressourcenauslastung wird zuerst identifiziert und überprüft.

Eine Anwendung sollte nicht sofort beendet werden, nur weil sie viele Ressourcen verwendet.

Falls notwendig:

1. Arbeit des Benutzers speichern
2. Anwendung normal schließen
3. Falls sie nicht reagiert, im Task-Manager beenden
4. Anwendung erneut starten
5. System erneut überprüfen

### Verwendete Tools

- Task-Manager
- `tasklist`

---

## Ticket 2 – Anwendung reagiert nicht

### Problem

Eine Anwendung, zum Beispiel Excel, reagiert nicht mehr. Der Benutzer hat möglicherweise nicht gespeicherte Arbeit.

### Diagnose

Im Task-Manager wird überprüft, ob die Anwendung noch läuft und nicht mehr reagiert.

### Lösung

Zuerst wird kurz gewartet und versucht, die Anwendung normal zu schließen.

Falls die Anwendung weiterhin nicht reagiert, wird der Benutzer darauf hingewiesen, dass nicht gespeicherte Daten verloren gehen können.

Danach kann die Anwendung über:

**Task-Manager → Task beenden**

geschlossen werden.

Nach dem Neustart der Anwendung wird überprüft, ob eine automatische Wiederherstellung verfügbar ist.

### Verwendete Tools

- Task-Manager

---

## Ticket 3 – Datenträger fast voll

### Problem

Auf einem Windows-PC sind beispielsweise nur noch 2,3 GB von 500 GB verfügbar.

### Diagnose

Unter:

**Einstellungen → System → Speicher**

wird überprüft, welche Kategorien den Speicherplatz verwenden.

Zum Beispiel:

- Installierte Anwendungen
- Temporäre Dateien
- Benutzerdaten
- Sonstige Dateien

### Lösung

Mögliche Maßnahmen:

1. Temporäre Dateien überprüfen
2. Windows-Bereinigungsempfehlungen verwenden
3. Große Anwendungen identifizieren
4. Nicht mehr benötigte Anwendungen nach Rücksprache deinstallieren

Benutzer- oder Systemdateien sollten nicht ohne vorherige Prüfung gelöscht werden.

Nach der Bereinigung wird der freie Speicherplatz erneut überprüft.

### Verwendete Tools

- Windows-Speicherverwaltung
- Installierte Apps

---

## Ticket 4 – Benutzer hat keine Berechtigung

### Problem

Ein Benutzer versucht eine Aktion auszuführen, für die Administratorrechte erforderlich sind.

### Diagnose

Zuerst wird der aktuell angemeldete Benutzer identifiziert:

```cmd
whoami
```

Lokale Benutzerkonten können angezeigt werden mit:

```cmd
net user
```

Informationen zu einem bestimmten Benutzer:

```cmd
net user BENUTZERNAME
```

Damit können unter anderem die Gruppenmitgliedschaften überprüft werden.

Beispiele:

- Benutzer
- Administratoren

### Lösung

Fehlende Administratorrechte sollten nicht einfach dadurch gelöst werden, dass der Benutzer zur Administratorengruppe hinzugefügt wird.

Die Berechtigungen müssen entsprechend den IT-Richtlinien des Unternehmens behandelt oder an die zuständige Stelle eskaliert werden.

### Verwendete Tools und Befehle

- `whoami`
- `net user`
- `net user BENUTZERNAME`

---

## Ticket 5 – WLAN funktioniert nicht / WLAN-Adapter fehlt

### Problem

Ein Benutzer kann sich nicht mehr mit dem WLAN verbinden oder der WLAN-Adapter wird nicht wie erwartet angezeigt.

### Diagnose

Der **Geräte-Manager** wird geöffnet.

Danach:

**Geräte-Manager → Netzwerkadapter**

Der physische WLAN-Adapter wird identifiziert.

Unter:

**Eigenschaften → Allgemein → Gerätestatus**

werden mögliche Warnungen und Fehlercodes überprüft.

### Lösung

Abhängig vom Ergebnis können folgende Punkte untersucht werden:

- Gerätestatus
- Treiberstatus
- Fehlercodes
- kürzliche Änderungen am System

Geräte oder Treiber sollten nicht ohne vorherige Diagnose entfernt werden.

### Verwendete Tools

- Geräte-Manager

---

## Ticket 6 – Keine automatische IP-Adresse

### Problem

Ein Computer erhält nicht automatisch die erwartete IP-Konfiguration.

### Diagnose

Zuerst wird die Netzwerkkonfiguration überprüft:

```cmd
ipconfig /all
```

Danach werden die Windows-Dienste geöffnet:

```text
Windows + R
services.msc
```

Der Dienst **DHCP-Client** wird überprüft.

Dabei werden insbesondere kontrolliert:

- Status
- Starttyp

### Lösung

Wenn der DHCP-Client gestoppt ist, kann er gestartet werden.

Falls der Dienst bereits läuft und ein Problem vermutet wird, kann er neu gestartet werden.

Danach wird die Netzwerkkonfiguration erneut überprüft:

```cmd
ipconfig /all
```

### Verwendete Tools und Befehle

- `services.msc`
- `ipconfig /all`

---

## Ticket 7 – Eine Anwendung startet nicht mehr

### Problem

Eine bestimmte Anwendung lässt sich unter Windows nicht mehr starten.

### Diagnose

Da nur eine einzelne Anwendung betroffen ist, wird nicht sofort von einem Windows-Systemfehler ausgegangen.

Folgende Schritte werden durchgeführt:

1. Anwendung erneut starten
2. Task-Manager auf hängende Prozesse prüfen
3. Hängenden Prozess gegebenenfalls beenden
4. Computer neu starten
5. Updates oder kürzliche Änderungen überprüfen
6. Anwendung gegebenenfalls reparieren oder neu installieren

### System File Checker

Bei einem begründeten Verdacht auf beschädigte Windows-Systemdateien kann zusätzlich verwendet werden:

```cmd
sfc /scannow
```

`SFC` ist jedoch nicht der erste Schritt, wenn nur eine einzelne Anwendung nicht funktioniert.

### Verwendete Tools

- Task-Manager
- Installierte Apps
- Windows Update
- `sfc /scannow`

---

## Ticket 8 – WLAN verbunden, aber Webseiten funktionieren nicht

### Problem

Ein Benutzer meldet:

> „Ich bin mit dem WLAN verbunden, aber Webseiten funktionieren nicht. Meine Kollegen haben Internet."

### Schritt 1 – Netzwerkkonfiguration prüfen

```cmd
ipconfig /all
```

Beispiel:

```text
IPv4-Adresse:      192.168.0.54
Subnetzmaske:      255.255.255.0
Standardgateway:   192.168.0.1
DHCP aktiviert:    Ja
DNS-Server:        192.168.0.250
```

Der Computer besitzt eine lokale IP-Konfiguration.

Zu diesem Zeitpunkt wissen wir noch **nicht**, ob der konfigurierte DNS-Server korrekt ist.

### Schritt 2 – Gateway testen

```cmd
ping 192.168.0.1
```

Ergebnis:

```text
Antwort erhalten
```

Damit funktioniert die Kommunikation mit dem lokalen Gateway.

### Schritt 3 – Internetverbindung über IP testen

```cmd
ping 8.8.8.8
```

Ergebnis:

```text
Antwort erhalten
```

Damit funktioniert in diesem Szenario die Internetverbindung über eine IP-Adresse.

### Schritt 4 – DNS-Auflösung testen

```cmd
nslookup google.com
```

Ergebnis:

```text
Server:  Unknown
Address: 192.168.0.250

DNS request timed out.
```

Damit wurde festgestellt, dass die DNS-Namensauflösung nicht funktioniert.

Das bedeutet jedoch noch nicht automatisch, dass `192.168.0.250` eine falsche Adresse ist. Der DNS-Server könnte beispielsweise auch nicht erreichbar oder ausgefallen sein.

### Schritt 5 – DNS-Konfiguration vergleichen

Da die Computer der Kollegen funktionieren, wird die DNS-Konfiguration mit einem funktionierenden Computer beziehungsweise mit der vorgesehenen Unternehmenskonfiguration verglichen.

Beispiel:

```text
Betroffener PC:
DNS-Server: 192.168.0.250

Funktionierender PC:
DNS-Server: 192.168.0.10
```

Nach Überprüfung der vorgesehenen Netzwerkkonfiguration wird festgestellt, dass:

```text
192.168.0.10
```

der korrekte DNS-Server ist.

Damit ist die falsche DNS-Konfiguration auf dem betroffenen PC identifiziert.

### Lösung

Die DNS-Konfiguration wird entsprechend korrigiert.

Danach wird erneut getestet:

```cmd
nslookup google.com
```

Der DNS-Server liefert nun eine IP-Adresse für `google.com`.

Anschließend wird der Zugriff auf die Webseite erneut getestet.

**Ticket gelöst.**

### Troubleshooting-Logik

```text
ipconfig /all
      ↓
Gateway testen
      ↓
Internet über IP testen
      ↓
DNS-Auflösung mit nslookup testen
      ↓
DNS-Konfiguration mit funktionierendem PC vergleichen
      ↓
Fehler identifizieren
      ↓
Konfiguration korrigieren
      ↓
Erneut testen
```

---

## Weitere verwendete Windows-Befehle

### Systeminformationen anzeigen

```cmd
systeminfo
```

Zeigt Informationen über Windows, Computer, Architektur und Systemkonfiguration.

### Laufende Prozesse anzeigen

```cmd
tasklist
```

Zeigt die aktuell laufenden Prozesse und deren Prozess-IDs.

### Aktuellen Benutzer anzeigen

```cmd
whoami
```

### Lokale Benutzer anzeigen

```cmd
net user
```

### Benutzerinformationen anzeigen

```cmd
net user BENUTZERNAME
```

### Windows-Systemdateien überprüfen

```cmd
sfc /scannow
```

---

## Verwendete Windows-Tools

- Task-Manager
- Geräte-Manager
- Windows-Dienste (`services.msc`)
- Windows Update
- Speicherverwaltung
- Installierte Apps
- Eingabeaufforderung

---

## Verwendete Befehle

```text
ipconfig
ipconfig /all
ping
nslookup
whoami
net user
net user BENUTZERNAME
tasklist
systeminfo
sfc /scannow
```

---

## Lernziele

Durch diese Übungen wurden grundlegende praktische Kenntnisse in folgenden Bereichen aufgebaut:

- First-Level IT Support
- Windows-Fehlerdiagnose
- Prozess- und Ressourcenanalyse
- Benutzer- und Berechtigungsprüfung
- Windows-Dienste
- Geräte- und Netzwerkadapterdiagnose
- grundlegende Netzwerkdiagnose
- DHCP-Fehlerdiagnose
- DNS-Fehlerdiagnose
- systematische Eingrenzung von Fehlerursachen

Der Schwerpunkt liegt auf einem strukturierten Troubleshooting-Prozess:

**Problem verstehen → Informationen sammeln → Ursache eingrenzen → geeignete Maßnahme durchführen → Ergebnis testen**
