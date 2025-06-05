# GraphQL eksempler mod Datafordeleren

Denne guide viser en række praktiske eksempler på, hvordan man bygger GraphQL forespørgsler mod datafordeleren.
Det antages i denne guide, at du har din egen API-key til Datafordeleren. Denne skal indsættes i alle eksemmpler, hvor der er anført **API-KEY**

**Forfatter: Morten Fuglsang, Septima** 

## Hentning af schemaer

Schemaer kan kaldes med en GET kommando, og returnerer en graphql schema-fil hvis den kaldes i en browser. Denne kan med fordel indlæses i diverse editors.

### DAR
https://graphql.datafordeler.dk/DAR/v1/schema?apiKey=**API-KEY**

### BBR
https://graphql.datafordeler.dk/BBR/v1/schema?apiKey=**API-KEY**

### DAGI
https://graphql.datafordeler.dk/DAGI/v1/schema?apiKey=**API-KEY**

### Stednavne
https://graphql.datafordeler.dk/DS/v1/schema?apiKey=**API-KEY**

### CVR
https://graphql.datafordeler.dk/CVR/v1/schema?apiKey=**API-KEY**

### Ejendomsbeliggenhedsregistret
https://graphql.datafordeler.dk/EBR/v1/schema?apiKey=**API-KEY**

### Ejerfortegnelsen
https://graphql.datafordeler.dk/EJF/v1/schema?apiKey=**API-KEY**

### GeoDanmark Vektor
https://graphql.datafordeler.dk/GEODKV/v1/schema?apiKey=**API-KEY**

### Matriklen2
https://graphql.datafordeler.dk/MAT/v1/schema?apiKey=**API-KEY**


## Basis forespørgsler

### DAR

Endpoint : https://graphql.datafordeler.dk/DAR/v1?apiKey=**API-KEY**
```graphql
query {
  DAR_Adresse(
    registreringstid: "2025-06-04T00:00:00Z"
    virkningstid:  "2025-06-04T00:00:00Z"
    first: 10
  ) {
    nodes {
      adressebetegnelse
      id_lokalId
      husnummer
      doerbetegnelse
      etagebetegnelse
      status
      registreringFra
      virkningFra
    }
  }
}
```

```graphql
query {
  DAR_Husnummer(
    first: 10,
     registreringstid: "2025-06-04T00:00:00Z"
    virkningstid:  "2025-06-04T00:00:00Z") 
    {
    nodes {
      adgangsadressebetegnelse
      husnummertekst
      id_lokalId
      postnummer
      status
      registreringFra
      virkningFra
    }
  }
}
```

```graphql
query {
  DAR_Adressepunkt(
    first: 10, 
    registreringstid: "2025-06-04T00:00:00Z"
    virkningstid:  "2025-06-04T00:00:00Z")
     {
    nodes {
      id_lokalId
      status
      oprindelse_kilde
      oprindelse_noejagtighedsklasse
      oprindelse_tekniskStandard
      position {
        wkt
        crs
        type
        dimension
      }
      registreringFra
      virkningFra
    }
  }
}
```
```graphql
query {
  DAR_NavngivenVej(first: 10, virkningstid: "2025-06-04T00:00:00Z") {
    nodes {
      id_lokalId
      vejnavn
      vejadresseringsnavn
      udtaltVejnavn
      administreresAfKommune
      status
      registreringFra
      virkningFra
    }
  }
}
```

### Stednavne

Endpoint https://graphql.datafordeler.dk/DS/v1?apiKey=**API-KEY**

```graphql
query {
  DS_Bebyggelse(
    first: 10
    registreringstid: "2025-06-04T00:00:00Z"
    virkningstid:  "2025-06-04T00:00:00Z"
  ) {
    nodes {
      id_lokalId
      bebyggelseskode
      bebyggelsestype
      areal
      indbyggertal
      geometri {
        wkt
        crs
      }
    }
  }
}
```
### Matriklen

