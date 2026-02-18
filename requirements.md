# Requirements — CS&VA Automation
**Projekt:** Automatische Erstellung von Component Specification und Vendor Addendum  
**Auftraggeber:** Fabian Mutschler · PTC BD Q SN EES CGS · Vitesco / Schaeffler  
**Version:** 1.0 · Stand: Februar 2026  

---

## 1. Projekthintergrund

### 1.1 Ausgangssituation
Jede Anfrage von NGK oder einem internen PM zur Erstellung einer Component Specification (CS) und eines Vendor Addendum (VA) erfordert derzeit einen manuellen Prozess mit folgenden Problemen:

| Kennzahl | Ist-Zustand |
|---|---|
| Anzahl Anfragen pro Monat | ~20 |
| Zeitaufwand pro Vorgang | ~30 Minuten |
| Gesamtaufwand pro Monat | ~10 Stunden |
| Felder manuell im CS | 23 |
| Ausführbar durch | 1 Person (Fabian Mutschler) |
| Fehlerrisiko | Hoch (Wiederholung, manuelle Dateneingabe) |

### 1.2 Ziel des Projekts
Automatisierung des gesamten Prozesses von der Dateneingabe bis zum versandfertigen Outlook-Entwurf. Der manuelle Anteil reduziert sich auf die **abschließende Prüfung und den Versand** durch FaMu.

**Fertigstellungsziel:** 10. April 2026

---

## 2. Stakeholder

| Rolle | Person | Aufgabe |
|---|---|---|
| Hauptnutzer / Auftraggeber | Fabian Mutschler (FaMu) | Prozessverantwortlicher, Tester, Versand |
| Vertreter | tbd | Soll ebenfalls die App nutzen können |
| Lieferant / Empfänger | NGK | Empfänger der CS&VA-E-Mails |
| ME-Unterstützung | Thomas Kropf, Michael Leitl | Zugang zu Variant Overview / WISE |
| Variant Overview Update | Andreia-Raluca Ivan | Pflegt Zeichnungsversionen in Variant Overview |
| Sponsor / PM | tbd | LEAN Projekt Sponsor |

---

## 3. Funktionale Anforderungen

### 3.1 Pflichtanforderungen (Must Have)

#### REQ-F-001 — Eingabemaske
- Die App stellt eine grafische Oberfläche bereit (lokal im Browser)
- Pflichtfelder: NOS-Zeichnungsnummer, NOS-Code
- Optionales Feld: MLC-Status-Prüfung

