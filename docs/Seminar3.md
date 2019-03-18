---
title: "Seminar3"
output: 
  html_document:
    keep_md: TRUE
---



# Velkommen til 3. Seminar!

F�rst: Er det noen sp�rsm�l/kommentarer til hjemmeoppgavene?

I dagens seminar skal vi dekke f�lgende emner:

1. [Dataanalyse i praksis](#praksis)
2. [Omkoding](#omkoding)
3. [Eksperimentell logikk](#eksperiment)
4. [Aggregering og arrangering av data med dplyr](#dplyr)


## Dataanalyse i praksis

I dette, og neste seminar, skal vi gj�re ekte forskning (riktignok forskning andre har gjort f�r oss). Vi skal jobbe med replikasjon av en publisert forskningsartikkel: 
*Burnside, C., & Dollar, D. (2000). Aid, policies, and growth. American economic review, 90(4), 847-868.*

Valget av denne artikkelen er ikke tilfeldig, den er sv�rt godt egnet til � l�re dataanalyse med line�r regresjon. Videre er datasettetsom ble brukt i artikkelen offentlig tilgjengelig.

Vi skal se n�rmere p� en av hypotesene til **Burnside og Dollar (2000)**: *Effekten av bistand p� �konomisk utvikling avhenger av den makro�konomiske politikken som f�res i landet som f�r bistand*.  
Denne hypotesen testes i regresjonsmodell 5 fra tabell 4 p� s. 856. Vi skal se p� den f�rste spesifikasjonen av denne modellen (kolonnen **OLS**), som er en multivariat, line�r regresjon med samspill. 

I dagens seminar, skal vi jobbe med � forst� grunnlaget for slutningene regresjonsmodell 5 gir oss, uten � gj�re selve regresjonsanalysen. Vi kan skille mellom 2 fremgangsm�ter � gj�re dette p�:

1. **Teoretisk**: les artikkel og kodebok (i dette tilfellet i artikkelen), og tenk gjennom grunnlaget for slutningene. Dette inneb�rer � stille sp�rsm�l som dette: Hvilke valg (f.eks. spesifikasjon av variabler) tar forfatterne for � kunne teste hypotesen sin? Hvilke andre valg kunne forfatterne tatt? Gj�r forfatterne noen implisitte antagelser gjennom valgene sine? Er begrunnelsene for valgene forfatterne tar gode? Dersom du fikk tilgang p� all informasjon du hadde lyst p�, og kunne lage et eksperiment, hvordan ville du godt frem? Hvordan skiller den faktiske analysen seg fra den ideelle analysen? 
2. **Vurdering av den empiriske slutningen (ved hjelp av R)**: Vi kan bruke deskriptiv statistikk, plotting og regresjonsdiagnostikk til � �ke forst�elsen v�r av datagrunnlaget for slutningen fra modell 5. Ved � studere data og statistikker for regresjon n�rmere, kan vi f� et inntrykk av om sammenhengen virker robust. Videre kan vi teste konsekvensen av � gj�re andre valg enn det forfatterne av en artikkel gjorde, for eksempel ved � legge inn eller fjerne kontrollvariabler, eller kode variabler p� andre m�ter enn det forfatterne gjorde. Dersom vi synes at kodingen av en variabel er vilk�rlig fordi den mangler en god begrunnelse,  kan vi teste om slutningene regresjonsmodellen gir er robust til omkoding av variabelen. 

En god vurdering av den empiriske st�tten for en statistisk modell b�r inneholde b�de en vurdering av det teoretiske grunnlaget for slutningen, og en vurdering av den faktiske empiriske slutningen. Selv om vi ofte f�r informasjon som gir oss noe grunnlag for � vurdere den empiriske slutningen, er det stort sett alltid mer � hente ved � unders�ke grunnlaget for den statistiske slutningen selv. 

I oppgavene til dagens seminar skal vi f�rst og fremst se p� univariate og bivariate sammenhenger, men det er ogs� noen multivariate slutninger. Det viktigste med tanke p� R-pr�ven, er � forst� hvordan koden fungerer, dere trenger ikke henge dere altfor mye opp i det empiriske grunnlaget for slutningen i modell 5 om dere ikke har lyst. Jeg vil likevel p�st� at det er vel verdt � bruke tid p� � forst� dataanalysene vi gjennomf�rer i dette og neste seminar, da det kan gi dere en �kt forst�else av regresjonsanalyse, og dataanalyse generelt. 

## Omkoding av variabler

For � spesifisere regresjonsmodell 5 hos **Burnside og Dollar (2000)**, m� vi f�rst gj�re noen omkodinger. Dersom dere leser artikkelen, vil dere se at n�dvendigheten av alle disse omkodingene ikke fremg�r av beskrivelsen av modellen. Det burde de ha gjort. Dersom vi ikke helt vet hvordan en regresjon er spesifisert, kan vi ikke vurdere alle forutsetningene for slutningene modellen gir. Heldigvis muliggj�r omkoding av variabler i datasettet som f�lger med artikkelen replikasjon av modell 5. Jeg skal n� vise disse omkodingene. Jeg har gjemt koden for � laste inn datasettet, siden det er den f�rste oppgaven til dagens seminar. Jeg har kalt datasettet for **aidgrowth**




Vi skal se p� eksempler p� tre forskjellige typer omkodinger. Jeg legger ut en mer komplett liste over funksjoner for omkodinger i et oversiktsdokument.

### Omkoding av variabler med matematiske transformasjoner

N�r vi omkoder variabler i et datasett, har vi lyst til � opprette en ny variabel. Dersom vi ikke gj�r dette, erstatter vi informasjonen i den opprinnelige variabelen. Informasjonen i den opprinnelige variabelen er uunv�rlig for � teste at omkodingen har fungert som vi �nsker. Den er enda mer uunv�rlig dersom vi har gjort en feil som vi ikke kan rette opp uten den opprinnelige variabelen (dette hender). Derfor er syntaksen for � omkode en variabel som f�lger:

```r
data$ny_omkodet_variabel <- funksjon_for_omkoding(data$gammel_variabel)
```

Den f�rste omkodingen vi skal gj�re er en matematisk transformasjon av en variabel. Her skal vi gj�re en logtransformasjon av BNP per capita (GDP er engelsk for BNP):


```r
aidgrowth$gdp_pr_capita_log <- log(aidgrowth$gdp_pr_capita)
# lager ny variabel, som er en logtransformasjon av eksisterende variabel i datasettet
```

N�r du har omkodet en variabel, er det lurt � sjekke at du har gjort riktig. Vi kan gj�re dette med en tabell. Dersom vi ikke spesifiserer et tilleggsargument, gir funksjonen `log()` den naturlige logaritmen til en variabel. Vi kan dermed sjekke om antilogaritmen til den omkodede variabelen som vi f�r med `exp()`, er lik den opprinnelige variabelen:


```r
# Logikken til testen
a <- log(3)
exp(a) == 3
```

```
## [1] FALSE
```

```r
# Test av omkoding:
table(exp(aidgrowth$gdp_pr_capita_log) == aidgrowth$gdp_pr_capita)
```

```
## 
## FALSE  TRUE 
##   266    58
```

Vi kunne ogs� gjort testet omkodingen p� andre m�ter. N�r man gj�r helt enkle omkodinger er det viktigste gjerne � sjekke om det har skjedd noe rart i R. Her ser vi at det kan ha skjedd noe rart, det fremg�r ogs� i koden som viser logikken til testen. La oss se n�rmere p� hva som skjer:


```r
log(3)
```

```
## [1] 1.098612
```

```r
exp(1.098612)
```

```
## [1] 2.999999
```

```r
round(exp(1.098612)) == 3
```

```
## [1] TRUE
```

Her har det skjedd en avrundingsfeil, 3 blir til `2.99999`. La oss spesifisere testen  p� nytt, med avrunding:

```r
## Ny teste av omkoding
table(round(exp(aidgrowth$gdp_pr_capita_log)) == aidgrowth$gdp_pr_capita)
```

```
## 
## TRUE 
##  324
```

Heldigvis se det ut som om onkodingen v�r virket! Denne testen viser viktigheten av � holde tungen bent i munnen, og av � forst� hva som skjer b�de i R og i testen. P� pr�ven kommer jeg ikke til � be om tester av omkodinger med denne typen R-komplikasjoner, men det er viktig � vite om at slike tilfeller kan forekomme.


### Omkoding med ifelse()

En sv�rt nyttig funksjon til omkoding, er `ifelse()`. Denne funksjonen har f�lgende syntaks: 

```r
data$ny_omkodet_variabel <- ifelse(data$gammel_variabel == "logisk test", output hvis resultat av logisk test er TRUE, output hvis resultat av logisk test er FALSE)

# Man kan spesifisere alle slags logiske tester med gammel_variabel)
```

Vi skal lage en variabel for regioner, basert p� regionsdummyene `sub_saharan_africa` og `fast_growing_east_asia`:


```r
aidgrowth$regions <- ifelse(aidgrowth$sub_saharan_africa == 1, "Sub-Saharan Africa", "Other")
aidgrowth$regions <- ifelse(aidgrowth$fast_growing_east_asia == 1, "East Asia", aidgrowth$regions)

# Tester resultat av omkoding:
table(aidgrowth$regions)
```

```
## 
##          East Asia              Other Sub-Saharan Africa 
##                 30                177                124
```

```r
table(aidgrowth$regions, aidgrowth$sub_saharan_africa)
```

```
##                     
##                        0   1
##   East Asia           30   0
##   Other              177   0
##   Sub-Saharan Africa   0 124
```

```r
table(aidgrowth$regions, aidgrowth$fast_growing_east_asia)
```

```
##                     
##                        0   1
##   East Asia            0  30
##   Other              177   0
##   Sub-Saharan Africa 124   0
```

```r
# Tabellene indikerer at omkodingen fungerte
```

### Omkoding av klasse 
Vi har lyst til at R automatisk skal lage dummyer av regionsvariabelen v�r n�r vi gj�r regresjonsanalyse. Da m� den v�re av klassen `factor`. La oss teste om variabelen har denne klassen:

```r
class(aidgrowth$regions)
```

```
## [1] "character"
```
Vi ser at variabelen har klassen `character`. Vi kan endre klasse p� en variabel med funksjoner som heter `as.klassenavn`. Her trenger vi funksjonen `as.factor`. La oss lage en ny variabel med denne omkodingen:


```r
aidgrowth$regions_f <- as.factor(aidgrowth$regions)
table(aidgrowth$regions_f)
```

```
## 
##          East Asia              Other Sub-Saharan Africa 
##                 30                177                124
```

```r
levels(aidgrowth$regions_f)
```

```
## [1] "East Asia"          "Other"              "Sub-Saharan Africa"
```

```r
## Bytter referansekategori til "Others"
levels(aidgrowth$regions_f) <- levels(aidgrowth$regions_f)[c(2,1,3)]
levels(aidgrowth$regions_f)
```

```
## [1] "Other"              "East Asia"          "Sub-Saharan Africa"
```

Vi har n� gjennomf�rt omkodingene som er n�dvendig for � spesifisere regresjonsmodell 5 i artikkelen til Burnside og Dollar 2000.


## Eksperimentell logikk

Forestill deg at du kunne gj�re akkurat hva du ville. Hvordan ville du da ha testet f�lgende hypotese: *bistand f�rer til �kt �konomisk utvikling*?

Fra forelsningen til Tore - oppskrift for kausalitet:

1. Mekanisme - Bistand gj�r at land kan investere mer i produksjonsmidler, folkehelse, utdanning og andre vekstfremmende tiltak. Land vil velge � gjennomf�re vekstfremmende tiltak med mer penger.
2. Motsatt kausalitet: Vi gjennomf�rer et eksperiment, der vi tilfeldig velger om land skal f� bistand eller ikke. Vi bestemmer ogs� at veksten land opplever ikke skal bestemme hvor mye bistandspenger de f�r (dvs. at det bare er vi som f�r gi bistand).
3. Korrelasjon - Vi skal unders�ke korrelasjonen i et eksperiment
4. Andre variable kan forklare sammenhengen - Fordi vi som forskere skal randomisere hvem som f�r bistand, er det ingen sammenheng mellom bistand og andre variabler som kan p�virke �konomisk vekst.


Vi kan tenke oss flere forskjellige varianter av et eksperiment, vi kunne for eksempel delt land inn i grupper som fikk ulik mengde bistand. Her skal vi tenke oss et sv�rt enkelt eksperiment - vi tar landene i utvalget til Burnside og Dollar. Tenk dere at disse landene er et tilfeldig utvalg, selv om de ikke er det. Del landene i en kontrollgruppe og en behandlingsgruppe. Landene i behandlingsgruppen f�r bistand, mens landene i kontrollgruppen f�r ingen bistand (heller ikke av andre). 



```r
## Eksperiment:
land <- unique(aidgrowth$country) # unique() henter ut alle unike verdier i en variabel

# Randomisering

bistand <- rnorm(n = 56) # rnorm trekker n verdier tilfeldig fra en normalfordeling

# Vi bestemmer at alle land som har verdi h�yere enn medianen til denne tilfeldige variabelen skal f� bistand, mens andre land ikke skal f� det. Denne randomiseringen gir oss en behandlingsgruppe, og en kontrollgruppe:
bistand <- ifelse(bistand> median(bistand), 1, 0)
table(bistand) 
```

```
## bistand
##  0  1 
## 28 28
```

Simulering - statisk simulering inneb�rer at vi forestiller oss en prosess med elementer av sannsynlighet, og bruker et statistikkprogramm til � unders�ke resultatet av prosessen. R lar oss gjennomf�re statistisk simulering. Vi kan ikke gjennomf�re eksperimentet vi tenkte oss over fordi vi ikke har blitt ener�dende verdensherskere enda, og fordi det er totalt uetisk. Vi kan imidlertid bruke simulering til � tenke oss hva som hadde skjedd dersom vi gjennomf�rte et slikt eksperiment.  Selv om Burnside og Dollar umulig kunne ha gjennomf�rt dette eksperimentet, er det nyttig � sammenligne det vi ideelt sett skulle ha gjort for � estimere kausaleffekter med et eksperiment, med det som faktisk gj�res. Alle vesentlige forskjeller kan bidra til � gj�re det mindre plausiblet at den ikke-eksperimentelle metoden ikke estimerer kausaleffekter korrekt.

I den statiske simuleringen er vi allmektige, vi m� definere den probabilistiske prosessen som ligger til grunn for kausaleffekten av bistand p� �konomisk vekst. La oss bestemme at effekten av bistand p� vekst er sterkt positiv i gjennomsnitt, men at effekten av bistand varierer noe mellom land - effekten er heterogen. Dette f�rer til probabilistisk kausalitet (jf. Tore sin forelesning). Jeg legger ogs� til enda et probabilistisk komponent, ved � la vekst uten bistand variere tilfeldig.


```r
## Definerer sann verdi for �konomisk vekst uten bistand, for alle land

vekst_kontroll <- rnorm(n = 56, 
                        mean= mean(aidgrowth$gdp_growth, na.rm = T),
                        sd(aidgrowth$gdp_growth, na.rm = T)) # standard normalfordeling, vekst er helt tilfeldig fordelt, men har gjennomsnitt og standardavvik som i det faktiske utvalget til Burnside og Dollar

##Introduserer kausal heterogenitet - effekten land f�r av bistand varierer mellom land, men standardavviket er bare p� 0.5
heterogenitet <- rnorm(n = 56, mean = 0, sd = 0.5)

## Definerer sann verdi for �konomisk vekst uten bistand, for alle land. Denne effekten er i utgangspunktet 2.5 for alle land, men varierer litt +/- heterogeniteten. Legger ogs� til vekst_kontroll, fordi veksten som kommer av behandling kommer p� toppen av effekten i kontrollgruppen.

vekst_behandling <- 2.5*bistand + heterogenitet + vekst_kontroll

# Den sanne effekten for hvert land, er differansen mellom vekst med bistand og uten bistand
effekt_bistand <- vekst_behandling - vekst_kontroll

# Vi kan imidlertid bare observere et av utfallene for hvert land, samme land kan ikke b�de v�re i kontrollgruppe og behandlingsgruppe. Definerer derfor den veksten vi faktisk ser

observert_vekst <- ifelse(bistand == 1, vekst_behandling, vekst_kontroll)

utvikling <- data.frame(land, bistand, vekst_behandling, vekst_kontroll, effekt_bistand, observert_vekst)
rm(land, bistand, vekst_behandling, vekst_kontroll, effekt_bistand, observert_vekst)
```


```r
# Ser p� resultater:
summary(utvikling$effekt_bistand) # den ekte effekten 
```

```
##     Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
## -0.72830  0.02858  1.28668  1.21138  2.42763  3.00169
```

```r
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
# group_by() fra dplyr lar oss aggregere - se neste seksjon!
gj_effekt <- utvikling %>% 
  group_by(bistand) %>%
  summarise(snitt = mean(observert_vekst))
# Den observerte gjennomsnittseffekten: Gjennomsnittseffekten finner vi ved � ta  differansen mellom gjennomsnittsvekst for kontroll- og behandlingsgruppe 
gj_effekt$snitt[2] - gj_effekt$snitt[1]
```

```
## [1] 4.378113
```




```r
# ser p� resultater med plot, geom_boxplot
library(ggplot2)
```

```
## Warning: package 'ggplot2' was built under R version 3.5.2
```

```r
utvikling$bistand <- as.factor(utvikling$bistand)
ggplot(utvikling, aes(x = bistand, y = observert_vekst)) + geom_boxplot()
```

![](Seminar3_files/figure-html/unnamed-chunk-14-1.png)<!-- -->



<img src="../bilder/sem3.png" width="2100" />

Merk: den observerte gjennomsnittseffekten og den sanne gjennomsnittseffekten blir likere dess st�rre utvalg vi har i eksperimentet v�rt. Test dette selv, ved � lage en ny simulering med flere land (du kan f.eks. starte fra `land <- 1:200`), og husk � endre p� n i `rnorm()`! 



 
## Aggregering og arrangering av data med dplyr

I [introduksjonen til seminar 2](https://github.com/langoergen/stv1020R/blob/master/for_seminaret/Introduksjon_seminar2.md) viste jeg hvordan du kan indeksere data med dplpyr pipelines, og funksjonene `select()` og `filter()`. Her viser jeg noen flere nyttige teknikker som kan brukes i `dplyr()` pipelines.

### Aggregering av data

Aggregering av data betyr at vi grupperer data med utgangspunkt i variabelverdier (med `group_by`), og deretter bruker ulike statistikker for hver gruppe(til dette bruker vi `summarise()`, som vi setter andre funksjoner, f.eks. `median()` inn i).

Her viser jeg hvordan vi kan gruppere bilene i `mtcars` etter antall sylindre, og regne ut gjennomsnittlig bruk av drivstoff (mpg - miles per gallon) for hver gruppe:


```r
library(dplyr)
mtcars %>%
  group_by(cyl) %>%
  summarise(mpg_snitt = mean(mpg),
            mpg_median = median(mpg)) 
```

```
## # A tibble: 3 x 3
##     cyl mpg_snitt mpg_median
##   <dbl>     <dbl>      <dbl>
## 1     4      26.7       26  
## 2     6      19.7       19.7
## 3     8      15.1       15.2
```

N�r vi aggregerer, produserer vi et nytt datasett, p� gruppeniv�. Vi bestemmer hvilke informasjon vi vil ta med oss, ved � definere variabler med `varnavn = funksjon(gammel_var)` inn i `summarise`. Her er `funksjon(gammel_var)` en funksjon som lar oss oppsummere informasjon om hver gruppe i et enkelt tall, f.eks. `sum()` eller `mean()`.


### Arrangering av data

`arrange()` lar deg ordne et datasett etter verdier p� angitte variabler. Denne funksjonen stammer ogs� fra `dplyr`, men her trenger vi ikke `%>%`, fordi vi bare utf�rer en operasjon. Ved � sette inn `desc(var)`, angir vi at vi skal sortere p� variabelen i synkende rekkef�lge, default er stigende.


```r
mtcars
```

```
##                      mpg cyl  disp  hp drat    wt  qsec vs am gear carb
## Mazda RX4           21.0   6 160.0 110 3.90 2.620 16.46  0  1    4    4
## Mazda RX4 Wag       21.0   6 160.0 110 3.90 2.875 17.02  0  1    4    4
## Datsun 710          22.8   4 108.0  93 3.85 2.320 18.61  1  1    4    1
## Hornet 4 Drive      21.4   6 258.0 110 3.08 3.215 19.44  1  0    3    1
## Hornet Sportabout   18.7   8 360.0 175 3.15 3.440 17.02  0  0    3    2
## Valiant             18.1   6 225.0 105 2.76 3.460 20.22  1  0    3    1
## Duster 360          14.3   8 360.0 245 3.21 3.570 15.84  0  0    3    4
## Merc 240D           24.4   4 146.7  62 3.69 3.190 20.00  1  0    4    2
## Merc 230            22.8   4 140.8  95 3.92 3.150 22.90  1  0    4    2
## Merc 280            19.2   6 167.6 123 3.92 3.440 18.30  1  0    4    4
## Merc 280C           17.8   6 167.6 123 3.92 3.440 18.90  1  0    4    4
## Merc 450SE          16.4   8 275.8 180 3.07 4.070 17.40  0  0    3    3
## Merc 450SL          17.3   8 275.8 180 3.07 3.730 17.60  0  0    3    3
## Merc 450SLC         15.2   8 275.8 180 3.07 3.780 18.00  0  0    3    3
## Cadillac Fleetwood  10.4   8 472.0 205 2.93 5.250 17.98  0  0    3    4
## Lincoln Continental 10.4   8 460.0 215 3.00 5.424 17.82  0  0    3    4
## Chrysler Imperial   14.7   8 440.0 230 3.23 5.345 17.42  0  0    3    4
## Fiat 128            32.4   4  78.7  66 4.08 2.200 19.47  1  1    4    1
## Honda Civic         30.4   4  75.7  52 4.93 1.615 18.52  1  1    4    2
## Toyota Corolla      33.9   4  71.1  65 4.22 1.835 19.90  1  1    4    1
## Toyota Corona       21.5   4 120.1  97 3.70 2.465 20.01  1  0    3    1
## Dodge Challenger    15.5   8 318.0 150 2.76 3.520 16.87  0  0    3    2
## AMC Javelin         15.2   8 304.0 150 3.15 3.435 17.30  0  0    3    2
## Camaro Z28          13.3   8 350.0 245 3.73 3.840 15.41  0  0    3    4
## Pontiac Firebird    19.2   8 400.0 175 3.08 3.845 17.05  0  0    3    2
## Fiat X1-9           27.3   4  79.0  66 4.08 1.935 18.90  1  1    4    1
## Porsche 914-2       26.0   4 120.3  91 4.43 2.140 16.70  0  1    5    2
## Lotus Europa        30.4   4  95.1 113 3.77 1.513 16.90  1  1    5    2
## Ford Pantera L      15.8   8 351.0 264 4.22 3.170 14.50  0  1    5    4
## Ferrari Dino        19.7   6 145.0 175 3.62 2.770 15.50  0  1    5    6
## Maserati Bora       15.0   8 301.0 335 3.54 3.570 14.60  0  1    5    8
## Volvo 142E          21.4   4 121.0 109 4.11 2.780 18.60  1  1    4    2
```

```r
biler  <- arrange(mtcars, cyl, disp)
biler2 <- arrange(mtcars, desc(disp))
```