Endpoint : https://graphql.datafordeler.dk/MAT/v1?apiKey=**API-KEY**

```graphql
query {
  MAT_Jordstykke(
    first: 10
    registreringstid: "2025-06-04T00:00:00Z"
    virkningstid:  "2025-06-04T00:00:00Z"
  ) {
    nodes {
      id_lokalId
      matrikelnummer
      delnummer
      ejerlavLokalId
      kommuneLokalId
      regionLokalId
      sognLokalId
      status
      registreretAreal
      arealberegningsmetode
      arealbetegnelse
      arealtype
      brugsretsareal
      faelleslod
      fredskov_areal
      fredskov_omfang
      jordrente_omfang
      klitfredning_areal
      klitfredning_omfang
      majoratsskov_nummer
      majoratsskov_omfang
      strandbeskyttelse_areal
      strandbeskyttelse_omfang
      vejareal
      vejarealberegningsstatus
      vandarealinkludering
      samletFastEjendomLokalId
      senesteSagLokalId
      skelforretningssagsLokalId
      supplerendeMaalingSagLokalId
    }
  }
}
```

```graphql
query {
  MAT_SamletFastEjendom(
    first: 10
    registreringstid: "2025-06-04T00:00:00Z"
    virkningstid:  "2025-06-04T00:00:00Z"
  ) {
    nodes {
      id_lokalId
      id_namespace
      objectid
      status
      erFaelleslod
      arbejderbolig
      landbrugsnotering
      udskiltVej
      hovedejendomOpdeltIEjerlejligh
      BFEnummer
      paataenktHandling
      StedbestemmelsesReference
      geometri {
        wkt
        crs
      }
    }
  }
}
```

### CVR

Endpoint : https://graphql.datafordeler.dk/CVR/v1?apiKey=**API-KEY**

Der er

```graphql
query {
  CVR_Virksomhed(
    first: 10
    virkningstid: "2025-06-04T00:00:00Z"
  ) {
    nodes {
      CVRNummer
      virksomhedStartdato
      status
      virkningFra
      virkningTil
      registreringFra
      registreringTil
    }
  }
}

```

### BBR

