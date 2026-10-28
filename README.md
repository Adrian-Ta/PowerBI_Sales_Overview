# Power BI Sales Overview – Starterprojekt

Dieses Paket enthält realistische, synthetische Beispieldaten für ein Sales-Overview-Dashboard. Es ist ein Starterpaket für Power BI Desktop, **keine fertige PBIX-Datei**.

## Dateien
- `Sales.csv`: 2.600 Bestellpositionen von 01.01.2025 bis 23.10.2026
- `Products.csv`: Produktstamm mit Kategorie, Unterkategorie, Einkaufskosten und Listenpreis
- `Customers.csv`: Kundenstamm mit Segment und Region
- `PowerQuery_M.txt`: M-Code zum Importieren der drei CSV-Dateien
- `DAX_Measures.txt`: empfohlene DAX-Kennzahlen
- `Dashboard_Layout.md`: Visualisierungsaufbau und Formatierung

Alle Daten sind frei erfunden und dienen nur zu Lern- und Demonstrationszwecken. Werte sind beispielhaft und nicht für geschäftliche Entscheidungen gedacht.

## Schnellstart in deiner Windows-VM
1. ZIP-Datei in Windows entpacken, zum Beispiel in `C:\PowerBI\SalesOverview`.
2. Power BI Desktop öffnen → **Startseite > Daten abrufen > Text/CSV**.
3. `Sales.csv` auswählen → **Daten transformieren**. Später die beiden anderen CSVs auf dieselbe Weise importieren.
4. Datentypen prüfen: `OrderDate` = Datum; Mengen = ganze Zahl; Umsatz/Kosten/Gewinn = Dezimalzahl bzw. feste Dezimalzahl.
5. In der Modellansicht Beziehungen anlegen:
   - `Products[ProductID]` (1) → `Sales[ProductID]` (*)
   - `Customers[CustomerID]` (1) → `Sales[CustomerID]` (*)
   Filterrichtung: einfach von den Dimensionen zur Sales-Tabelle.
6. In `DAX_Measures.txt` die Measures nacheinander in der Tabelle `Sales` über **Modellierung > Neue Maßnahme** anlegen.
7. Das Layout aus `Dashboard_Layout.md` umsetzen. Ein Bericht kann mehrere Seiten haben.

## Empfohlene Seiten
- Overview: KPI-Karten, Monatsumsatz/Gewinn, Kategorien, Regionen, Top-Produkte
- Product Analysis: Produkt- und Unterkategorieanalyse
- Customer Analysis: Segmente, Kunden und Umsatz
- Geography: Region und Stadt

## Hinweis zu den Daten
Die Werte für `Sales`, `Cost` und `Profit` sind bereits auf Positionsebene berechnet. Die Gewinnmarge im Dashboard sollte als `Profit / Sales` berechnet werden, nicht als Durchschnitt der Zeilenmargen.
