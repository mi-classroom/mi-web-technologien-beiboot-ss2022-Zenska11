# Hosting - GitHub Pages

## Status

Accepted

## Context

Die Applikation muss online erreichbar sein. Dafür können verschiedene Dienste wie Microsoft Azure, AWS, GitHub pages, ADV Labor, etc. verwendet werden.

## Decision

Als Hosting Dienst wird [GitHub pages](https://pages.github.com/) verwendet.

## Consequences

Jeder mit einem GitHub-Account kann Github Pages verwenden. Es können dort allerdings nur statische Seiten gehostet werden. Für die erste Iteration sollte dies jedoch reichen. Dafür wird einfach der compilierte Code in ein zusätzliches Repository kopiert. Falls im späteren Verlauf festgestellt werden sollte, dass statische Seiten nicht mehr ausreichen, kann immer noch zu einem der anderen Anbieter gewechselt werden.
