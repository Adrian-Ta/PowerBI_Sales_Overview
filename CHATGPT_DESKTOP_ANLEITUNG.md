# Anleitung für ChatGPT Desktop: Power BI Sales Overview erstellen

## Ziel
Setze das Starterprojekt in Power BI Desktop unter Windows vollständig um. Erstelle einen interaktiven Sales-Overview-Bericht anhand der enthaltenen synthetischen CSV-Daten. Arbeite schrittweise, prüfe nach jedem wichtigen Schritt das Ergebnis und melde klar, wenn eine Aktion manuell erforderlich ist.

## Dateien im Projektordner
- `Sales.csv`: Bestellpositionen mit Datum, Produkt, Kunde, Umsatz, Kosten und Gewinn
- `Products.csv`: Produktstammdaten mit Kategorie und Unterkategorie
- `Customers.csv`: Kundenstammdaten mit Segment und Region
- `DAX_Measures.txt`: vorgeschlagene DAX-Kennzahlen und Kalender-Tabelle
- `PowerQuery_M.txt`: Power Query-Importhinweise
- `Dashboard_Layout.md`: gewünschtes Layout und Visualisierungen
- `README.md`: Setup- und Datenhinweise

## Arbeitsregeln
1. Verwende die Dateien im entpackten Projektordner. Suche den Ordner bei Bedarf über den Windows-Dateiauswahldialog; er kann je nach Rechner anders liegen.
2. Nutze ausschließlich die synthetischen Beispieldaten aus diesem Paket. Behaupte nicht, dass sie echte Unternehmensdaten sind.
3. Speichere das Ergebnis als neue Power-BI-Datei, z. B. `Sales_Overview.pbix`, in einem separaten Ausgabeordner. Überschreibe keine bestehenden Dateien.
4. Führe keine Veröffentlichung in Power BI Service aus und verbinde keine Konten.
5. Frage nach, bevor du etwas löschst, überschreibst, veröffentlichst oder Einstellungen außerhalb dieses Projekts änderst.
6. Falls du die grafische Oberfläche nicht bedienen kannst, erkläre exakt, welche Schritte der Nutzer selbst ausführen muss. Behaupte nie, eine Datei erstellt oder gespeichert zu haben, wenn du das nicht verifiziert hast.
7. Wenn ein Schritt scheitert, notiere die Fehlermeldung und fahre mit unabhängigen Schritten fort.

## Auszuführende Schritte

### 1. Dateien und Power BI prüfen
- Öffne den Projektordner und verifiziere, dass `Sales.csv`, `Products.csv` und `Customers.csv` vorhanden sind.
- Starte oder fokussiere Power BI Desktop in der Windows-VM.
- Falls Power BI Desktop nicht erreichbar ist, halte an und frage den Nutzer, ob er es öffnen kann.

### 2. Daten importieren
- Importiere alle drei CSV-Dateien in Power BI Desktop.
- Verwende UTF-8, Komma als Trennzeichen.
- Stelle Datentypen ein:
  - `Sales[OrderDate]`: Datum
  - `Sales[Quantity]`: ganze Zahl
  - `Sales[UnitPrice]`, `Sales[Sales]`, `Sales[Cost]`, `Sales[Profit]`: Dezimalzahl oder feste Dezimalzahl
  - `Sales[DiscountRate]`, `Sales[ProfitMargin]`: Dezimalzahl/Prozent
  - IDs und Namen: Text
  - `Products[UnitCost]`, `Products[ListPrice]`: Dezimalzahl/Währung
- Prüfe, ob keine Abfragefehler vorhanden sind und ob die Zeilenanzahlen plausibel sind: Sales 2.600 Zeilen, Products 18 Zeilen, Customers 240 Zeilen.
- Wende die Änderungen an.

### 3. Datenmodell erstellen
Erstelle diese Beziehungen mit einfacher Filterrichtung von Dimension zu Fakt:
- `Products[ProductID]` (1) → `Sales[ProductID]` (*)
- `Customers[CustomerID]` (1) → `Sales[CustomerID]` (*)

Erstelle über Modellierung > Neue Tabelle eine Kalender-Tabelle mit DAX:

```DAX
Calendar =
ADDCOLUMNS(
    CALENDAR(MIN(Sales[OrderDate]), MAX(Sales[OrderDate])),
    "Year", YEAR([Date]),
    "Month Number", MONTH([Date]),
    "Month", FORMAT([Date], "MMM"),
    "Year Month", FORMAT([Date], "YYYY-MM")
)
```

Erstelle die Beziehung:
- `Calendar[Date]` (1) → `Sales[OrderDate]` (*)

Markiere `Calendar` als Datumstabelle, falls die Power-BI-Version die Option anbietet. Sortiere `Calendar[Month]` nach `Calendar[Month Number]`. Verwende für Zeitreihen `Calendar[Year Month]`, damit Monate über mehrere Jahre korrekt sortiert sind.

