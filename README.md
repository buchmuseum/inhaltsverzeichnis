Virtuelle Fachbibliothek des Deutschen Buch- und Schriftmuseums,  gefiltern anhand Inhaltsverzeichnissen

Diese Skripte verarbeiten bibliografische Daten aus der Deutschen Nationalbibliothek (DNB) und transformiert sie in eine strukturierte, klassifizierte und wieder in PICA rückspielbare Datenform.

Der Workflow kombiniert:

PICA-Datenextraktion
Schlagwortbasierte Volltextanalyse
Gewichtete Relevanzbewertung
KI-gestützte Klassifikation
ESK-Kategorisierung
Rückschreiben in PICA (.dat)

Gesamtprozess
PICA Dump (.dat.gz)
        ↓
1. PICA-Filter + Extraktion → Excel Rohdaten
        ↓
2. DNB Inhaltsanalyse + Schlagwortgewichtung
        ↓
   → Treffer + Systematik + Score
        ↓
3. KI-Relevanzbewertung + ESK-Klassifikation
        ↓
   → Filterung + Annotation + Farbmarkierung
        ↓
4. Rückschreiben in PICA (.dat)
        ↓
   → Anreicherung für WinIBW / Katalogsystem



1. PICA-Datenextraktion (Basisdatenaufbau)
Ziel

Extraktion strukturierter bibliografischer Daten aus einem PICA-Datendump.

Funktion
Filterung eines großen .dat.gz PICA-Dumps
Anwendung eines PICA-Filters:
z. B. Materialtyp / Kategorieeinschränkung
Extraktion folgender Felder:
Metadaten
IDN
Titel
Titelzusatz
Autor
Erscheinungsort
Verlag
Jahr
DDC
Schlagwörter
Output

Output = Zwischenauswertung1.xlsx

Zusatzlogik
Duplikaterkennung über idn.txt
keine Mehrfachverarbeitung identischer Datensätze



2️. DNB Volltextanalyse + Schlagwortgewichtung
Ziel

Inhaltliche Analyse der DNB-Inhaltsverzeichnisse zur semantischen Bewertung.

Ablauf
Webzugriff
Zugriff auf:
https://d-nb.info/{IDN}/04/text 
Analyse
Volltext wird bereinigt und normalisiert
Schlagwörter aus Mapping-Tabelle werden gesucht
Gewichtungssystem

Jeder Treffer hat:

Anzahl Treffer
Gewichtung
zugeordnete Systematik
Ergebnis pro Datensatz
Gesamtgewichtung
Gefundene Schlagwörter
beste Systematik
Kombination mit Metadaten

Zusätzlich werden berücksichtigt:

Titelanalyse
Schlagwortfelder aus PICA

➡ Ergebnis:

Gewicht wird erhöht
Systematik kann überschrieben werden

Output = ergebnis_juni_2026.xlsx



3️. KI-basierte Relevanzbewertung + ESK-Klassifikation
Ziel

Semantische Bewertung der Ergebnisse durch KI-Modell.

Eingabe
DNB Inhaltsverzeichnis (OCR / Text)
Relevanzkriterien (YAML)
Beispiele für relevante/nicht relevante Inhalte
KI-Relevanzbewertung

Das Modell entscheidet:

relevant
teilweise relevant
nicht relevant

inkl. Begründung

ESK-Klassifikation

Nur bei:

relevant
teilweise relevant

wird zusätzlich:

ESK-Kategorie bestimmt
Regeln:
immer spezifischste Unterkategorie bevorzugen (z. B. ESK.16.4)
historische Inhalte → immer ".4 Geschichte"
Fachbereich schlägt allgemeine Kategorie
Ergebnisaufbereitung
Farbliche Markierung in Excel:
🟢 relevant
🟠 teilweise relevant
🔴 nicht relevant
automatische Filterung irrelevanter Einträge
zusätzliche Spalte: „Übernehmen“

Output = Ki_juni_2026_2.xlsx



4️. PICA-Rückschreibung (WinIBW-Anreicherung)
Ziel

Rückführung der KI-Ergebnisse in PICA-Struktur zur Systemanreicherung.
Verarbeitung
Schritt 1: ESK-Extraktion
Parsing der KI-Antwort:
ESK: ...
Codes extrahieren (Regex)
Schritt 2: Mapping

ESK-Code → PICA-IDN
Mapping erfolgt über YAML-Datei:
esk-idn.yaml
Schritt 3: DAT-Datei Generierung

Für jeden Datensatz:
IDN
5320 <PICA-IDN aus ESK Mapping>
4700 |BSM|*Relevanztext$hNVF-2606

Output = update_045Pjuni2.dat

➡ direkt nutzbar für:
WinIBW
Kataloganreicherung
PICA Importprozesse
Systemanforderungen



⚙️ Installation
Voraussetzungen
Python 3.9+
pip

Benötigte Pakete
pip install pandas openpyxl requests beautifulsoup4 pyyaml

KI-Modul (optional / erforderlich für Schritt 3)
OpenAI-kompatible API erforderlich
verwendetes Model hier: 
Academic Cloud / SAIA Endpoint
gültiger API-Key notwendig
base_url = "https://chat-ai.academiccloud.de/v1"
model = "openai-gpt-oss-120b"

Externe Abhängigkeiten
Deutsche Nationalbibliothek (DNB)
Inhaltsverzeichnisse über IDN-Endpunkte
Internetverbindung zwingend erforderlich

Dateivoraussetzungen
PICA-Datendump (.dat.gz) 
Maaping-exceltabelle (.xlsx) 
Schlagwort → Gewichtung → Systematik 
Relevanzkriterien (.yaml)
ESK-Kategorien (.yaml)