```graphql
query {
  BBR_Bygning(first: 10
                 registreringstid: "2025-06-04T00:00:00Z"
    virkningstid:  "2025-06-04T00:00:00Z") {
    nodes {
      byg007Bygningsnummer
      byg021BygningensAnvendelse
      byg024AntalLejlighederMedKoekken
      byg025AntalLejlighederUdenKoekken
      byg026Opfoerelsesaar
      byg027OmTilbygningsaar
      byg029DatoForMidlertidigOpfoertBygning
      byg030Vandforsyning
      byg031Afloebsforhold
      byg032YdervaeggensMateriale
      byg033Tagdaekningsmateriale
      byg034SupplerendeYdervaeggensMateriale
      byg035SupplerendeTagdaekningsMateriale
      byg036AsbestholdigtMateriale
      byg037KildeTilBygningensMaterialer
      byg038SamletBygningsareal
      byg039BygningensSamledeBoligAreal
      byg040BygningensSamledeErhvervsAreal
      byg041BebyggetAreal
      byg042ArealIndbyggetGarage
      byg043ArealIndbyggetCarport
      byg044ArealIndbyggetUdhus
      byg045ArealIndbyggetUdestueEllerLign
      byg046SamletArealAfLukkedeOverdaekningerPaaBygningen
      byg047ArealAfAffaldsrumITerraenniveau
      byg048AndetAreal
      byg049ArealAfOverdaekketAreal
      byg050ArealAabneOverdaekningerPaaBygningenSamlet
      byg051Adgangsareal
      byg052BeregningsprincipCarportAreal
      byg053BygningsarealerKilde
      byg054AntalEtager
      byg055AfvigendeEtager
      byg056Varmeinstallation
      byg057Opvarmningsmiddel
      byg058SupplerendeVarme
      byg069Sikringsrumpladser
      byg070Fredning
      byg071BevaringsvaerdighedReference
      byg094Revisionsdato
      byg111StormraadetsOversvoemmelsesSelvrisiko
      byg112DatoForRegistreringFraStormraadet
      byg113Byggeskadeforsikringsselskab
      byg114DatoForByggeskadeforsikring
      byg119Udledningstilladelse
      byg121OmfattetAfByggeskadeforsikring
      byg122Gyldighedsdato
      byg123MedlemskabAfSpildevandsforsyning
      byg124PaabudVedrSpildevandsafledning
      byg125FristVedrSpildevandsafledning
      byg126TilladelseTilUdtraeden
      byg127DatoForTilladelseTilUdtraeden
      byg128TilladelseTilAlternativBortskaffelseEllerAfledning
      byg129DatoForTilladelseTilAlternativBortskaffelseEllerAfledning
      byg130ArealAfUdvendigEfterisolering
      byg131DispensationFritagelseIftKollektivVarmeforsyning
      byg132DatoForDispensationFritagelseIftKollektivVarmeforsyning
      byg133KildeTilKoordinatsaet
      byg134KvalitetAfKoordinatsaet
      byg135SupplerendeOplysningOmKoordinatsaet
      byg136PlaceringPaaSoeterritorie
      byg137BanedanmarkBygvaerksnummer
      byg140ServitutForUdlejningsEjendomDato
      byg150Gulvbelaegning
      byg151Frihoejde
      byg152AabenLukketKonstruktion
      byg153Konstruktionsforhold
      byg301TypeAfFlytning
      byg302Tilflytterkommune
      byg403OevrigeBemaerkningerFraStormraadet
      byg406Koordinatsystem
      byg500Notatlinjer
      datafordelerOpdateringstid
      ejerlejlighed
      forretningshaendelse
      forretningsomraade
      forretningsproces
      grund
      husnummer
      id_lokalId
      id_namespace
      jordstykke
      kommunekode
      registreringFra
      registreringsaktoer
      registreringTil
      status
      virkningFra
      virkningsaktoer
      virkningTil
    }
  }
}
```

## Forespørgsler med filtre

Vi skal når der filtreres huske at undersøge hvilke atributter der er muligt at filtrere på - Eksemplet her er fra Navngivenvej i schemaet:
```graphql
input DAR_NavngivenVejFilterInput {
  and: [DAR_NavngivenVejFilterInput!]
  datafordelerOpdateringstid: DafDateTimeOperationFilterInput
  id_lokalId: DafStringOperationFilterInput
  registreringFra: DafDateTimeOperationFilterInput
  registreringTil: DafDateTimeOperationFilterInput
  status: DafStringOperationFilterInput
  vejnavn: DafSearchableStringOperationFilterInput
  vejnavnebeliggenhed_vejnavnelinje: SpatialFilterInput
  vejnavnebeliggenhed_vejnavneomraade: SpatialFilterInput
  vejnavnebeliggenhed_vejtilslutningspunkter: SpatialFilterInput
  virkningFra: DafDateTimeOperationFilterInput
  virkningTil: DafDateTimeOperationFilterInput
}
```
Med den viden, kan vi bygge en filtreret queries:

```graphql
query {
  DAR_NavngivenVej(
    where: {
      vejnavn: { eq: "Lundagervej" }
    }
    first: 10
    registreringstid: "2025-06-04T00:00:00Z"
    virkningstid:  "2025-06-04T00:00:00Z"
  ) {
    nodes {
      id_lokalId
      vejnavn
      administreresAfKommune
    }
  }
}
```

```graphql
query {
  DAR_Adresse(
    where: {
      adressebetegnelse: { eq: "Lundagervej 43, 2740 Skovlunde" }
    }
    first: 1
    registreringstid: "2025-06-04T00:00:00Z"
    virkningstid:  "2025-06-04T00:00:00Z"
  ) {
    nodes {
      adressebetegnelse
      husnummer
      doerbetegnelse
      etagebetegnelse
      status
    }
  }
}
```