#### REQ-F-002 — Automatische Ordnerstruktur
- Die App legt automatisch einen nummerierten Hauptordner an unter:  
  `...\15_PPAP_documentation\`
- Format: `{lfd.Nr}_{A2C-Nummer}_{NOS-Name}`  
  Beispiel: `034_AAA9896630000_AA9`
- Zwei Unterordner werden erstellt:
  - `Component Specification and Vendor Addendum`
  - `PPAP_PSW_{A2C-Nummer}`
- Templates (CS + VA) werden automatisch in den Unterordner kopiert

#### REQ-F-003 — Component Specification befüllen
- Das CS-Template (.docx) wird automatisch mit folgenden Feldern befüllt:
  - A2C-Nummer (mehrfach, konsistent auf allen 4 Seiten)
  - NOS-Name (mehrfach, konsistent auf allen 4 Seiten)
  - Aktuelles Datum
  - NOS-Zeichnungsreferenz (Dokumentnummer, Version, Datum)
  - Variant-Overview-Referenz (Dokumentnummer, Version, Datum)
  - NOS Specification Nummer
- Gesamtanzahl zu befüllender Felder: 23

#### REQ-F-004 — Vendor Addendum befüllen
- Das VA-Template (.docx) wird automatisch befüllt:
  - A2C-Nummer (Component part number + Specification number)
  - NOS-Name
  - Aktuelles Datum

#### REQ-F-005 — Zeichnungsdaten aus lokalem Dateisystem
- Die App liest Zeichnungsmetadaten (Version, Datum, Dokumentname) aus den lokal synchronisierten SharePoint-Ordnern
- Variant Overview und NOS-Drawing werden aus dem lokalen Pfad gelesen:  
  `...\NOS_Assy\` (Gen2 / Gen3 / Gen4p)
- PDFs werden in den Zielordner kopiert

#### REQ-F-006 — PDF-Export
- CS wird als `.docx` und `.pdf` im Zielordner gespeichert
- Dateiname: `{A2C-Nummer}.docx` / `{A2C-Nummer}.pdf`
- VA bleibt als `.docx` (Vendor Addendum.docx)

#### REQ-F-007 — Verteilerkreis aus Kommunikationsmatrix
- Die App liest automatisch die Excel-Datei:  
  `...\RI-256016_QA Communication Matrix(SAG-NGK)_RI-256017_NGK QA organization.xlsx`
- Task-Filter: "PPAP"
- Liefert To- und CC-Adressen für den E-Mail-Entwurf

#### REQ-F-008 — Outlook-Entwurf erstellen
- Die App erstellt einen Outlook-Entwurf (nicht automatisch senden!)
- Betreff: `Specification {A2C-Nummer} Exhaust Gas Nox Sensor {NOS-Name} Version {Version} with drawings`
- Anhänge in dieser Reihenfolge:
  1. NOS Component Zeichnung (.pdf)
  2. Variant Overview (.pdf)
  3. CS als .pdf
  4. VA als .docx
- Standardtext wird vorausgefüllt
- **Manuelle Prüfung und Versand durch FaMu** (keine automatische Absendung)

---

### 3.2 Sollte-Anforderungen (Should Have)

#### REQ-F-009 — Datentracking / Protokoll
- Jeder Vorgang wird protokolliert: Datum, Anfragesteller, A2C-Nummer, NOS-Code, Status
- Log-Datei im Zielordner oder zentral

#### REQ-F-010 — Vorschau der Dokumente
- Die App zeigt nach dem Befüllen eine Vorschau der wichtigsten Felder an (Validierung vor Export)

#### REQ-F-011 — Multi-User-Fähigkeit
- Die App ist so dokumentiert, dass weitere Personen sie installieren und nutzen können
- Installer-Skript für Windows (install.bat)

---

### 3.3 Kann-Anforderungen (Nice to Have)

#### REQ-F-012 — Mehrere NOS gleichzeitig
- Stapelverarbeitung: Liste von NOS-Nummern auf einmal verarbeiten

#### REQ-F-013 — Status-Dashboard
- Übersicht aller verarbeiteten Vorgänge in der App-Oberfläche

---

## 4. Nicht-funktionale Anforderungen

| ID | Anforderung | Details |
|---|---|---|
| REQ-NF-001 | Betriebssystem | Windows 10 / 11 |
| REQ-NF-002 | Laufzeitumgebung | Lokal, kein Server erforderlich |
| REQ-NF-003 | Installierbarkeit | Installierbar durch nicht-technische Nutzer via install.bat |
| REQ-NF-004 | Verarbeitungszeit | Ein Vorgang in unter 2 Minuten (exkl. manuelle Prüfung) |
| REQ-NF-005 | Zuverlässigkeit | Konsistente Feldbefüllung ohne manuelle Fehler |
| REQ-NF-006 | Sprache der Oberfläche | Deutsch |
| REQ-NF-007 | Datenzugriff | Nur lokale Dateisystempfade (SharePoint-synchronisiert) |
| REQ-NF-008 | Keine Internetverbindung nötig | Außer für initiale Installation der Python-Pakete |
| REQ-NF-009 | Outlook-Version | Microsoft Outlook (COM-Interface, nicht Web-Outlook) |
| REQ-NF-010 | Quellcode | Python 3.11+, dokumentiert, auf GitHub |

---

## 5. Abgrenzung (Out of Scope)

- Automatischer E-Mail-Versand ohne manuelle Prüfung
- Import von CS-Inhalten aus SAP (Daten unvollständig / nicht gepflegt)
- Web-basierter Betrieb auf einem Server
- Integration in SAP-Systeme
- Automatische PCN-Daten (werden manuell durch Supplier befüllt)

---

## 6. Randbedingungen & Risiken

| Risiko | Wahrscheinlichkeit | Maßnahme |
|---|---|---|
| WISE-Dateipfade ändern sich | Mittel | Pfade konfigurierbar in config.yaml |
| Template-Struktur wird geändert | Mittel | Template-Versionierung im Repository |
| Vertreter für FaMu stimmt nicht zu | Mittel | Frühe Einbindung in UAT |
| Outlook-COM-Zugriff blockiert durch IT | Niedrig | Alternativ: .eml-Datei erzeugen |
| SharePoint-Sync nicht vorhanden auf anderem PC | Mittel | Dokumentation als Installationsvoraussetzung |

---

## 7. Akzeptanzkriterien

Ein Vorgang gilt als erfolgreich automatisiert, wenn:

1. ✅ Ordnerstruktur korrekt angelegt
2. ✅ Alle 23 CS-Felder korrekt und konsistent befüllt
3. ✅ VA-Felder korrekt befüllt
4. ✅ PDFs korrekt exportiert und benannt
5. ✅ Zeichnungs-PDFs korrekt abgelegt
6. ✅ Outlook-Entwurf mit korrekten Empfängern, Betreff, Anhängen
7. ✅ Gesamtdauer unter 2 Minuten (Maschinenlaufzeit)

---

*Dokument gepflegt unter: `docs/requirements.md`*
