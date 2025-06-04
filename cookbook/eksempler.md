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

## Forespørgsler med filtre

## Forespørgsler med paging

## Spatiale forespørgsler

## Bitemporale forespørgsler

## Forespørgsler på tværs af registre