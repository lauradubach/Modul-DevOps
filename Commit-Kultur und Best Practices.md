# Commits – Kurzüberblick

## Zweck
- Speichern von **sinnvollen Änderungen** in kleinen Gruppen  
- Jeder Commit hat eine **einzigartige SHA/Hash-ID**  
- Enthält Infos zu:
  - Änderungen
  - Zeitpunkt
  - Autor

## Commit-Nachrichten
- Kurze Beschreibung der Änderungen verpflichtend  
- Optional: **Co-Autoren** hinzufügen oder im Namen einer Organisation committen  
- Web-Interface: automatisches Sign-off, falls aktiviert

## Branches und Tags
- Labels zeigen, auf welchem Branch ein Commit ist  
- Tags zeigen Releases, die den Commit enthalten  
- Bei nicht-default Branch → zeigt alle Branches mit diesem Commit  
- Unmerged Pull Requests → Link zum PR

## Dateibaum verwenden
- Dateiänderungen innerhalb eines Commits über **File Tree** navigieren  
- Einzelne Dateien diffen und prüfen  
- Filter möglich über Suchfeld für Dateipfade  
- Hinweis: File Tree wird **nicht angezeigt**, wenn Bildschirm zu schmal oder nur eine Datei betroffen ist  

## Kurzfazit
- Commits sind **Snapshots** von Änderungen  
- Helfen bei Versionskontrolle, Zusammenarbeit und Nachvollziehbarkeit  
- Branch- und Tag-Labels erleichtern Navigation und Release-Zuordnung

# Conventional Commits 1.0.0 – Kurzüberblick

## Zweck
- Leichte Konvention für **Commit-Nachrichten**
- Erleichtert **automatisierte Tools**, CHANGELOGs und SemVer
- Kommuniziert **Features, Fixes und Breaking Changes**

## Struktur einer Commit-Nachricht

<type>\[optional scope]\[!]: <description>

\[optional body]

\[optional footer(s)]

### Typen
- **feat:** neues Feature → MINOR Release  
- **fix:** Bugfix → PATCH Release  
- **BREAKING CHANGE:** große Änderung → MAJOR Release  
- Weitere Typen möglich: `docs`, `chore`, `ci`, `style`, `refactor`, `perf`, `test`, etc.

### Scope
- Optional, in Klammern: `feat(parser): …`

### Fußzeilen
- Optional, z.B. `BREAKING CHANGE: …` oder `Refs: #123`  
- Unterstützt Git-Trailer-Format  

### Beispiele
- `feat: add new login API`  
- `fix(auth)!: change password validation`  
- `chore!: drop Node 6 support`  
- `feat(lang): add Polish language`  

## Regeln
- Commit muss **Typ + optional Scope + ":" + Beschreibung** enthalten  
- Beschreibung folgt direkt nach `:`  
- Body optional, beginnt nach leerer Zeile  
- Fußzeilen optional, leerzeilengetrennt nach Body  
- BREAKING CHANGE in Footer oder Typ/Scope (!)  

## Vorteile
- Automatische CHANGELOG-Erstellung  
- SemVer Version-Bumping  
- Klare Kommunikation von Änderungen  
- Unterstützt strukturierte Beiträge und schnelle Zusammenarbeit  

## FAQ – Kurz
- Typen können beliebige Groß-/Kleinschreibung haben  
- Mehrere Änderungen → mehrere Commits  
- Fehlerhafte Typen → Rebase vor Release, ansonsten einfach ignorieren  
- Contributor müssen nicht strikt folgen; Lead Maintainer kann Nachrichten säubern  
- Revert Commits → flexibel, empfohlen `revert:` mit SHA-Referenzen  

# Atomic Git Commits

## Definition
- Kleinste mögliche Änderung → nur **eine Sache**
- Beschreibung in **einem Satz**
- Tests immer **grün** → vollständig & klein

## Vorteile
1. **Reversibel**: `git revert` möglich
2. **Saubere Historie**: einfaches Debuggen
3. **Einfache PR-Reviews**
4. **Besserer Workflow**: große Aufgaben in kleine Schritte zerlegen

## Fazit
- Wissen reicht nicht → **anwenden**
- Kleine, abgeschlossene Schritte = **kontrolliertes Arbeiten**