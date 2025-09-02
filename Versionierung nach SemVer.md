# Semantic Versioning (SemVer) – Kurzüberblick

## Warum SemVer?
SemVer löst zwei Probleme bei Abhängigkeiten:
- **Version Lock**: Wenn Bibliotheken auf exakt dieselbe Version angewiesen sind → blockiert Upgrades  
- **Version Promiscuity**: Unbedachtes Upgrade führt zu **Breaking Changes** in Abhängigkeiten  

➡️ SemVer macht durch klare **Versionsregeln** sichtbar, wann Änderungen **sicher** oder **kritisch** sind.  

## Aufbau

```
MAJOR.MINOR.PATCH\[-TAG]\[+BUILDMETADATA]
```

- **MAJOR** → erhöht bei **Breaking Changes**  
- **MINOR** → neue Features, **abwärtskompatibel**  
- **PATCH** → Bugfixes ohne Änderungen am Verhalten  
- **TAG (optional)** → Vorab-Version (z. B. alpha, beta)  
- **BUILDMETADATA (optional)** → Zusatzinfos (z. B. Build-Nummern)  

⚠️ Pro Release wird **nur eine Zahl** erhöht, alle kleineren zurückgesetzt (z. B. `2.0.0`).

## SemVer in GitVersion
- **GitVersion** berechnet automatisch die nächste SemVer-Version  
- **GitFlow**: beim Taggen von `main` wird die **MINOR** hochgesetzt  
- **GitHubFlow**: erhöht typischerweise den **PATCH**  
- Unterstützt verschiedene Variablen:  
  - **SemVer** = `{major}.{minor}.{patch}-{tag}`  
  - **FullSemVer** = `{major}.{minor}.{patch}-{tag}+{buildmetadata}`  

## Kurzfazit
SemVer macht Versionsnummern **vorhersehbar & zuverlässig**.  
- MAJOR = Breaking  
- MINOR = Features  
- PATCH = Bugfixes  
Tools wie **GitVersion** helfen, diese Regeln automatisch einzuhalten.

# Git Tagging – Kurzüberblick

## Zweck
- Tags markieren **wichtige Punkte** in der Historie, z. B. Releases (`v1.0`, `v2.0`) 

## Tags auflisten
```bash
git tag              # alle Tags alphabetisch
git tag -l "v1.8*"   # Tags nach Muster filtern
```

## Tag-Arten

* **Annotated Tag** → enthält Nachricht, Tagger, Datum, kann signiert werden

```bash
git tag -a v1.4 -m "Version 1.4"
git show v1.4
```

* **Lightweight Tag** → nur Zeiger auf Commit, keine zusätzlichen Infos

```bash
git tag v1.4-lw
git show v1.4-lw
```

## Tags nachträglich erstellen

```bash
git tag -a v1.2 9fceb02  # Commit per Hash taggen
```

## Tags pushen

```bash
git push origin v1.5       # einzelnes Tag
git push origin --tags     # alle Tags
```

* Standardmäßig werden **beide Tag-Arten** übertragen
* `--follow-tags` → nur annotated Tags pushen

## Tags löschen

* Lokal:

```bash
git tag -d v1.4-lw
```

* Remote:

```bash
git push origin :refs/tags/v1.4-lw
# oder
git push origin --delete v1.4-lw
```

## Tags auschecken

```bash
git checkout v2.0.0       # "detached HEAD"-Zustand
```

* Änderungen in detached HEAD bleiben **außerhalb von Branches**
* Um Änderungen zu behalten, neuen Branch erstellen:

```bash
git checkout -b version2 v2.0.0
```

## Kurzfazit

* Tags markieren Releases
* Annotated Tags enthalten zusätzliche Infos, lightweight nur Commit-Zeiger
* Tags müssen **explizit gepusht** werden, und beim Auschecken vorsichtig im detached HEAD-Zustand arbeiten

# GitHub Releases – Kurzüberblick

## Zweck
- Releases **packen Software** zusammen mit **Release Notes** und **Binärdateien**  
- Basieren auf **Git-Tags**, markieren einen bestimmten Punkt in der Repo-Historie  
- Tags- und Release-Datum können unterschiedlich sein

## Zugriff & Rechte
- **Leserechte** → Releases ansehen und vergleichen  
- **Schreibrechte** → Releases verwalten

## Release Notes
- Manuell erstellen oder automatisch aus Vorlage generieren  
- Detaillierte Infos zu jedem Release-Asset werden angezeigt

## Download
- GitHub erstellt automatisch **ZIP** und **Tarball** für den Release-Tag  
- Admins können entscheiden, ob **Git LFS**-Objekte enthalten sind  

## Sicherheit
- Sicherheitsfixes → **Security Advisory** veröffentlichen  
- GitHub kann **Dependabot Alerts** an betroffene Repositories senden  

## Statistiken & API
- **Releases API** liefert z. B. Downloadzahlen  
- Bis zu **1000 Assets pro Release**, max. **2 GiB pro Datei**  
- Keine Limits für **Gesamtgröße** oder **Bandbreite**

# Releases in einem Repository verwalten

## Wer kann Releases verwalten?
- **Repository-Kollaboratoren** und Personen mit **Schreibrechten**  
- Können Releases **erstellen, bearbeiten und löschen**  

## Release erstellen
1. Auf der **Repo-Hauptseite** → **Releases** → **Draft a new release**  
2. **Tag auswählen**:
   - Bestehenden Tag wählen oder neuen Tag erstellen  
   - Branch auswählen, falls neuer Tag  
   - Optional: vorherigen Tag angeben
3. **Release-Titel und Beschreibung** eingeben  
   - @Mentions → Contributors-Sektion automatisch erstellen  
   - Optional: Release Notes automatisch generieren  
4. **Binärdateien** hinzufügen (z. B. kompilierte Programme)  
5. Optional:
   - Pre-Release markieren (instabil)  
   - Als **latest release** setzen  
   - Diskussion erstellen (wenn GitHub Discussions aktiviert)  
6. **Veröffentlichen** → `Publish release`  
   - Oder Entwurf speichern → `Save draft`  

## Release bearbeiten
- Nur Titel und Release Notes, falls **immutable releases** aktiviert  
1. Hauptseite → **Releases**  
2. Release auswählen → **Bearbeiten-Symbol** klicken  
3. Details ändern → **Update release**  
4. @Mentions aktualisieren → Contributors-Sektion automatisch angepasst  

## Release löschen
1. Hauptseite → **Releases**  
2. Release auswählen → **Papierkorb-Symbol** klicken  
3. **Delete this release** bestätigen  

## Kurzfazit
- Releases bündeln **Software, Notizen und Binärdateien**  
- Verwaltung über **Weboberfläche, GitHub CLI oder Releases API**  
- Flexible Optionen: Pre-Releases, Diskussionen, Contributors, automatische Release Notes