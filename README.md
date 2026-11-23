# Maki Sandbox – Basic

Eine lokale, betriebssystemunabhängige Testumgebung zum Einstieg in Docker und Container. Die Variante startet einen einzelnen **NGINX-Webserver** mit Docker Compose. Die Anwendung läuft auf dem eigenen Rechner; eine Verbindung zur zentralen Lerninfrastruktur ist nicht erforderlich.

## Funktionen

- NGINX-Webserver in einem Docker-Container
- Aufbau des Images aus dem enthaltenen `Dockerfile`
- Veröffentlichung des Webservers über Port `8080`
- Anpassbare Beispiel-Webseite
- Reproduzierbarer Aufbau über versionierte Projektdateien

## Voraussetzungen

| Komponente | Mindestanforderung / Hinweis |
|---|---|
| Betriebssystem | macOS (Intel oder Apple Silicon), Windows 11 oder eine gängige 64-Bit-Linux-Distribution |
| Arbeitsspeicher | 8 GB; 16 GB empfohlen |
| Prozessor | 4 CPU-Kerne |
| Speicherplatz | 15 GB frei; 25 GB empfohlen |
| Software | Docker mit Docker Compose, Git und ein Terminal |
| Rechte | Administrator- bzw. `sudo`-Rechte für die Installation |
| Internet | Für das Klonen des Repositorys und den erstmaligen Download des NGINX-Basis-Images |

Unter Windows wird Docker Desktop mit WSL2 empfohlen. Unter Linux muss die Docker Engine einschließlich des Compose-Plugins verfügbar sein. Nach dem Download der benötigten Images kann die laufende Sandbox auch ohne Internetverbindung verwendet werden.

## Installation der Voraussetzungen

Installieren Sie Docker Desktop bzw. Docker Engine und Git passend zu Ihrem Betriebssystem. Prüfen Sie anschließend die Installation:

```bash
git --version
docker --version
docker compose version
```

Weiterführende Installationsanleitungen:

- [Docker Desktop](https://docs.docker.com/desktop/)
- [Docker Engine](https://docs.docker.com/engine/install/)
- [Docker Compose](https://docs.docker.com/compose/)
- [Git](https://git-scm.com/doc)

## Sandbox starten

Öffnen Sie ein Terminal oder unter Windows die PowerShell und führen Sie die folgenden Befehle aus:

```bash
git clone https://github.com/MakiGmbH/maki-sandbox-basic.git
cd maki-sandbox-basic
docker compose up -d --build
```

Docker erstellt das lokale Image und startet anschließend den NGINX-Container.

Öffnen Sie im Browser:

**http://localhost:8080**

Dort sollte die Beispielseite mit der Überschrift „Hello World“ erscheinen.

## Betrieb überprüfen

Status des Containers anzeigen:

```bash
docker compose ps
```

Protokollausgaben verfolgen:

```bash
docker compose logs -f
```

Die Protokollanzeige wird mit `Strg+C` beendet. Dadurch wird der Container nicht gestoppt.

## Eigene Inhalte verwenden

Die Beispielseite liegt unter `anwendungen/index.html`. Öffnen Sie diese Datei in einem Texteditor und passen Sie HTML, Text oder CSS an.

Bauen Sie das Image anschließend neu und starten Sie den Container:

```bash
docker compose up -d --build
```

Das `Dockerfile` kopiert den Inhalt des Ordners `anwendungen/` beim Image-Bau in das NGINX-Webverzeichnis. Änderungen an der HTML-Datei werden deshalb erst nach einem erneuten Build im Container sichtbar.

## Änderungen mit Git sichern

Wenn Sie Ihre Änderungen in einem eigenen GitHub-Repository speichern möchten, legen Sie dort zunächst ein leeres Repository an und ändern Sie anschließend die Remote-Adresse:

```bash
git remote set-url origin https://github.com/<BENUTZERNAME>/<REPOSITORY>.git
git status
git add anwendungen/index.html
git commit -m "Beispielseite angepasst"
git push -u origin main
```

Ersetzen Sie die Platzhalter durch Ihren GitHub-Benutzernamen und den Namen Ihres Repositorys. Für den Push wird bei HTTPS ein Personal Access Token (PAT) statt des GitHub-Kontopassworts verwendet. Speichern Sie den Token niemals in einer Datei oder direkt in der Remote-URL.

## Sandbox stoppen und entfernen

Im Repository-Verzeichnis:

```bash
docker compose down
```

Damit werden der Container und das zugehörige Compose-Netzwerk entfernt. Die Projektdateien bleiben erhalten. Mit `docker compose up -d --build` können Sie die Sandbox erneut starten.

## Netzwerk und Sicherheit

Der Webserver wird über Host-Port `8080` veröffentlicht. Die aktuelle Compose-Datei bindet diesen Port ohne Einschränkung an die Host-Schnittstellen. Soll die Webseite ausschließlich auf dem eigenen Rechner erreichbar sein, ändern Sie in `docker-compose.yml` den Port-Eintrag auf:

```yaml
ports:
  - "127.0.0.1:8080:80"
```

Die Sandbox ist eine Lern- und Testumgebung, keine produktive Sicherheitsgrenze. Verwenden Sie keine personenbezogenen, vertraulichen oder produktiven Daten. Speichern Sie keine Passwörter oder Tokens im Repository und prüfen Sie vor jedem Commit mit `git status`, welche Dateien hinzugefügt werden.

## Projektstruktur

```text
maki-sandbox-basic/
├── .gitignore
├── Dockerfile
├── docker-compose.yml
└── anwendungen/
    └── index.html
```

## Fehlerbehebung

| Problem | Mögliche Ursache | Lösung |
|---|---|---|
| `Cannot connect to the Docker daemon` | Docker läuft nicht | Docker Desktop bzw. die Docker Engine starten und den Status erneut prüfen. |
| Port `8080` ist belegt | Ein anderer Dienst verwendet den Port | Den Host-Port in `docker-compose.yml` ändern und den Container neu erstellen. |
| Änderungen an HTML sind nicht sichtbar | Image wurde nicht neu gebaut | `docker compose up -d --build` ausführen und die Seite neu laden. |
| `git push` meldet `Authentication failed` | Passwort statt PAT verwendet oder Token ohne Berechtigung | Einen gültigen PAT mit Schreibrecht auf das Repository verwenden. |

## Weitere Dokumentation

- [Docker-Dokumentation](https://docs.docker.com/)
- [Docker Compose-Dokumentation](https://docs.docker.com/compose/)
- [Git-Dokumentation](https://git-scm.com/doc)

Die weiterführende Projekt- und Kundendokumentation beschreibt die Varianten `cluster` und `gitops` sowie die Hintergründe der Umsetzung.
