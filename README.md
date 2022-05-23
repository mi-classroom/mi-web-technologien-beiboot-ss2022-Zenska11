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
docker run -it -p 8081:8080 -d --name docker-vuejs zenska11/beiboot:latest
```

Mit dem nachfolgenden Befehl kann nachgeschaut werden, ob der zuvor erstellte Container läuft:
```
docker ps
```
Im Bwoser ist die Applikation unter [localhost](lokalhost:8081) erreichbar.

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