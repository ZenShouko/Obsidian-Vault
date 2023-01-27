---
#Data #Examen #Solution
---
Data Essentials - Voorbeeld examen

[1]: Geef een overzicht volgens onderstaande resultaatquery van alle medewerkers en bepaal de nieuwe wedde als volgt:
• de naam De Koning: 30% verhoging;
• de functie Boekhouder: 15% verhoging;
• de functie Trainer: 5% verhoging;
• de functie Manager: 20% verhoging.
Druk het resultaat af in volgorde van de functie: eerst de directeur, dan de managers, dan de rest in aflopende volgorde van de naam. Het veld medewerker bevat de naam én voornaam. De naam moet enkel met beginhoofdletter weergegeven worden

# Tabel voor de update:
``` Oracle
       MNR NAAM            VOORN        FUNCTIE          CHEF GBDATUM       MAANDSAL       COMM        AFD
---------- --------------- ------------ ---------- ---------- ----------- ---------- ---------- ----------
      7369 CASPERS         JANA         TRAINER          7902 17-DEC-1985       1800                    20
      7499 ALLARD          NELE         VERKOPER         7698 20-FEB-1981       1600       3000         30
      7521 DEFOUR          THOMAS       VERKOPER         7698 22-FEB-1982       2250       5000         30
      7566 JACOBS          EMMA         MANAGER          7839 02-APR-1987       4975                    20
      7654 MARTENS         RAF          VERKOPER         7698 28-SEP-1976       2250       3400         30
      7698 BRIERS          ANDREA       MANAGER          7839 01-NOV-1983       5850                    30
      7782 CLERCKX         AN           MANAGER          7839 09-JUN-1985       3450                    10
      7788 SWINNEN         CHRIS        TRAINER          7566 26-NOV-1979       4000                    20
      7839 DE KONING       LIEVE        DIRECTEUR             17-NOV-1972       7000                    10
      7844 DEN RUYTER      JOACHIM      VERKOPER         7698 28-SEP-1988       2500          0         30
      7876 SLECHTEN        TOM          TRAINER          7788 30-DEC-1986       2700                    20
      7900 JACOBS          SIMON        BOEKHOUDER       7698 03-DEC-1989       2800                    30
      7902 DE COOMAN       DORIEN       TRAINER          7566 13-FEB-1979       4000                    20
      7934 WOUTERS         SVEN         BOEKHOUDER       7782 23-JAN-1982       2300                    10
```


# Tabel ná update:
Medewerker                   FUNCTIE    Nieuwe Wedde
---------------------------- ---------- ------------
DE KONING LIEVE              DIRECTEUR          9100
JACOBS EMMA                  MANAGER            5970
CLERCKX AN                   MANAGER            4140
BRIERS ANDREA                MANAGER            7020
WOUTERS SVEN                 BOEKHOUDER         2645
SWINNEN CHRIS                TRAINER            4200
SLECHTEN TOM                 TRAINER            2835
MARTENS RAF                  VERKOPER           2250
JACOBS SIMON                 BOEKHOUDER         3220
DEN RUYTER JOACHIM           VERKOPER           2500
DEFOUR THOMAS                VERKOPER           2250
DE COOMAN DORIEN             TRAINER            4200
CASPERS JANA                 TRAINER            1890
ALLARD NELE                  VERKOPER           1600

 - Stap 1: Update de maandsal.
```
update medewerkers
 	set maandsal = maandsal * 1.3
 	where naam = 'De Koning';
 - update medewerkers
 	set maandsal = maandsal * 1.15
 	where functie = 'BOEKHOUDER';
 • update medewerkers
 	set maandsal = maandsal * 1.05
 	where functie = 'TRAINER';
 • update medewerkers
 	set maandsal = maandsal * 1.2
 	where functie = 'MANAGER';
```

 - Stap 2: Toon resultaten. Eerst the directeurs dan the managers en als laatste sorteren op naam descending.
 	(Toon naam + voornaam als medewerker, functie en maandsal als nieuwe-wedde)
 select initcap(naam) || ' ' || voorn as "Medewerker", functie, maandsal as "Nieuwe Wedde" from medewerkers
 Order By
 case
 	when functie = 'DIRECTEUR' then 1
 	when functie = 'MANAGER' then 2
 	else 3
 End ASC,
 naam desc;

[2]: Geef de namen van de medewerkers die in de maanden november of december geboren zijn en die meer verdienen dan € 2500. Toon tevens ook de naam van de afdeling waarvoor zij werken.
 (Toon naam, voorn, Gbdatum, maandsal, [NAAM from AFDELINGEN])

 - Stap 1: Selecteer alle medewerkers die geborden zijn in november of december
 select * from medewerkers where extract(month from gbdatum) in (11, 12);

 - Stap 2: Filter nu op basis van maandsal > 2500
 select * from medewerkers where extract(month from gbdatum) in (11, 12)
 AND maandsal > 2500;

 - Stap 3: Toon de naam van de afdelingen waarvoor ze werken. (Naam from Afdelingen)
  select m.naam, m.voorn, m.gbdatum, m.maandsal, a.naam
  from medewerkers m, afdelingen a
  where extract(month from m.gbdatum) in (11, 12)
  AND m.maandsal > 2500
  AND m.afd = a.anr
  order by a.naam, m.maandsal;


[3]:  Wat is de gemiddelde commissie van de verkopers wanneer de verkopers die momenteel geen commissie (= 0) hebben een commissie zouden hebben van 200 EUR?

- Stap 1: Bereken de gemiddelde commisie en vervang NULL waardes dor 200
  select AVG(
  case
      when comm = 0 then 200
      else comm
END) as "Gem. Waarde"
from medewerkers;

- Stap 2: Filter ze door alleen te kijken naar de verkopers.
    select AVG(
  case
      when comm = 0 then 200
      else comm
END) as "Gem_Waarde"
from medewerkers
where functie = 'VERKOPER';


[4] Welke medewerkers zijn ouder dan hun chef?

- Stap 1: Laten we beginnen met het selecteren van alle medewerkers samen met hun chef naast hun
select m.naam || ' ' || m.voorn as Medewerker, mm.naam as chef
from medewerkers m
left join medewerkers mm
on m.chef = mm.mnr;

- Stap 2: Filter deze medewerkers op basis van of ze ouder zijn dan hun baas
-- Remember 1990 < 2001 => Als gbdatum kleiner is, dan is medewerker ouder
select m.naam, m.voorn
from medewerkers m
left join medewerkers mm
on m.chef = mm.mnr
where m.gbdatum < mm.gbdatum;

[5] Welke medewerkers hebben zowel de cursus WEB als SQL gevolgd? Geef hun cursistennummer.
-- Cursistennummer = SELECT Cursist FROM Inschrijvingen
SELECT cursist
FROM inschrijvingen
WHERE cursus IN ('SQL', 'WEB')
GROUP BY cursist
HAVING COUNT(DISTINCT cursus) = 2;