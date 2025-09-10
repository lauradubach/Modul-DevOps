# Git Undo Cheatsheet

## Öffentliche Änderungen rückgängig
- **git revert <SHA>** → erstellt "inverse" Commit, sicher, verändert Historie nicht

## Letzte Commit-Nachricht ändern
- **git commit --amend -m "Neue Nachricht"** → letzte Commit-Nachricht korrigieren

## Lokale Änderungen verwerfen
- **git checkout -- <Datei>** → Datei auf letzten Commit zurücksetzen

## Lokale Commits zurücksetzen
- **git reset <SHA>** → Historie zurücksetzen, Änderungen bleiben  
- **git reset --hard <SHA>** → Historie + Änderungen verwerfen

## Undo nach Reset
- **git reflog** → zeigt Historie von HEAD  
- **git reset --hard <SHA>** → komplette Historie zurückholen  
- **git checkout <SHA> -- <Datei>** → Datei wiederherstellen  
- **git cherry-pick <SHA>** → Commit erneut anwenden

## Commits auf neuen Branch verschieben
- **git branch feature** → neuen Branch erstellen  
- **git reset --hard origin/master** → alten Branch zurücksetzen  
- **git checkout feature** → neue Branch aus alten Commits

## Branch in Zeit verschieben
- **git checkout feature**  
- **git rebase master** → Commits auf aktuelles Master setzen

## Mehrere Commits ändern / löschen
- **git rebase -i <SHA>** → interaktive Rebase  
  - `pick` → Commit behalten  
  - `drop` → Commit löschen  
  - `reword` → Commit-Nachricht ändern  
  - `squash/fixup` → Commits zusammenführen

## Frühere Commits korrigieren
- **git commit --squash <SHA>** → Inhalte in neuen Commit zusammenfassen  
- **git commit --fixup <SHA>** → automatisch Commit-Nachricht übernehmen  
- **git rebase --autosquash -i <SHA>** → interaktiv anwenden

## Tracking von Dateien stoppen
- **git rm --cached <Datei>** → Datei aus Git entfernen, lokal behalten

# Pull Requests (PR) auf GitHub

## Was ist ein Pull Request?
- Vorschlag, Änderungen von einem Branch in einen anderen zu mergen
- PR zeigt Unterschiede (Diffs) zwischen Source- und Zielbranch
- Andere können Änderungen prüfen, kommentieren, oder ergänzen

## Wichtig
- **Themen-Branch** verwenden für sauberes PR-Management
- **Force Push vermeiden** → kann Historie überschreiben
- PRs können über GitHub Web, Desktop, Codespaces, Mobile oder CLI erstellt werden

## PR Funktionen
- Review-Seite: Übersicht + Kommentare + Labels + Assignees
- Commits können nachträglich hinzugefügt werden
- Status Checks müssen bestehen, bevor merge möglich
- PR kann mit Issues verknüpft werden → automatisches Schließen bei Merge
- **Squash Merge** möglich → saubere Commit-Historie

## Draft Pull Requests
- Noch nicht review-fertig, können nicht gemerged werden
- Code Owner werden nicht automatisch angefragt
- Kann jederzeit in Ready-for-Review oder zurück zu Draft geändert werden

## Diff Unterschiede
- Compare-Seite: Diff zwischen Branch-Spitze & aktuellem Merge Base  
- PR-Seite: Diff zwischen Branch-Spitze & Merge Base beim Erstellen des PR

# Pull Request erstellen

## Zweck
- Änderungen in einem Branch vorschlagen und zusammenarbeiten
- Standardbranch enthält nur fertige, geprüfte Arbeit

## Wer kann PRs erstellen?
- Jeder mit Lesezugriff auf das Repository
- Bei fehlenden Schreibrechten: Repository forken

## Vorgehen
1. **Branch auswählen:** Branch mit deinen Änderungen wählen
2. **PR starten:** „Compare & pull request“ klicken
3. **Zielbranch wählen:** Base branch = Ziel, Compare branch = Feature/Topic branch
4. **Titel & Beschreibung eingeben**
5. **PR erstellen:**  
   - Ready-for-Review → „Create Pull Request“  
   - Draft → „Create Draft Pull Request“

