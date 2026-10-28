# Dashboard-Layout: Sales Overview

## Seite 1 – Overview
**Canvas:** 16:9, heller Hintergrund, einheitliche Abstände, dezente Rahmen.

### KPI-Karten (erste Zeile)
1. Total Sales – `[Total Sales]`, Währung EUR, 1–2 Dezimalstellen je nach Bedarf
2. Total Profit – `[Total Profit]`, Währung EUR
3. Total Orders – `[Total Orders]`, Ganzzahl mit Tausendertrennzeichen
4. Average Order Value – `[Average Order Value]`, EUR
5. Total Customers – `[Total Customers]`, Ganzzahl

### Diagramme
- **Sales and Profit by Month:** gruppiertes Säulendiagramm oder Kombinationsdiagramm. Achse: `Calendar[Year Month]`; Werte: `[Total Sales]` und `[Total Profit]`. Sortierung chronologisch.
- **Sales by Category:** Ringdiagramm. Legende: `Products[Category]`; Werte: `[Total Sales]`.
- **Sales by Region:** Balkendiagramm nach `Customers[Region]` oder Kartenvisual, falls geografische Darstellung verfügbar ist. Nicht von Stadt-/Regionsnamen auf genaue Geografie schließen.
- **Top 5 Products by Sales:** Tabelle mit `Products[ProductName]`, `[Total Sales]`, `[Total Profit]`, `[Profit Margin %]`. Visualfilter Top N = 5 nach `[Total Sales]`.
- **Sales by SubCategory:** horizontales Balkendiagramm; Achse `Products[SubCategory]`, Wert `[Total Sales]`.
- **Sales by Customer Segment:** Ringdiagramm; Legende `Customers[Segment]`, Wert `[Total Sales]`.

### Slicer
- `Calendar[Year]`
- `Customers[Region]`
- `Products[Category]`
- `Sales[SalesChannel]`

## Formatierung
- Währung: EUR (€), nicht USD.
- Grün/Rot für positive/negative Abweichungen; Farbe nicht als einziges Bedeutungssignal verwenden.
- Diagrammtitel eindeutig und Achsen beschriften.
- Bei Monatsdiagrammen `Calendar[Year Month]` chronologisch sortieren.
- Interaktionen zwischen Visuals prüfen; unnötige Kreuzfilterung deaktivieren.

## Datenmodell
`Products` 1 → * `Sales` ← * 1 `Customers`
`Calendar` 1 → * `Sales`

Das Modell ist bewusst als Sternschema aufgebaut: Dimensionstabellen filtern die zentrale Faktentabelle `Sales`.
