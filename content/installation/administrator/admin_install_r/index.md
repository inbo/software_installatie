---
title: "Install R"
description: "Instruction for the installation of R (in Dutch)"
date: "2022-05-19"
authors: [thierryo]
categories: ["installation"]
tags: ["r", "installation"]
output: 
  md_document:
    preserve_yaml: true
    variant: gfm+footnotes
---



## Windows

Installatiebestand beschikbaar via [cloud.r-project.org](https://cloud.r-project.org/bin/windows/base)

In de onderstaande tekst moet je in `R-4.x.y` zowel `x` als `y` vervangen door een cijfer om zo het huidige versienummer te krijgen. 
Dus voor versie `R-4.0.0` is `x` = 0 en `y` = 0.

### Nieuwe installatie van R

1. Voer het bestand _R-4.x.y-win.exe_ uit.
1. Kies _Nederlands_ als taal voor de installatie en klik op _OK_.
1. klik op _Volgende_.
1. Aanvaard de licentievoorwaarden door op _Volgende_ te klikken.
1. Wijzig de standaarddoelmap naar `C:\R\R-4.x.y` en klik op _Volgende_.
1. Selecteer de gewenste componenten en klik op _Volgende_. 
Je **MOET** deze standaardwaarden laten staan.
1. Opstartinstelling aanpassen: Kies `Nee` en klik op _Volgende_.
1. Geef de map voor het start menu en klik op _Volgende_. 
Je mag de standaardwaarde gebruiken.
1. Vink de gewenste extra snelkoppelingen _aan_ (default is ok), alle register entries _aan_ en klik op _Volgende_.
1. R wordt nu geïnstalleerd.
Klik op _Voltooien_ als de installatie afgelopen is.
1. Ga naar `Start` en tik "Omgevingsvariabelen" in het veld `Programma's en variabelen zoeken`. 
Selecteer `De omgevingsvariabelen van het systeem bewerken`. 
Selecteer het tabblad `Geavanceerd` en klik op de knop `Omgevingsvariabelen`. 
Ga na of er een systeemvariabele `R_LIBS_USER` met waarde `C:/R/library` bestaat^[Het moeten forward slashes zijn.]. 
Indien niet, maak deze aan met de knop `Nieuw`. 
Sluit al deze schermen via de `OK` knop.
1. Kopieer het bestand [`Rprofile.site`](Rprofile.site) naar `etc` in de doelmap waar je R geïnstalleerd hebt (`C:\R\R-4.x.y`). 
Hierbij moet je het bestaande bestand overschrijven.
1. Zorg dat de gebruiker schrijfrechten heeft voor `C:\R\R-4.x.y\library` en `C:\R\library`.

#### Afwijkingen t.o.v. default installatie

- Wijzig de standaarddoelmap naar `C:\R\R-4.x.y`
- **alle** gebruikers moeten **volledige** rechten hebben in 
    - `C:\R\library`
    - `C:\R\R-4.x.y\library`
- Systeemvariable `R_LIBS_USER` instellen op `C:/R/library` (**verplicht forward slashes**)
- [`Rprofile.site`](Rprofile.site) in `C:\R\R-4.x.y\etc` overschrijven

**R mag niet met admininstratorrechten gestart worden.** 
Anders worden een aantal packages met administrator rechten geïnstalleerd waardoor de gebruiker ze niet meer kan updaten.

Start `R` als een gewone gebruiker om de configuratie te testen.

### Upgrade van een bestaande R installatie

**Deze instructies veronderstellen dat R en RStudio in het verleden reeds geïnstalleerd werden volgens de bovenstaande instructies.
Indien dat niet het geval is, volg dan de instructies voor een nieuwe installatie.**

1. Voer het bestand _R-4.x.y-win.exe_ uit.
1. Kies _Nederlands_ als taal voor de installatie en klik op _OK_.
1. klik op _Volgende_.
1. Aanvaard de licentievoorwaarden door op _Volgende_ te klikken.
1. Wijzig de standaarddoelmap naar `C:\R\R-4.x.y` en klik op _Volgende_.
1. Selecteer de gewenste componenten en klik op _Volgende_.
Je **MOET** deze standaardwaarden laten staan.
1. Opstartinstelling aanpassen: Kies `Nee` en klik op _Volgende_.
1. Geef de map voor het start menu en klik op _Volgende_. Je mag de standaardwaarde gebruiken.
1. Vink de gewenste extra snelkoppelingen _aan_ (default is ok), alle register entries _aan_ en klik op _Volgende_.
1. R wordt nu geïnstalleerd.
Klik op _Voltooien_ als de installatie afgelopen is.
1. Kopieer het bestand [`Rprofile.site`](Rprofile.site) naar `etc` in de doelmap waar je R geïnstalleerd hebt (`C:\R\R-4.x.y`).
Hierbij moet je het bestaande bestand overschrijven.
1. Zorg dat de gebruiker schrijfrechten heeft voor `C:\R\R-4.x.y\library`
1. De nieuwe R versie is klaar voor gebruik. De gebruiker moet `RStudio` bijwerken.

**R mag niet met admininstratorrechten gestart worden.**
Anders worden een aantal packages met administrator rechten geïnstalleerd waardoor de gebruiker ze niet meer kan updaten.

Start `R` als een gewone gebruiker om de configuratie te testen.

### Inhoud `Rprofile.site`


```
options(
  papersize = "a4",
  tab.width = 2,
  width = 80,
  help_type = "html",
  keep.source.pkgs = TRUE,
  xpinch = 300,
  ypinch = 300,
  yaml.eval.expr = TRUE,
  repos = c(
    CRAN = "https://cloud.r-project.org/",
    INLA = "https://inla.r-inla-download.org/R/stable",
    inbo = "https://inbo.r-universe.dev"
  ),
  pkgType = "binary",
  install.packages.check.source = "no",
  install.packages.compile.from.source = "never",
  inbo_required = c("checklist", "fortunes", "remotes", "INBOmd", "INBOtheme")
)
# display fortune when starting new interactive R session
if (interactive() && "fortunes" %in% rownames(utils::installed.packages())) {
  tryCatch(
    print(fortunes::fortune()),
    error = function(e) {
      invisible(NULL)
    }
  )
}

if ("checklist" %in% rownames(utils::installed.packages())) {
  options(
    lintr.linter_file = system.file("lintr", package = "checklist")
  )
}

if (
  !all(getOption("inbo_required") %in% rownames(utils::installed.packages()))
) {
  stop(
    c(
      "\n",
      rep("^", getOption("width")),
      "\nThis R installation lacks some required INBO packages.",
      "\nPlease install them using the code below:\n",
      "\ninstall.packages(c(",
      paste0(
        "\"",
        getOption("inbo_required")[
          !getOption("inbo_required") %in% rownames(utils::installed.packages())
        ],
        "\"", collapse = ", "
      ),
      "))\n\n",
      rep("^", getOption("width"))
    )
  )
}
```

## Ubuntu

Instructies om R te installeren onder Ubuntu zijn beschikbaar op https://cran.r-project.org/bin/linux/ubuntu.
Na de installatie kopier je [`Rprofile.site`](Rprofile.site) naar `/etc/R`.