### 4. DAX-Measures erstellen
Erstelle jedes Measure einzeln. Falls der DAX-Editor Fehler zeigt, korrigiere die Formel und teste erneut.

```DAX
Total Sales = SUM(Sales[Sales])
Total Profit = SUM(Sales[Profit])
Total Cost = SUM(Sales[Cost])
Total Orders = DISTINCTCOUNT(Sales[OrderID])
Total Quantity = SUM(Sales[Quantity])
Total Customers = DISTINCTCOUNT(Sales[CustomerID])
Average Order Value = DIVIDE([Total Sales], [Total Orders], 0)
Profit Margin % = DIVIDE([Total Profit], [Total Sales], 0)
Sales Previous Month =
CALCULATE([Total Sales], DATEADD('Calendar'[Date], -1, MONTH))
Sales MoM Change % =
DIVIDE([Total Sales] - [Sales Previous Month], [Sales Previous Month], 0)
```

Formatiere Umsatz, Gewinn, Kosten und durchschnittlichen Bestellwert als EUR. Formatiere `Profit Margin %` und `Sales MoM Change %` als Prozent. Verwende bei Bedarf Dezimalstellen, die die Lesbarkeit verbessern.

### 5. Berichtseite „Overview“ gestalten
Erstelle eine 16:9-Berichtsseite mit hellem, aufgeräumtem Layout. Verwende konsistente Abstände und gut lesbare Beschriftungen.

**Obere Zeile: fünf KPI-Karten**
1. Total Sales
2. Total Profit
3. Total Orders
4. Average Order Value
5. Total Customers

**Mittlerer Bereich**
- Kombinationsdiagramm oder Säulendiagramm: Umsatz nach `Calendar[Year Month]`; Gewinn als zweite Kennzahl hinzufügen, sofern lesbar.
- Ringdiagramm: Umsatz nach `Products[Category]`.
- Regionale Umsatzanalyse nach `Customers[Region]` als Balkendiagramm. Eine Karte ist optional, nur wenn die geografischen Daten sauber erkannt werden.

**Unterer Bereich**
- Tabelle „Top 5 Products by Sales“ mit Produktname, Umsatz, Gewinn und Gewinnmarge. Filtere das Visual auf Top 5 nach Total Sales.
- Horizontales Balkendiagramm: Umsatz nach `Products[SubCategory]`.
- Ringdiagramm: Umsatz nach `Customers[Segment]`.

**Slicer**
- `Calendar[Year]`
- `Customers[Region]`
- `Products[Category]`
- `Sales[SalesChannel]`

Aktiviere sinnvolle Interaktionen und überprüfe, dass Slicer die passenden Visuals filtern.

### 6. Weitere Seiten, falls möglich
Füge zusätzliche Seiten hinzu:
- `Product Analysis`: Umsatz/Gewinn nach Produkt und Unterkategorie
- `Customer Analysis`: Umsatz nach Kundensegment und Kunde
- `Geography`: Umsatz nach Region und Stadt

Priorität hat eine korrekte Overview-Seite; zusätzliche Seiten nur, wenn die Overview fertig und getestet ist.

### 7. Qualitätssicherung
- Prüfe, dass keine Visualisierung „Daten können nicht angezeigt werden“ meldet.
- Prüfe Gesamtumsatz und Gesamtgewinn anhand der Daten. Erwartungswerte ungefähr:
  - Sales: 1.707.090,78 EUR
  - Profit: 505.273,78 EUR
  Geringfügige Abweichungen sind nicht zu erwarten; bei Abweichungen untersuche Datentypen, Filter und doppelte Beziehungen.
- Prüfe, dass die Top-5-Tabelle nach Umsatz absteigend sortiert ist.
- Prüfe Monatsachse chronologisch und nicht alphabetisch.
- Prüfe Slicer und Kreuzfilterung.
- Stelle sicher, dass negative oder fehlende Werte nicht stillschweigend verborgen werden.

### 8. Speichern und Abschlussbericht
- Speichere als `Sales_Overview.pbix` in einem neuen Ausgabeordner.
- Verifiziere den Dateinamen und den Speicherort anhand der Power-BI-Oberfläche.
- Berichte abschließend:
  1. Was tatsächlich erledigt wurde
  2. Wo die PBIX-Datei gespeichert wurde
  3. Welche Tests bestanden wurden
  4. Was offen oder manuell zu erledigen ist

## Wichtig
Diese Markdown-Datei ist eine Aufgabenanweisung. Sie kann Power BI nicht selbst bedienen. Die tatsächliche Ausführung hängt davon ab, ob ChatGPT Desktop die Windows-VM über die verfügbaren Computer-Use-Funktionen steuern kann. Falls kein Zugriff besteht, führe den Nutzer interaktiv durch die nötigen Schritte, statt Erfolg vorzutäuschen.
