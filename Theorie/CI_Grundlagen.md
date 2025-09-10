# CI Grundlagen und GitHub CI

## Definition & Ziel
- **CI = kontinuierliche Integration** von Code in ein zentrales Repository  
- Jeder Commit löst **automatisierten Build + Tests** aus  
- Ziel: **Fehler früh erkennen**, Konflikte vermeiden, stabile Hauptlinie sicherstellen  

## CI vs. CD/CD
- **Continuous Integration (CI):** Häufiges Mergen, automatischer Build + Tests → getestetes Artefakt  
- **Continuous Delivery (CD):** Artefakt automatisch in Staging/Release bereitgestellt, Deployment in Produktion erfolgt **manuell**  
- **Continuous Deployment (CD):** Vollautomatischer Rollout in Produktion, jedes erfolgreiche Commit geht live  

## Vorteile von CI
- Frühe **Fehlererkennung**  
- Weniger **Merge-Konflikte**  
- Höhere **Codequalität**  
- **Schnelles Feedback** & Produktivität  
- **Automatisierung** spart Zeit & senkt Risiken

## Herausforderungen
- **Kultureller Wandel**: Zusammenarbeit von Dev, QA, Ops notwendig  
- **Initialer Setup-Aufwand**: Pipelines, Build-Skripte, Tests  
- **Testabdeckung & Qualität** entscheidend  
- **Umgang mit Fehlschlägen** erfordert Fehlerkultur  
- **Tool-/Infrastrukturvielfalt** kann komplex sein 

## Typischer CI-Workflow

![CI_Ablauf](Pictures/ci_ablauf.png)

1. Lokale Entwicklung & Tests  
2. **Commit/Push/Pull Request** ins Repo  
3. CI-Server startet Build (Trigger via Hook)  
4. Schritte:  
   - Code auschecken  
   - Abhängigkeiten installieren  
   - Build erstellen  
   - Automatisierte Tests  
   - (optional) Linter/Analyse  
   - Artefakt erzeugen  
5. **Benachrichtigung** über Ergebnis (grün/rot)  
6. Bei Fehler → Fix & neuer Commit → Zyklus startet erneut

## GitHub Actions für CI
- Nahtlose **Integration in GitHub-Repos**  
- **Ereignisgesteuert** (push, PR, schedule etc.)  
- **Skalierbare Cloud-Runner** mit vorinstallierten Tools  
- **Wiederverwendbare Actions** aus Marketplace  
- Unterstützt auch **Continuous Delivery & Deployment**

## Beispiel: CI in einem Cloud-Projekt
1. Web-App (Python/Node.js) in GitHub Repository  
2. Pipeline via **GitHub Actions**:  
   - Trigger bei Push/PR  
   - Build + Unit-Tests  
   - ggf. Integrationstests  
   - Docker-Image bauen  
   - Artefakt/Registry-Upload  
   - Benachrichtigung im PR  
3. Ergebnis: Ständig **deploy-bereites Artefakt**

## Kurzformel
**CI = automatisiertes Bauen & Testen jeder Änderung**  
➡ Basis für **CD/Deployment**  
➡ Führt zu **schnellerem, sichererem & qualitativ hochwertigerem Release**
