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

!> Wir arbeiten hauptsächlich mit Docker Compose, da wir üblicherweise zusammenhängende Service-Netzwerke haben.

## Docker Compose

Mit Docker Compose besteht die Möglichkeit, mehrere Docker-Container gleichzeitig anzulegen in einem so genannnten *Stack*. In einem Stack werden üblicherweise Docker-Container zusammen gefasst, die eine Abhängigkeit voneinander haben und daher zusammen eine Service-Umgebung bereitstellen.

### Beispiel: Rocket.Chat
Rocket.Chat kann selber in einem Docker-Container installiert und gestartet werden. Die Nachrichten und Einstellungen von Rocket.Chat hingegen werden gesondert in einer *MongoDB* Datenbank gespeichert.

## Getting started

### Vorbereitungen: Docker-Compose ohne `sudo` ausführen
Siehe <https://en.wikipedia.org/wiki/Principle_of_least_privilege> für mehr Infos dazu.

1. Logge dich ein via `ssh`
2. Füge deinen Nutzer der `docker` und der `docker-data` Nutzergruppe hinzu:
```
sudo usermod -aG docker $USER
```
3. Logge dich aus und wieder ein
4. Teste, ob du nun Docker-Container ohne `sudo` ausführen kannst:
```
docker run hello-world
```

### Docker Stack via Docker Compose erstellen und starten

1. Lege einen Ordner mit dem jeweiligen Package-Namen unter `/var/docker/` an.
2. Gib dem Ordner die Nutzerrechte, die Docker benötigt, um Dateien in den Ordner zu schreiben:
```bash
sudo chown -R docker-data:docker-data ORDNERNAME
```
3. Suche für deinen Service eine vordefinierte `docker-compose.yml` Datei. Meistens wird sie vom Service-Entwickler selber bereitgestellt.
4. Verändere alle vorkommenden Pfade, indem du sie auf `/var/docker/ORDNERNAME` weiterleitest (bspw: `/var/docker/rocketchat/config:/usr/local/config`)
!> Sollte der Docker-Container dennoch mit `root`-Rechten ausgeführt werden, füge `privileged: true` in die Konfiguration hinzu
5. Starte den Docker-Container mit `docker-compose up -d`. Das `-d` ist wichtig, um die Docker-Instanz vom aktuellen Nutzer zu trennen ("detach").

## Tipps und Tricks

- Tipps und Interessantes zu Docker-Sicherheit: <https://towardsdatascience.com/top-20-docker-security-tips-81c41dd06f57>
