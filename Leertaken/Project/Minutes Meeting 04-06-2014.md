# Minutes Meeting 04-06-2014

## Announcements
Markus Wind was absent due to illess.

## General information
Aleksandro Stulginskio Universitetas will perform calculations on weather data to conduct research on climate change. The university is interested in climate change in Lithuania and other countries around the Baltic Sea.

Would like 'sleet' measurements.

Interested in the following measurements:
- Wind speed
- Rain fall
- Temperature
- Humidity

Countries: 'Lithuania, Denmark, Germany, Poland, Kalingrad (Russia), Estonia, Latvia, Finland, Sweden.'

Want a view to display the top 10 of weather stations by highest average wind speed.

## Technical

#### Queries:
1. __Top 10 by Wind Speed.__  
Show top 10 of Weather Stations determined by highest average wind speed. Only stations within 50 km of coastline.
2. __Rain fall above 10 mm within 50 km.__  
Show data about rain fall when it is above 10 mm. Don't show when rain fall is below that. Only stations within 50 km of coastline.
3. __Graphs of temperature and humidity.__  
Be able to show a graph of temperature and humidity of any individual weatherstation. (Since previous midnight). Any weatherstation worldwide.

#### Key requirements
- Cockpit-like graphical presentation, no 'boring' tables.
- History of data by days, weeks and months. 
- Half a year (6 months) data retention.
- Support Internet Explorer 11 and Google Chrome.
- We host data and webapp.
- Map with colour code for wind speed and temperature values.
- Authenticate using username and password.
- Allow for data to be downloaded in Excel file for the past half a year in days/weeks/months.
- ONLY measurements per minute.
- Username and Password authentication.
- SSL to secure safe data transport.

## Service

#### Security
- Customer will not store username and password locally.
- 50 users, no segregation in privileges.

#### Availability
Three different levels of availability:

- Query 1: High. (99%) between 13:00 and 15:00
- Query 2: Moderate. (97%) every week on Monday before 12:00
- Query 3: Moderate. (97%) every week on Monday before 12:00

#### Lost data
- Query 1: Back-up of the last 2 hours.
- Query 2: Back-up of all data received that day from 00:00 (midnight, local time in Kaunas).
- Query 3: Back-up of all data received that day from 00:00 (midnight, local time in Kaunas).

#### Load time for queries
- Query 1: 10 seconds.
- Query 2: 1 minute.
- Query 3: 1 minute.

#### Language
The entire interface and data will be in English.

#### Reporting
Monthly service reports on availability.

#### Contract
Initially, one-year contract. Possibility of increasing this to 5 years.

#### Service desk
- Service desk will be available on Monday to Friday from 7:00 to 20:00 (local time in Kaunas).
- Response time will depend on package. Maximum response time 2 hours.

#### Changes
- The amount of changes will depend on package. At least 6 changes a year will be allowed on any package.

## Agreements

1. Email minutes
2. Email suggestion and representations for type of graphs

## Any other business
none

## Unscheduled business
none

## Arrangements next meeting
- Minutes will be sent within 2 working days by email in PDF or Word format.
- Next meeting will be on the 26th of June.
- We will have a fully working application for the next meeting.
- Can email Chancellor Stadman and Acting Vice Chancellor Wempe 24/7 for questions.