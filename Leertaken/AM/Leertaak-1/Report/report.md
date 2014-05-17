# Leertaak 1 <small>Stresstest rapportage</small>

> __Groep:__ 1a
> __Auteurs:__ André Nanninga &amp; Maurits van Mastrigt
> __Datum:__ 17 mei 2014

---

# Inhoud

- Inleiding
- Verklaring programmaonderdelen
	- Infrastructuur
	- Applicatie
- Stresstest resultaten
- Bottlenecks
- Conclusie

---

# Inleiding

Dit rapport beschrijft de conclusies en bevindingen van André Nanninga en Maurits van Mastrigt tijdens het uitwerken van Leertaak 1. Zoals in de opdrachtomschrijving aangegeven, bevat dit rapport de volgende onderdelen:

- Een verklaring van de programmaonderdelen die de gevraagde functies vormgeven;
- De resultaten van de stresstest inclusief verklaring voor de maximale snelheid van de gegevenswerking, waarbij het volgende zal worden aangegeven:
	+ Een overzicht van de gebruikte systemen en infrastructuur;
	+ De gehaalde verwerkingssnelheid (aantal verwerkte berichten per seconde);
	+ Welke resource de bottleneck vormt en door welk proces dit wordt veroorzaakt;
- Een onderbouwing met behulp van de verzamelde gegevens.

De in dit rapport beschreven bevindingen zullen worden meegenomen in het uitwerken van leertaken 2 en 5.

---

# Verklaring programmaonderdelen

Onderstaand een toelichting op de verschillende programmaonderdelen. Met als eerst een beschrijving van de infrastructuur en daarop volgend meer over de gebouwde applicatie.

## Infrastructuur

<center>
	!["Infrastructuur"](Figures/infrastructure.png "Infrastructuur")
</center>

In het bovenstaande figuur is te zien waar elk onderdeel verantwoordelijk voor is. Zowel de generator, applicatie, en de database werden gedraaid op één en dezelfde desktop PC. Deze PC heeft de volgende specificaties:

	Besturingssysteem: Windows 8.1 Pro 64-bit

	CPU: AMD Athlon(tm) X4 740 Quad Core Processor
	Geheugen: 8.00GB Dual-Channel DDR3 @ 665MHz (9-9-9-24)
	Moederboord: ASRock FM2A75 Pro4-M (CPUSocket)

	Opslag: RAID 0,
		232GB Seagate ST3250318AS ATA Device
		232GB Seagate ST3250318AS ATA Device
		232GB Seagate ST3250318AS ATA Device

De __generator__ is door de Hanzehogeschool als uitvoerbaar `.jar`-bestand aangeleverd. Deze Java applicatie genereert (semi)willekeurge weerdata aan de hand van een aantal instellingen. Zo kon het aantal workers worden ingesteld, waarmee de server applicatie eenvoudig te stresstesten was.

De bovengenoemde __applicatie__ is volledig zelf ontwikkeld. Hier lagen wel een aantal vereisten aan ten grondslag. Samengevat moest er een multithreaded Java applicatie worden gebouwd, die door middel van sockets een XML stream uitleest. De ingelezen gegevens moeten vervolgens worden omgezet naar een werkbaar data formaat. Ontbrekende data moest woden gecorrigeerd en de data moest worden opgeslagen in een RDBMS. De specifieke uitwerking van de applicatie zal nader worden toegelicht in paragraaf _Applicatie_.

Voor opslag van de gegevens is er gekozen voor de bekende relationele database __MySQL__. Met name het makkelijke opzetten van deze database, en de uitgebreide online hulpmiddelen, heeft hier de doorslag in gegeven (ten opzichte van PostgreSQL).

---

## Applicatie

In deze paragraaf zullen de verschillende onderdelen van de applicatie in volgorde van verloop worden toegelicht.

### Runner

De zogeheten "Runner" van de applicatie dient als startpunt van de applicatie. Hierin wordt de applicatie geconfigureerd en worden de initiële onderdelen, zoals de Server, opgezet. Tevens dient de _Runner_ klasse voor het meten van de resultaten, waarbij de server een X aantal seconden wordt gedraaid - terwijl ondertussen metingen worden verricht als:

- Ingevoerde configuratiewaarden
- Uiteindelijke looptijd (inclusief afronden,)
- Geheugengebruik
- Aantal workers (inkomende connecties)
- Aantal database queries
- Verwachtte aantal records
- Uiteindelijke aantal records
- Efficiëntie

Met deze meeteenheden kan applicatiebrede efficiëntie worden gemeten, wat zeer heeft geholpen bij het verbeteren van de applicatie code.

### Server

De _Server_ klasse beheert de database connectie en accepteert continue inkomende connecties, welke worden overgezet naar een aparte _Worker_ voor elke connectie. Deze connecties komen voor nu nog vanaf de generator.

### Database

De _Database_ klasse zorgt voor zowel het tot stand brengen van een verbinding met de MySQL database, als eenvoudige communicatie met deze database. Tevens maakt deze laag van abstractie het wisselen van database mogelijk, wat voordelig is (met oog op de tweede leertaak).

Voor het optimaal uitvoeren van de "INSERT" queries, waarbij de ingelezen weerdata wordt ingeschoten in de database, is er gekozen om deze uit te laten voeren door een aparta klasse: _Database.Executor_. De database klasse maakt een _Executor_ instantie aan voor elke query die wordt gedraaid, waardoor er eenvoudig in één keer een grote hoeveelheid aan records ingeschoten kan worden.

