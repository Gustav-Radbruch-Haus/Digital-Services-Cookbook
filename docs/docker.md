# Docker

Docker ist ein Virtualisierungstool, das wir benutzen, um unsere Tools gekapselt zu halten, sodass wir sie jederzeit updaten und ersetzen können, ohne tief in das System unseres Servers eingreifen zu müssen.

Im Gegensatz zu ursprünglichen virtuellen Maschinen stellt Docker die Möglichkeiten des Betriebssystems in so genannte *Container* zur Verfügung. Dadurch werden die zu installierenden Pakete vom eigentlichen System abgekoppelt.

Diese Container entstehen dadurch, dass ein *Image* heruntergeladen und in einer gesonderten Umgebung mit vorselektierten *Einstellungen* als Container initiiert und gestartet werden.

Da Anwendungen immer portbasiert ihre Schnittstellen anbieten (bspw. Apache über Port `80`, Portainer über Port `9000`, Rocket.Chat über Port `3000` usw.), bietet Docker die Möglichkeit, die Ports mappen zu lassen (in Form von außen-innen Bindungen, wie Port `9234` außen und `9000` innen: `9234:9000`). Dadurch kann direkt entschieden werden, auf welchem Port welcher Docker Container erreichbar ist.

Docker bietet zusätzlich die Möglichkeit, Variablen und Verzeichnisse nach außen zu binden. Dadurch kann man ein Verzeichnis auf dem Server auch im Docker-Container verfügbar machen. Üblicherweise wird das bei Konfigurations- und anderen Dateien gemacht, die sonst bei einer Aktualisierung des Docker Containers gelöscht werden.

Ein typischer Befehl, um ein Docker-Container zu installieren und zu starten ist folgender:

```bash
docker run -d\
  -p 8000:8000\
  -p 9000:9000\
  -v /var/run/docker.sock:/var/run/docker.sock\
  -v portainer_data:/data portainer/portainer
```

- Mit `-p` werden die Ports gebunden
- Mit `-v` werden Verzeichnisse gebunden
- Weitere Flags können hier als Variablen gesetzt werden

## Docker Compose

Mit Docker Compose besteht die Möglichkeit, mehrere Docker-Container gleichzeitig anzulegen in einem so genannnten *Stack*. In einem Stack werden üblicherweise Docker-Container zusammen gefasst, die eine Abhängigkeit voneinander haben und daher zusammen eine Service-Umgebung bereitstellen.

### Beispiel: Rocket.Chat
Rocket.Chat kann selber in einem Docker-Container installiert und gestartet werden. Die Nachrichten und Einstellungen von Rocket.Chat hingegen werden gesondert in einer *MongoDB* Datenbank gespeichert.

### How To: add and start a Docker stack

Aus Sicherheitsgründen erstellen wir einen gesonderten Ordner für jede Anwendung, die wir mit Docker starten. Unter `/var/docker/` werden alle Ordner der Container angelegt und die Ordner mit der Verzeichnis-Struktur des Containers verbunden. Als Extra-Sicherheit führen wir alle Docker-Container via `sudo` aus und binden die Nutzerrechte an den Nutzer `docker-data` (UID `999`) der Gruppe `docker-data` (GID `5002`).

!> Sollte die Gruppen-ID von der hier beschriebenen abweichen, kannst du mithilfe des Befehls `getent group docker-data | awk -F: '{printf "Group %s with GID=%d\n", $1, $3}'` über die Konsole die Gruppen-ID der Gruppe `docker-data` herausfinden.

Go to `/var/docker` and add a folder with the name of the tool you want to use (e.g. `rocketchat` for Rocket.Chat). Make sure the folder owner group is `docker-data`.

docker-user Daten:

```
username: docker-data
UID: 999
password: perverse-tycoon-xenia-baseman-plumbing-tracheae
```
