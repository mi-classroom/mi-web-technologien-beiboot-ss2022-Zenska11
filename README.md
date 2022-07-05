# Web Technologien // begleitendes Projekt Sommersemester 2022

Zum Modul Web Technologien gibt es ein begleitendes Projekt. Im Rahmen dieses Projekts werden wir von Veranstaltung zu Veranstaltung ein Projekt sukzessive weiter entwickeln und uns im Rahmen der Veranstaltung den Fortschritt anschauen, Code Reviews machen und Entwicklungsschritte vorstellen und diskutieren.

Als organisatorischen Rahmen für das Projekt nutzen wir GitHub Classroom. Inhaltlich befassen wir uns mit der Entwicklung einer kleinen Web-Anwendung für die Bearbeitung von Bildern. Hierbei steht weniger ein professioneller Konzeptions-, Entwurfs- und Entwicklungsprozess im Vordergrund, sondern vielmehr die sukzessive Weiterentwicklung einer Anwendung, das Ausprobieren, Vergleichen, Refactoren und die Freude an lauffähigem Code.

## Inhaber
Marc Kevin Zenzen

Github Pages Link: -> [beiboot-webseite](https://zenska11.github.io/)

### Rieviewer
Valeria Orlova

### Docker
Es muss Docker auf dem aktuellen PC installiert sein (In meinem Fall ist [Docker Desktop](https://www.docker.com/products/docker-desktop/) installiert)

Um die Anwendung in einem Container auszuführen muss folgender Befehl ausgeführt werden:
```
docker run -d -p 8080:80 --rm --name docker-vuejs zenska11/beiboot:latest
```

Mit dem nachfolgenden Befehl kann nachgeschaut werden, ob der zuvor erstellte Container läuft:
```
docker ps
```
Im Bwoser ist die Applikation unter [localhost](lokalhost:8080) erreichbar.

## Projekt klonen und ausführen (ohne docker)
### Project setup
```
npm install
```
### Add JSON
Es muss noch die .json Datei in das Hauptverzeichnis gezogen werden, wenn an der Anwendung weiter entwickelt werden möchte. Die .JSON befindet sich nicht im Repository. 
Diese muss folgenden Namen haben: cda-paintings.json

### Anwendung ausführen
```
npm run serve
```

### Steuerung
* Mit W/S/A/D kann sich bewegt werden. 
* Mit der Leertaste kann nach oben geflogen werden und mit Shift wieder runter.
* Mit der Maus kann die Kamera bewegt werden.
* Mit der Taste ALT kann das Bild eingefroren werden, um aus der Galerie raus zu navigieren. 
* Wenn man von einem geöffneten Link zurück in die Anwendung möchte mus lediglich in das Fenster geklickt werden und man kann sich wieder normal bewegen.

# Aufwände
### 1 Issue
30h

### 2 Issue
2h

### 3 Issue
32h 

### 4 Issue
12h

### 5 Issue
4h