### Database.Executor

Voor optimaal verwerkingssneldheden is er gekozen voor het maken van een _Executor_, waarin een database query in een aparte thread wordt uitgevoerd. Dit voorkomt dat de applicatie blokkeert tijdens het inschieten van de ingelezen weerdata.

### Worker

Accepteert inkomende data, verwerkt en corrigeert de data (middels een Corrector), en schiet deze in de database in (middels een RecordBuffer).

_Bevat een instantie van: Corrector en RecordBuffer._

### Corrector

Corrigeert ontbrekende of afwijkende data door middel van extrapolatie.

### RecordBuffer

Houdt een X aantal records vast, om deze één database query te kunnen inschieten (batch).

_Bevat een instantie van: Database._

### Record

Helper voor waarden in een record object. voor het bepalen van missende waarden en het omzetten naar een database "INSERT" query.

---

# Stresstest resultaten

De stress is meerdere malen uitgevoerd met een doorloop tijd van 30 seconden. De resultaten hiervan zijn als volgt:

| Clusters      | Geheugen  | Queries | Aantal records | Verwacht aantal records | Efficiëntie |
| :------------ | :-------- | :------ | :------------- | :---------------------- | :---------- |
| 800           | 499.50 MB | 872     | 246480         | 240000                  | 102.70%     |
| 800           | 557.00 MB | 872     | 247910         | 240000                  | 103.30%     |
| 800           | 507.00 MB | 879     | 248000         | 240000                  | 103.33%     |
| 800           | 509.00 MB | 881     | 248000         | 240000                  | 103.33%     |
| 800           | 499.50 MB | 888     | 247480         | 240000                  | 103.12%     |
| __Gemiddeld__ |           |         |                |                         |             |
| 800           | 514.40 MB | 878.4   | 247574         | 240000                  | 103.16%     |

De verwerkingssnelheid van de applicatie is hoog genoeg om alle 800 clusters die de generator kan simuleren af te handelen. De reden dat de efficiëntie boven 100% is omdat de workers niet direct worden gestopt en zo dus nog een klein beetje data kunnen ontvangen.

Tijdens het uitvoeren van de stresstest verbruikt de applicatie ongeveer 50% cpu tijd, de MySQL database verbruikt slechts 5%. Qua geheugen gebruik zien we dat MySQL 1.5GB in beslag neemt en de applicatie 700MB. Verder schrijft de MySQL database ongeveer 4MB/s weg naar de harde schijven.

Uit een stresstest van een half uur kwamen de volgende getallen:

| Clusters | Geheugen  | Queries | Aantal records | Verwacht aantal records | Efficiëntie |
| :------- | :-------- | :------ | :------------- | :---------------------- | :---------- |
| 800      | 498.00 MB | 24019   | 12428690       | 14400000                | 86.31%      |
| 700      | 500.00 MB | 23756   | 12336400       | 12600000                | 97.91%      |
| 600      | 497.50 MB | 20688   | 10804980       | 10800000                | 100.05%     |

Uit deze data lijkt het dat het aantal queries op de database een bottleneck is. Het limiet wat de MySQL database aan over een half uur lijkt rond de 23.000 queries te liggen.

Een aanpassing aan grootte van de buffer in de RecordBuffer zorgt ervoor dat er minder queries worden verstuurd maar deze queries wel meer data bevatten. De resultaten van een stresstest van een half uur met een grotere buffer zijn als volgt:

| Clusters | Geheugen  | Queries | Aantal records | Verwacht aantal records | Efficiëntie |
| :------- | :-------- | :------ | :------------- | :---------------------- | :---------- |
| 800      | 645.50 MB | 15597   | 14408000       | 14400000                | 100.06%     |

Het aantal queries is drastisch gedaalt en daarmee is de efficientie weer op 100% gekomen. Hier staat wel tegen over dat de applicatie meer geheugen in beslag neemt, 650MB tegenover 500MB, maar dat levert geen verdere problemen op.

Te concluderen valt dat de grootste bottleneck momenteel nog de MySQL database is. Hoewel uiteindelijk deze toch de ruwweg 8000 records per seconde aan kan blijkt dit toch het punt te zijn waar het het eerste fout gaat. De vraag is hoe goed de MySQL database mee schaalt wanneer meer clusters worden gebruikt en wanneer de applicatie langer draait.

## Machine resources tijdens stresstesting

<center>
  !["CPU usage"](Figures/cpu-usage.png "CPU usage")
  !["Memory usage"](Figures/memory-usage.png "Memory usage")
  !["Overall usage"](Figures/overall-usage.png "Overall usage")
</center>

---

# Bottlenecks

- Worker blocking
- Slow Corrector
- Efficient Parsen
- MySQL
- Query commiting

# Conclusie

Het doel van 8000 records per seconde verwerken is uiteindelijk gehaald omdat er gefocust is op het scheiden van data en taken. Elk onderdeel van de applicatie is zoveel mogelijk zelfstandig gemaakt zodat deze niet op een ander onderdeel hoeft te wachten. Deze opzet heeft er voor gezorgd dat de applicatie een grote hoeveelheid data kan ontvangen, verwerken en op kan slaan in een database.