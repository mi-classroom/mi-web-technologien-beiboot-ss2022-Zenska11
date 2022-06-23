# Docker

## Status

Accepted

## Context
 
Die Applikation soll für Dritte lokal ausführbar sein. Dafür kann die Anwendung entweder per Vuejs (npm run serve) oder per Docker Container installiert/ aufgesetzt werden. 

## Decision

Es wird sich für die Bereitstellung eines Docker Images entschieden.

## Consequences

JSON befindet sich nicht mehr im Container, da der Docker Container einen Nginx Server beinhaltet und damit nur der Dist Ordner verwendet wird. Durch Nginx ist zusätzlich Sicherheit geboten.
### Pros 
* Ein Befehl, um die Anwendung lokal ausführen zu können.
* Es muss nur das Images runter geladen werden.
* Nach Ausführung kann der Container und das Image gelöscht werden und es befindet sich nichts mehr von der Anwendung auf dem Rechner.
### Cons
* Benutzer benötigt Grundkenntnisse in Docker.