Endpoint https://graphql.datafordeler.dk/MAT/v1?apiKey=**API-KEY**

```graphql
query {
  MAT_Jordstykke(
    where: {
      matrikelnummer: { eq: "6hh" }
      ejerlavLokalId: { eq: "21751" }
    }
    first: 1
    registreringstid: "2025-06-04T00:00:00Z"
    virkningstid:  "2025-06-04T00:00:00Z"
  ) {
    nodes {
      id_lokalId
      matrikelnummer
      ejerlavLokalId
      registreretAreal
      status
    }
  }
}
```

```graphql
query {
  MAT_SamletFastEjendom(
    where: {
      BFEnummer: { eq: 2154708 }
    }
    first: 1
    registreringstid: "2025-06-04T00:00:00Z"
    virkningstid:  "2025-06-04T00:00:00Z"
  ) {
    nodes {
      id_lokalId
      BFEnummer
      status
      arbejderbolig
      erFaelleslod
      landbrugsnotering
      udskiltVej
      hovedejendomOpdeltIEjerlejligh
      geometri {
        wkt
        crs
      }
    }
  }
}
```

### IN Filter
GraphQL inderstøtter 'IN LIST' filtre - her er eksemplet fra stednavne :

Endpoint https://graphql.datafordeler.dk/DS/v1?apiKey=**API-KEY**

```graphql
query {
  DS_Bebyggelse(
    where: {
      bebyggelsestype: { in: ["by", "bydel"] }
    }
    first: 10
    registreringstid: "2025-06-04T00:00:00Z"
    virkningstid:  "2025-06-04T00:00:00Z"
  ) {
    nodes {
      id_lokalId
      bebyggelsestype
      bebyggelseskode
      indbyggertal
      geometri {
        wkt
        crs
      }
    }
  }
}
```

## Forespørgsler med paging
Paging gør det muligt at hente resultater i chunks - så man ikke skal vente på meget store forespørgsler returnerer alt på en gang.

```graphql
query {
  DAR_Adresse(
    first: 10
    registreringstid: "2025-06-04T00:00:00Z"
    virkningstid:  "2025-06-04T00:00:00Z"
  ) {
    pageInfo {
      endCursor
      hasNextPage
    }
    nodes {
      id_lokalId
      adressebetegnelse
    }
  }
}
```
Dette svar giver en cursor tilbage, der gør at vi kan hente de næste 10 ved at bruge denne cursor.

```json
      "pageInfo": {
        "endCursor": "TURBd01EWTNNR010TkdZNE9TMDBZekEzTFdJM04ySXRaamxpT0RKaFpqQXhZemd3O01qQXlNQzB3TnkweE4xUXhNRG94T1RveE1pNDJOakV6TmpFd0t6QXdPakF3O01qQXlNQzB3TnkweE4xUXhNRG94T1RveE1pNDJOakV6TmpFd0t6QXdPakF3",
        "hasNextPage": true
      },
```

```graphql
query {
  DAR_Adresse(
    first: 10
    after: "TURBd01EWTNNR010TkdZNE9TMDBZekEzTFdJM04ySXRaamxpT0RKaFpqQXhZemd3O01qQXlNQzB3TnkweE4xUXhNRG94T1RveE1pNDJOakV6TmpFd0t6QXdPakF3O01qQXlNQzB3TnkweE4xUXhNRG94T1RveE1pNDJOakV6TmpFd0t6QXdPakF3",
    registreringstid: "2025-06-04T00:00:00Z"
    virkningstid:  "2025-06-04T00:00:00Z"
  ) {
    pageInfo {
      endCursor
      hasNextPage
    }
    nodes {
      id_lokalId
      adressebetegnelse
    }
  }
}
```
Dette kan foresættes indtil 
```json 
"hasNextPage": true
```
 bliver false - så har vi hentet alle adresser for det givne tidspunt - 10 ad gangen...




