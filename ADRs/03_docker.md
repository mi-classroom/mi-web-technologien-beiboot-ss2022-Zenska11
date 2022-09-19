# Docker

## Status

Accepted

## Context
 
Die Applikation soll für Dritte lokal ausführbar sein. Dafür kann die Anwendung entweder per Vuejs (npm run serve) oder per Docker Container installiert/ aufgesetzt werden. 

## Decision

Es wird sich für die Bereitstellung eines Docker Images entschieden.

## Consequences

Die JSON-Datei befindet sich nicht im Container, da der Docker Container einen Nginx Server beinhaltet und damit nur der Dist Ordner verwendet wird. Durch das Verwenden von Nginx ist zusätzliche Sicherheit geboten.
### Pros 
* Ein Befehl, um die Anwendung lokal ausführen zu können.
* Das Projekt muss nicht geklont werden.
* Es muss nur das Image heruntergeladen werden.
* Nach Ausführung kann der Container und das Image gelöscht werden und es befindet sich nichts mehr von der Anwendung auf dem Rechner.
### Cons
* Benutzer benötigt Grundkenntnisse in Docker.