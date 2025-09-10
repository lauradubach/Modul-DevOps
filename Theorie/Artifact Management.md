# Artefakte in DevOps

* **Definition**: Ergebnisse aus dem Build-Prozess (nicht Quellcode, sondern nutzbare Produkte).
* **Beispiele**: Binaries (.exe, .jar), Libraries (.whl, .nupkg), Container-Images, Deployment-Pakete, Helm Charts, IaC-Templates.
* **Nutzen**:

  * Trennung von Build & Deployment (einmal bauen, mehrfach deployen)
  * Reproduzierbarkeit & Versionierung
  * Sicherheit (nur geprüfte Artefakte)
  * Effizienz (kein Neubauen nötig)

## Artefakt-Repositories

* Speichern & versionieren Artefakte zentral (z. B. Azure Artifacts, GitHub Packages, Artifactory, Nexus, DockerHub, PyPI).

## Begriffe

* **Quellcode** = geschriebenes Programm
* **Build** = Prozess, der aus Quellcode ein Produkt erzeugt
* **Artefakt** = Ergebnis des Builds

## GitHub Actions & Artefakte

* Automatisierte Workflows (CI/CD): Code kompilieren, testen, Artefakt erzeugen, speichern/deployen.
* **upload-artifact**: speichert temporäre Build-Outputs für den Workflow.

## GitHub Packages

* GitHub-integriertes Paket-Hosting (npm, Maven, NuGet, Docker …).
* Vorteile: Versionierung, Zugriffskontrolle, CI/CD-Integration.
* Unterschied zu **upload-artifact**:

  * Packages = dauerhafte Paketverwaltung für Wiederverwendung.
  * upload-artifact = temporäre Speicherung für Workflows.

## Besonderheit bei Python

* Artefakte sind optional (historisch keine Pflicht → Flexibilität, aber weniger Standardisierung).
* Pakete können mit Tools wie setuptools, poetry etc. erzeugt werden.
* GitHub Packages unterstützt Python **nicht nativ** → Workarounds nötig oder externe Repositories (Azure DevOps Artifacts, Artifactory, Nexus).
* Azure DevOps bietet native Python-Feeds, zentral und organisationsweit nutzbar.

Kurzfazit:
Artefakte sind zentrale, wiederverwendbare Ergebnisse des Build-Prozesses. Sie werden in Repositories gespeichert, versioniert und in CI/CD-Pipelines genutzt. GitHub Actions kann Artefakte erzeugen (upload-artifact = temporär, GitHub Packages = dauerhaft). Bei Python ist Artefakt-Management nicht zwingend, was im DevOps-Umfeld bewusst organisiert werden muss.

# Die „Three Ways“ von DevOps

Sie bilden die Grundprinzipien, aus denen alle DevOps-Methoden abgeleitet werden können. Sie beschreiben Werte, Philosophien und Vorgehensweisen.

**1. The First Way: Flow / Systems Thinking**

* Fokus auf das **Gesamtsystem**, nicht auf einzelne Abteilungen oder Rollen.
* Betrachtung des kompletten Wertstroms: von Anforderungen → Entwicklung → Betrieb → Kunde.
* Ergebnisse: keine Defekte weitergeben, keine lokale Optimierung zulasten des Gesamtsystems, stetige Verbesserung des Flusses, tiefes Systemverständnis.

**2. The Second Way: Amplify Feedback Loops**

* Ziel: **Feedbackschleifen verkürzen und verstärken**, um schneller auf Probleme reagieren zu können.
* Ergebnisse: Kundenfeedback (intern & extern) verstehen, Feedback in alle Prozesse einbetten, Wissen dort verankern, wo es gebraucht wird.

**3. The Third Way: Culture of Continual Experimentation and Learning**

* Aufbau einer Kultur der **ständigen Verbesserung und des Lernens**.
* Zwei Elemente:

  * Experimentieren & Risiken eingehen (Fehler zulassen & daraus lernen).
  * Übung & Wiederholung als Basis für Meisterschaft.
* Ergebnisse: Zeit für Verbesserungen schaffen, Risiken belohnen, gezielt Fehler ins System einführen, um Resilienz zu steigern.

**Kurzfassung:**
Die „Three Ways“ von DevOps sind:

1. **Flow/Systems Thinking** – Fokus aufs Gesamtsystem und durchgängigen Wertstrom.
2. **Feedback Loops** – Feedback verkürzen und verstärken, um kontinuierlich zu lernen.
3. **Experimentation & Learning** – Kultur des Lernens, Experimentierens und Risikos, um langfristige Verbesserungen und Resilienz zu erreichen.

| The .. Way        | kulturelle Herausforderungen bzw. Lösungen                                                                 | technische Herausforderungen bzw. Lösungen                                                                 |
|-------------------|------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|
| **The first way** | Silos aufbrechen, End-to-End-Denken fördern, gemeinsame Verantwortung für den gesamten Wertstrom schaffen.  | CI/CD-Pipelines einführen, Automatisierung von Builds/Deployments, Monitoring entlang des gesamten Systems. |
| **The second way**| Offene Feedbackkultur etablieren, Fehler früh adressieren, Zusammenarbeit zwischen Dev, Ops und Business.   | Schnelle Feedback-Mechanismen (z. B. Tests, Monitoring, Logging), automatisierte Alerts, Telemetrie.       |
| **The third way** | Fehler als Lernchance sehen, Risikobereitschaft belohnen, kontinuierliche Verbesserung und Lernen fördern.  | Chaos Engineering, Feature Flags, automatisierte Tests & Rollbacks, Experimente sicher in Staging/Prod.     |