## Spatiale forespørgsler

For at kunne lave en spatial forespørgsel, skal data indeholde geometri. Derefter skal det undersøges, hvilke spatiale operatorer der er tilrådighed fra scehamet.

```graphql
query {
  DAR_Adressepunkt(
    where: {
      position: {
        within: {
          wkt: "POLYGON((712994.1671187154 6179882.641955254,713134.5416895023 6179882.641955254,713134.5416895023 6179992.936260872,712994.1671187154 6179992.936260872,712994.1671187154 6179882.641955254))"
          crs: 25832
        }
      }
    }
    first: 100
    registreringstid: "2025-06-04T00:00:00Z"
    virkningstid:  "2025-06-04T00:00:00Z"
  ) {
    nodes {
      id_lokalId
      status
      position {
        wkt
      }
    }
  }
}
```

### Matriklen

Samlet fast ejendom ud fra BFE-nummer indenfor en søge-geometri

Endpoint https://graphql.datafordeler.dk/MAT/v1?apiKey=**API-KEY**

```graphql
query {
  MAT_SamletFastEjendom(
    where: {
      geometri: {
        within: {
          wkt: "POLYGON((712994.1671187154 6179882.641955254,713134.5416895023 6179882.641955254,713134.5416895023 6179992.936260872,712994.1671187154 6179992.936260872,712994.1671187154 6179882.641955254))"
          crs: 25832
        }
      }
    }
    first: 100
    registreringstid: "2025-06-04T00:00:00Z"
    virkningstid:  "2025-06-04T00:00:00Z"
  ) {
    nodes {
      BFEnummer
      id_lokalId
      geometri {
        wkt
      }
    }
  }
}
```

## Nestede Forespørgsler

### Stednavne
Hvis vi i stednavne vil have både navn og geometri, så skal vi kombinere to forespørgsler:
Først henter vi de stednavne vi gerne vil finde. Når vi har fundet dem, så taget vi navngivetSted_objectid ud

```graphql
query {
  DS_Stednavn(
    where: {
      skrivemaade: { eq: "Skovlunde" }
    }
    first: 10
  ) {
    nodes {
      skrivemaade
      navngivetSted_objectid
    }
  }
}
```
Herefter kan vi så sprørge efter geometrien dertil ud fra navngivetSted_objectid:

```graphql
query {
  DS_Bebyggelse(
    where: {
      objectid: { eq: "476543" }
    }
    virkningstid: "2025-06-05T10:00:00.000000Z"
  ) {
    nodes {
      id_lokalId
      objectid
      bebyggelsestype
      indbyggertal
      geometri {
        wkt
      }
    }
  }
}
```

### Fra matriklen til bygninger
Vi skal finde først finde en matrikle, og derfra bruge svaret til at finde bygninger på matriklen fra BBR.

```graphql
query {
  MAT_Jordstykke(
    where: {
      matrikelnummer: { eq: "6hh" }
      ejerlavLokalId: { eq: "21751" }
    }
    first: 1
    registreringstid: "2025-06-04T00:00:00Z"
    virkningstid:  "2025-06-04T00:00:00Z"
  ) {
    nodes {
      id_lokalId
      matrikelnummer
      ejerlavLokalId
      registreretAreal
      status
    }
  }
}
```

Herefter skal vi søge i BBR. vi harfået id fra jordstykket som er 1538971.

```graphql
query {
  BBR_Bygning(
        first: 10
        registreringstid: "2025-06-04T00:00:00Z"
        virkningstid:  "2025-06-04T00:00:00Z",
         where: { jordstykke: { eq: "1538971" } }) {
    nodes {
      id_lokalId
      jordstykke
      byg007Bygningsnummer
      byg021BygningensAnvendelse
      byg026Opfoerelsesaar
      byg038SamletBygningsareal
      status
    }
  }
}
```

## Forespørgsler med relationer

## Forespørgsler på tværs af registre