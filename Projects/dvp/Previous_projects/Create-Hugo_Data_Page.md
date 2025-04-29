---
title: "Create Hugo Data Page"
date: 2022-12-29T14:28:56Z
draft: false
---

## Create Data YAML file
```
nano data/logbook.yaml
```
Put this into the file
```
##################
# logbook.yaml #
##################

---
items:

- id: 1
  "maidenhead_location": "KG44dd58"
  "power": "100"
  "band": "20M"
  "mode": "J3E"
  "antenna": "20M End Fed halfwave"
  "date": "2022-12-16"
  "time_sast": "17:19"
  "frequency_mhz": "14.225"
  "callsign": "V51WW"
  "name": "Wynand"
  "location": "Nr Etosha"
  "sent_rst": "59+10dB"
  "received_rst": "57"
  "notes": "we had a great and long QSO"

- id: 2
  "maidenhead_location": "KG44dd58"
  "power": "100"
  "band": "20M"
  "mode": "J3E"
  "antenna": "20M End Fed halfwave"
  "date": "2022-12-16"
  "time_sast": "17:19"
  "frequency_mhz": "14.225"
  "callsign": "ZS1ELA"
  "name": "Wynand"
  "location": "Paketburg"
  "sent_rst": "59"
  "received_rst": "57"
  "notes": "we had a great and long QSO"

```
## Creating the Shortcode
Run this command:
```
mkdir layouts/shortcodes
```

Run this command:
```
nano layouts/shortcodes/logbook.html
```
put this into file
```
{{range .Site.Data.logbook.items}}
<h3>{{.callsign}}<h3>
My Location: {{.maidenhead_location}}<br>
Power: {{.power}}<br>
Band: {{.band}}<br>
Mode: {{.mode}}<br>
Antenna: {{.antenna}}<br>
Frequency {{.frequency_mhz}}<br>
Date: {{.date}}<br>
Time: {{.time_sast}} (SAST UTC+2)<br> 
Name: {{.name}}<br>
Location: {{.location}}<br>
Sent RST: {{.sent_rst}}<br>
Notes: {{.notes}}<br>
{{end}}
```
## publish this code in a md file ZS6BVR-QSLS.md
```
edit-md-page ZS6BVR-QSLS.md
```
put this content in this page

```
{{^logbook>}}
```
Replace the above ^ with a <
