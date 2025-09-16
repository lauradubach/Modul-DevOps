# Branching- und Merging-Strategie

## Branching-Strategie
Wir nutzen ein **Git-Flow-ähnliches Modell**, um die Entwicklung klar zu strukturieren:

- **`main`-Branch**
  - Enthält immer den stabilen, produktionsreifen Code.
  - Nur getestete Releases werden hierhin gemergt.

- **`develop`-Branch**
  - Enthält den aktuellen Stand der Entwicklung.
  - Alle neuen Features und Bugfixes werden hier integriert.

- **Feature-Branches**
  - Namensschema: `feature/<beschreibung>` (z. B. `feature/login-page`)
  - Basis: `develop`
  - Ziel: Merge zurück in `develop`
  - Werden nach Merge gelöscht.

- **Release-Branches**
  - Namensschema: `release/x.y.z`
  - Basis: `develop`
  - Ziel: Merge nach `main` **und** zurück nach `develop`
  - Hier erfolgen letzte Tests, kleine Fixes und Vorbereitungen (z. B. Versionsnummern).

- **Hotfix-Branches**
  - Namensschema: `hotfix/x.y.z`
  - Basis: `main`
  - Ziel: Merge nach `main` **und** `develop`
  - Für schnelle Fehlerbehebungen in Produktion.

## Merging-Strategie
- **Feature-Branches → develop**: per **Squash-Merge**, damit ein Feature einen sauberen Commit in der Historie hinterlässt.
- **Release- und Hotfix-Branches → main & develop**: per **Merge-Commit**, um die Historie der Releases nachvollziehbar zu halten.
- **Rebase** wird nur lokal genutzt, um Feature-Branches aktuell zu halten (keine Rebase auf `main` oder `develop` nach Push).

## Merge-Konflikte
- Konflikte erkennt man beim **lokalen Merge oder Rebase** (Git markiert betroffene Dateien).
- Vorgehen:
  1. Konfliktstellen manuell prüfen und auflösen.
  2. Änderungen testen.
  3. Gelöste Dateien mit `git add` markieren.
  4. Merge mit `git commit` abschließen.

## Pull Requests & Code Reviews
- **Jeder Merge nach `develop` oder `main` erfolgt über einen Pull Request (PR)**.
- PR-Checkliste:
  - Build läuft durch (CI).
  - Tests sind grün.
  - Code entspricht den Style-Guides.
- **Mindestens ein Review** von einem Teammitglied ist Pflicht.
- Diskussionen erfolgen im PR, offene Punkte werden durch neue Commits gelöst.

## Tags & Releases
- Nach jedem Release auf `main` wird ein **annotated Tag** gesetzt:

```bash
    git tag -a v1.1.0 -m "Release 1.1.0: Erste Version"
    git push origin v1.1.0
```
Die Release-Dokumentation (Changelog) enthält:
- Neue Features
- Fixes
- Breaking Changes

Optional wird GitHub/GitLab Releases genutzt, um die Infos sichtbar zu machen.

-> Versionierung nach SemVer (Semantic Versioning)

**Schema:** MAJOR.MINOR.PATCH

- MAJOR (z. B. 2.0.0): inkompatible Änderungen
- MINOR (z. B. 1.3.0): neue Features, abwärtskompatibel
- PATCH (z. B. 1.2.5): Bugfixes, kleine Verbesserungen
