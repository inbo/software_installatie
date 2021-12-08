---
title: "RStudio Desktop installation"
description: "Installation instructions for RStudio Desktop (in Dutch). RStudio is an integrated development environment (IDE) for R. It includes a console, syntax-highlighting editor that supports direct code execution, as well as tools for plotting, history, debugging and workspace management."
date: 2017-10-18T00:30:40+02:00
categories: ["installation"]
tags: ["r", "rstudio", "installation"]
---

De installatiebestanden voor de stabiele versies zijn beschikbaar via http://www.rstudio.com/products/rstudio/download/. 
De preview versie is beschikbaar via https://www.rstudio.com/products/rstudio/download/preview/ 

## Windows

### Nieuwe installatie en upgrade van RStudio

RStudio upgraden doe je door de nieuwe versie te installeren over de oude.

1. Zorg dat eerst `R` geïnstalleerd is.
1. Voer het 64-bit installatiebestand uit.
1. Klik _Ja_.
1. Welkom bij de installatie: klik op _volgende_.
1. Geef de doelmap en klik op _volgende_. 
Je mag de standaard gebruiken.
1. Klik op _installeren_.
1. Klik op _voltooien_.
1. Kopieer het bestand [`rstudio-prefs.json`](rstudio-prefs.json) naar de verborgen map `AppData/Roaming/RStudio` in de persoonlijke map van de gebruiker (`C:/users/username`).

**RStudio mag niet met admininstratorrechten gestart worden.** 
Anders worden een aantal R packages met administrator rechten geïnstalleerd waardoor de gebruiker ze niet meer kan updaten.

Test de configuratie door RStudio te starten als een **_gewone_** gebruiker.

### Afwijkingen t.o.v. default installatie

- Geen