## Nach dem PR
- Änderungen können weiter in Head Branch committet werden
- Dateien auch direkt auf GitHub editieren
- Reviewer gezielt anfragen
- Nach Review kann PR gemerged werden

## Tipps
- PR mit Issue verknüpfen → automatisches Schließen bei Merge
- Templates für PRs möglich
- Branches und Repository können für PR angepasst werden

# Pull Request Reviews

## Zweck
- Änderungen kommentieren, genehmigen oder Änderungen anfordern
- Sicherstellen, dass PR den Qualitäts- und Richtlinienstandards entspricht

## Wer kann Reviews machen?
- Jeder mit Lesezugriff kann kommentieren
- Admins/Owner können festlegen, wer Reviews genehmigen oder Änderungen anfordern darf
- Code Owners werden automatisch als Reviewer angefragt, wenn ihr Code geändert wird

## Review-Status
1. **Comment:** Feedback ohne Genehmigung/Änderungsanforderung
2. **Approve:** PR kann gemerged werden
3. **Request changes:** Änderungen müssen umgesetzt werden, bevor Merge (nur bei konfigurierten Regeln verbindlich)

## Weitere Funktionen
- Konversationen können **resolved** werden
- Neue Issues für Feedback außerhalb des PRs möglich
- Reviews können nach größeren Änderungen erneut angefragt werden
- Admins können Reviews bei Bedarf **dismissen**
- Übersicht über Reviews in Conversation Timeline & Merge Box

# Pull Request Mergen

## Zweck
- Änderungen aus einem Head-Branch in den Base-Branch übernehmen
- Jeder mit Push-Zugriff kann mergen

## Voraussetzungen
- Kein Merge bei Draft-PRs
- Status-Checks oder Branch-Protection-Regeln müssen ggf. erfüllt sein
- Konflikte müssen vorher gelöst werden

## Merge-Optionen
1. **Merge commit** – Alle Commits zusammenführen, erstellt Merge-Commit (`--no-ff`)
2. **Squash & Merge** – Alle Commits zu einem zusammenfassen
3. **Rebase & Merge** – Commits einzeln auf Base-Branch anwenden (SHA ändert sich)

## Vorgehen
1. PR auswählen
2. Merge-Option wählen
3. Commit-Nachricht prüfen oder anpassen
4. Merge bestätigen
5. Optional: Head-Branch löschen

## Hinweis
- PR kann mit Issue verlinkt werden, um es automatisch zu schließen

# Merge-Konflikte

## Was ist ein Merge-Konflikt?
- Tritt auf, wenn zwei Branches widersprüchliche Änderungen enthalten
- Git kann nicht automatisch entscheiden, welche Änderung übernommen wird
- Typische Fälle: gleiche Zeile geändert, Datei gelöscht vs. geändert

## Vorgehen bei Konflikten
1. **Auf GitHub**: Konflikt-Editor für Zeilenkonflikte nutzen
2. **Lokal**: Konflikt in lokaler Kopie lösen, dann Änderungen pushen
3. PR kann erst gemerged werden, wenn alle Konflikte gelöst sind

## Beispiel CLI
```bash
git merge BRANCH-NAME
# Auto-merging styleguide.md
# CONFLICT (content): Merge conflict in styleguide.md
# Automatic merge failed; fix conflicts and then commit the result
```
# Merge-Konflikte auf GitHub lösen

## Voraussetzungen
- Nur **Zeilen-Konflikte** können direkt auf GitHub gelöst werden
- Komplexe Konflikte → lokal auflösen (CLI oder Git-Client)

## Vorgehen auf GitHub
1. Pull Request mit Konflikt öffnen
2. Unten auf **Resolve conflicts** klicken
3. Entscheiden:
   - Nur eigene Änderungen behalten
   - Nur andere Branch-Änderungen behalten
   - Neue Version erstellen (beide Änderungen kombinieren)
4. Konflikt-Markierungen `<<<<<<<`, `=======`, `>>>>>>>` entfernen
5. **Mark as resolved** klicken
6. Alle Dateien mit Konflikten wiederholen
7. **Commit merge** klicken
8. Falls Head-Branch geschützt oder Default-Branch:
   - Neuen Branch erstellen oder vorhandenen aktualisieren
9. Pull Request anschließend mergen