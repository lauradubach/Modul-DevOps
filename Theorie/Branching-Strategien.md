# Git Branching Strategien

## Zweck
Regeln für Branch-Erstellung, Merge und Deployment → organisiert, fehlerfrei, vermeidet Konflikte.

## Branch erstellen
```bash
git branch <name>       # Branch erstellen
git checkout <name>     # Wechseln
git checkout -b <name>  # Erstellen + Wechseln
git branch -d <name>    # Löschen
```

## Strategien

### GitFlow

* **Branches:** Master, Develop, Feature, Release, Hotfix
* **Vorteile:** parallele Entwicklung, stabile Produktion, saubere Struktur
* **Nachteile:** komplex, viele Merge-Schritte, Debug schwierig

### GitHub Flow

* **Branches:** Master + Feature
* **Vorteile:** schnell, einfache Releases, CI/CD
* **Nachteile:** keine Entwicklungs-Branches → Risiko instabiler Master

### GitLab Flow

* **Branches:** Master, Develop, Feature, Release
* **Vorteile:** skalierbar, klare Trennung Dev/Prod, weniger Konflikte
* **Nachteile:** komplex, Merge-Konflikte möglich, langsamer Release

### Trunk Based Development

* **Branches:** nur Master/Trunk
* **Vorteile:** kontinuierliche Integration, schnelle Releases, weniger Merge-Konflikte
* **Nachteile:** diszipliniert, schwer für große Teams, Feature Isolation nötig

## Wahl der Strategie

* Klein/Einfach: GitHub Flow, Trunk Based
* Mittel: GitFlow, GitLab Flow
* Groß/Langfristig: GitFlow
* Feature Flags können branching reduzieren

# GitHub Merge-Optionen

## 1. Merge
- Fügt alle Commits der Branch zur Basis ein + Merge-Commit
- **Vorteile:** Detaillierte Historie, zeigt Branch-Erstellung
- **Nachteile:** Merge-Commits können unübersichtlich sein

## 2. Fast-Forward Merge
- Branch wird direkt an Basis angehängt, kein Merge-Commit
- **Vorteile:** Saubere Historie, alle Commits erhalten
- **Nachteile:** Nur möglich, wenn Basis keine neuen Commits hat; Branch-Erstellung geht verloren

## 3. Squash & Merge
- Alle Branch-Commits werden zu einem Commit zusammengefasst
- **Vorteile:** Saubere Historie, leichter Überblick über Arbeit
- **Nachteile:** Details der einzelnen Commits gehen verloren

## 4. Rebase & Merge
- Branch wird auf den aktuellen Stand der Basis verschoben, Commits erneut angewendet
- **Vorteile:** Saubere, lineare Historie, Commits bleiben erhalten
- **Nachteile:** Konflikte möglich, Branch-Historie nicht explizit

## Tipp
- Rebase & Merge → lineare, vollständige Historie  
- Merge → besser für Übersicht über Branches

# Git Branch Naming Cheatsheet

## Grundregeln
- Kleinbuchstaben + Bindestriche: `feature/new-login`
- Nur alphanumerische Zeichen und Bindestriche
- Keine doppelten oder endenden Bindestriche
- Beschreibend und kurz

## Branch-Präfixe
- **feature/** → neue Funktionen (`feature/login-system`)
- **bugfix/** → Fehlerbehebung (`bugfix/header-styling`)
- **hotfix/** → kritische Produktions-Fixes (`hotfix/critical-security-issue`)
- **release/** → Produktionsrelease vorbereiten (`release/v1.0.1`)
- **docs/** → Dokumentation (`docs/api-endpoints`)

## Optional
- Jira-Ticketnummern einfügen: `feature/T-123-new-login-system`

## Beispiele
- `feature/T-456-user-authentication`  
- `bugfix/T-789-fix-header-styling`  
- `hotfix/T-321-security-patch`  
- `release/v2.0.1`  
- `docs/T-654-update-readme`

**Tipp:** Einheitliche Branch-Namen verbessern Übersicht und Teamarbeit.