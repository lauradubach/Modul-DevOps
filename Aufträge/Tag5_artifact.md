# Fragen

## Wann reicht ein temporäres Artefakt (Option 1) aus – wann benötigst du einen zentralen Feed (Option 2)?

- Option 1 (upload-artifact) reicht für: einmalige Übergaben zwischen Jobs, manuelles Testen/Debuggen, PoCs, kurzfristige Builds ohne Wiederverwendung.
- Option 2 (Azure DevOps Feed) nötig für: wiederverwendbare Pakete über Teams/Repos hinweg, Installationen via pip, langfristige Aufbewahrung, Versionierung/Audit/Compliance, reproduzierbare Deployments.


## Welche Vor- und Nachteile haben temporäre Artefakte vs. Feed-Publikation (Versionierung, Sichtbarkeit, Governance, Wiederverwendung)?

- Temporär: + schnell, kein Setup; − begrenzte Retention, keine Versionierung/Indexierung, schwer teilbar, kein Standard-Installweg.
- Feed: + Versionierung, Discovery, Zugriffskontrolle, Wiederverwendung, Standard-Install (pip), Auditfähigkeit; − Setup/Authentifizierung nötig, Prozessdisziplin (Version-Bump).


## Welche Qualitätsanforderungen (Tests, Security Checks) sollten vor einer Feed-Publikation zwingend erfüllt sein?

- Build grün, Unit-/Integrationstests ok.
- Lint/Format/Types (z. B. flake8/black/mypy) ok.
- Paket-Check: python -m build; twine check; Test-Install in sauberer venv; Smoke-Test (import/CLI).
- Security: Dependency/SCA-Scan, optional SAST; Lizenz-Check.
- Doku/Changelog aktualisiert; SemVer-Version eindeutig; optional Signatur/SBOM.


## Wie stellst du sicher, dass Versionen konsistent erhöht werden (SemVer, Tags, Commit Hash, automatischer Bump)?

- SemVer als Policy; Single Source of Truth (pyproject.toml) oder tag-basierte Versionierung.
- CI-Guardrails: „version != bereits im Feed“ prüfen; Upload bei 409 verhindern.
- Automatischer Bump per Release-Workflow/Conventional Commits; Pre-Releases (rc) für Tests.
- Git-Tag vX.Y.Z beim Merge auf main setzen; Release Notes generieren/prüfen.


## Welche Kriterien nutzt du künftig zur Entscheidung: nur upload-artifact oder zusätzlich Feed-Publish?

- Wird das Ergebnis von anderen Projekten/Teams konsumiert? → Feed.
- Muss es versioniert/auditierbar/langfristig verfügbar sein? → Feed.
- Nur kurzzeitig für Debug/Hand-over im Workflow? → upload-artifact.
- Compliance/Vulnerability-Gates gefordert? → Feed mit Qualitäts-Gates.
- Aufwand vs. Nutzen: ab erstem externen Consumer → Feed lohnt sich.