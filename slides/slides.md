---
marp: true
style: |
  .columns {
    display: grid;
    grid-template-columns: repeat(2, minmax(0, 1fr));
    gap: 1rem;

  }

  .center_img {
  display: block;
  margin-left: auto;
  margin-right: auto;
  width: 50%;
  }

  .center {
    text-align: center;
  }

  .topbar {
  padding-top: 125px;
  }

    .top50 {
  padding-top: 50px;
  }

      .top100 {
  padding-top: 100px;
  }

        .top150 {
  padding-top: 150px;
  }

  .container {
    display: flex;
  }
  .col {
    flex:1;
    padding-right: 20px;
    padding-left: 20px;

  }

    section {
    color: white;
  }

paginate: true


        
---

![bg](./img/geoforum_green.png)


<h1 style="font-size:80px;color:white">GraphQL på Datafordelren</h1>

<h1 style="font-size:30px;color:white">Martin  Jensen, Klimadatastyrelsen</h1>
<h1 style="font-size:30px;color:white">Margit Kildevang, Klimadatastyrelsen</h1>
<h1 style="font-size:30px;color:white">Morten Fuglsang, Septima</h1>


<style>
section::after {
  content: attr(data-marpit-pagination) '/' attr(data-marpit-pagination-total);
}
</style>

---

![bg](./img/first.png)

<h1 style="font-size:50px;color:white">Hvorfor dette seminar ?</h1>
<br>

<img src="./img/figur1.png" width="1050">


---

![bg](./img/first.png)


<h1 style="font-size:50px;color:white">Dagens program</h1>

<h2> 13.00 - 13.30 : Introduktion og status Martin Jensen KDS</h2>

<h2> 13.30 - 13.50 : Introduktion til GraphQL</h2>

<h2> 13.50 - 14.00 : Pause</h2>

<h2> 14.00 - 14.45 : Hands on eksempler på GraphQL forespørgsler </h2>

<h2> 14.45 - 15.00 : Spørgsmål, opsamling og afslutning</h2>


---

![bg](./img/green.png)

<h1 style="font-size:50px;color:white">Introduktion og status</h1>

---

![bg](./img/green.png)

<h1 style="font-size:50px;color:white">Introduktion til GraphQL</h1>

---
![bg](./img/first.png)

<h1 style="font-size:50px;color:white">Hvorfor GraphQL</h1>

<h2>Før GraphQL: REST dominerede API-verdenen</h2>

Udfordringer med REST:

* Overfetching og underfetching af data

* Mange API-kald for at hente relaterede data

* Vanskeligt at tilpasse data til forskellige klienter (web, mobil)

---

![bg](./img/first.png)

<h1 style="font-size:50px;color:white">Fødselen af GraphQL hos Facebook</h1>

* Skabt af Facebook i 2012 som intern løsning

* Bruges først i deres mobile app til nyhedsfeedet

* Offentliggjort som open source i 2015

* Formål: Effektiv og fleksibel datatilgængelighed med ét enkelt kald



---

![bg](./img/first.png)

<h1 style="font-size:50px;color:white">GraphQL i dag</h1>

* Bred adoption hos store virksomheder (GitHub, Shopify, Twitter)

* Bruges til både frontend og backend integrationer

* Aktivt community og voksende økosystem (Apollo, Relay, Hasura osv.)

Et paradigmeskifte: fra ressourcer til grafer


---

![bg](./img/first.png)

<h1 style="font-size:50px;color:white">Hvad er en graf ?</h1>

En **graf** består af:

- **Noder (nodes):** Objekter som fx `Bruger`, `Adresse`, `Postnummer`
- **Kanter (edges):** Relationer mellem objekterne (fx en bruger har en adresse)
- **Felter (fields):** Informationer på hver node

En GraphQL-query følger forbindelserne i grafen  
Du spørger på tværs af datarelationer – fleksibelt

---

![bg](./img/first.png)

<h1 style="font-size:50px;color:white">Hvad er en query ?</h1>

<div class="container">
<div class="col">

**Query**: Nøgleordet for en læseoperation

Vi beder om et user-objekt med id = "123"

Vi specificerer præcis hvilke felter vi vil have: id, name, email

</div>

<div class="col">

```graphql
query {
  user(id: "123") {
    id
    name
    email
  }
}
```
</div>
</div>

---

![bg](./img/first.png)

<h1 style="font-size:50px;color:white">Hvordan sender man en forespørgsel ?</h1>

GraphQL bruger som regel HTTP POST (kan også være GET)
Body’en indeholder query som tekst (JSON)

```http
POST /graphql
Content-Type: application/json

{
  "query": "query { user(id: \"123\") { name email } }"
}
```

---

![bg](./img/first.png)

<h1 style="font-size:50px;color:white">Applikationer</h1>

Man kan i praksis godt bygge en **GET** url, men der skal url-encodes en masse ting.

I praksis vil man ofte benytte en applikation der kan lave **POST** forespørgsler:

* Postman / Insomnia / Altair

* Apollo Client

* curl

* GraphQL Playground / GraphiQL

---

![bg](./img/first.png)

<h1 style="font-size:50px;color:white">Response</h1>

<div class="container">
<div class="col">

Response indeholder kun de felter der blev forespurgt

Ingen overflødig data – ingen status, links, osv.

Altid under data-nøglen (fejl kommer i errors)

</div>

<div class="col">

```json
{
  "data": {
    "user": {
      "name": "Alice",
      "email": "alice@example.com"
    }
  }
}

```
</div>
</div>

---

![bg](./img/first.png)

<h1 style="font-size:50px;color:white">Mutations ?</h1>

<div class="container">
<div class="col">

Bruges til at ændre data (oprette, opdatere, slette)

Ligner en query, men påvirker serverens tilstand

Serveren returnerer typisk det opdaterede objekt

</div>


<div class="col">

```graphql
mutation {
  updateUser(id: "123", input: {
    email: "new@example.com"
  }) {
    id
    name
    email
  }
}
```

</div>
</div>

---

![bg](./img/first.png)

<img src="./img/figur2.png" width="800">

---

![bg](./img/first.png)

<h1 style="font-size:50px;color:white">GraphQL schemas</h1>

Et **GraphQL-schema** definerer de typer og felter, du kan spørge om i API’et.

Schemaet fungerer som:
- En **kontrakt** mellem klient og server
- En **struktur** over alle data og deres relationer
- En **validering** af dine queries

Et schema består af:
- `type` – definerer objekttyper (f.eks. `Adresse`, `Vejstykke`)
- `Query` – definerer tilgængelige læseoperationer
- `Mutation` – definerer skrive-/opdateringsoperationer (hvis tilladt)

Det hele er stærkt typet og dokumenteret...


---

![bg](./img/first.png)



<div class="container">
<div class="col">

**type Adresse** = en type med felterne id og adgangsadresse

'**ID!** = et obligatorisk ID-felt (udråbstegn = required)

**adresse(id: ID!)**: Adresse = query, der returnerer én adresse

**adgangsadresser(...)** = query, der returnerer en liste [...]



</div>


<div class="col">
</br>

```graphql
type Adresse {
  id: ID!
  adgangsadresse: Adgangsadresse
}

type Adgangsadresse {
  vejstykke: Vejstykke
  husnr: String
  postnummer: Postnummer
}

type Query {
  adresse(id: ID!): Adresse
  adgangsadresser(postnr: String): [Adgangsadresse]
}
```

</div>
</div>



---

![bg](./img/first.png)

<h1 style="font-size:40px;color:white">Eksempel fra Datafordeleren</h1>

```graphql
query {
    BBR_Bygning( # Entitet
        where: { # Standardfiltre
            kommunekode: { eq: "0550" }
            virkningsaktoer: { startsWith: "Konvertering" }
        }
        virkningstid: "2024-11-12T14:41:33Z" # Bitemporalt filter
        first: 10 # Paging
        ) {
        pageInfo { # Paginginformation
            startCursor
            endCursor
            hasNextPage
            hasPreviousPage
        }
        nodes {
            id_lokalId
            kommunekode
            virkningsaktoer
        }
    }
}
```

---

![bg](./img/first.png)
</br>
- **Entitet**: Hver query skal specificere hvilken entitet den omhandler. Eksemplet
ovenfor viser en query for entiteten BBR_Bygning fra BBR og specifikt kun for 3 kolonner (angivet i ”nodes”): id_lokalId, kommunekode og virkningsaktoer.
- **Endepunkt**: En query sendes til et GraphQL-endepunkt som et GET- eller POSTrequest.
- **Standardfiltre**: En query kan indeholde standardfiltre der filtrerer data. Query’en
ovenfor indeholder to standardfiltre: et lighedsfilter (eq: "0550") på kommunekode-kolonnen og et tekstfilter (startsWith: "Konvertering") på virkningsaktoer-kolonnen. 
---

![bg](./img/first.png)
</br>

- **Geometriske filtre**: En query kan indeholde geometriske filtre, der filtrerer
data på baggrund af geometri.
- **Bitemporale filtre**: En query kan også indeholde bitemporale filtre. Query’en oven
for indeholder et bitemporalt point-in-time-filter (virkningstid: "2024-11-12T14:41:33Z") på virkningstidspunktet.
- **Paging** : En query kan også definere hvordan data pagineres. Query’en oven for returnerer de første 10 rækker (first: 10) givet filtreringen samt paginginformation for resultatsættet (angivet i ”pageInfo”). 


---
![bg](./img/first.png)

<h1 style="font-size:60px;color:white">Hvordan får man adgang ? </h1>

---

![bg](./img/first.png)



<div class="container">
<div class="col">


</br>
</br>

* Det er nødvendigt at etablere en ny type adgang - det er ikke længere nok med en webbruger og en tjenestebruger.

* Hvis man skal have adgang til frie data, er en token nok, men den skal oprettes

</div>

<div class="col">
</br>
</br>
<img src="./img/bruger1.png" width="500">

</div>
</div>

---
![bg](./img/first.png)

<img src="./img/bruger2.png" width="800">

---

![bg](./img/first.png)

<img src="./img/bruger3.png" width="1000">

---

![bg](./img/first.png)

<img src="./img/bruger4.png" width="800">

---

![bg](./img/first.png)

<img src="./img/bruger6.png" width="500">

---

![bg](./img/first.png)

<img src="./img/bruger5.png" width="1000">

---
![bg](./img/first.png)
<h1 style="font-size:60px;color:white">Bevæbnet med en token, er vi nu klar til at begynde at spørge...</h1>

---
![bg](./img/first.png)
<h1 style="font-size:60px;color:white">Lad os hente nogle schemaer </h1>

graphql.datafordeler.dk/DAR/v1/schema?apiKey=API-KEY

graphql.datafordeler.dk/BBR/v1/schema?apiKey=API-KEY

graphql.datafordeler.dk/MAT/v1/schema?apiKey=API-KEY


</br>
</br>
Vi starter med at kigge nærmere på DAR schemaet...


---
![bg](./img/first.png)



<div class="container">
<div class="col">



<h1 style="font-size:50px;color:white">Elementer</h1>


* <h3 style="font-size:40px;color:white">Metadata</h3>


* <h3 style="font-size:40px;color:white">Query types</h3>


* <h3 style="font-size:40px;color:white">Filter inputs</h3>


* <h3 style="font-size:40px;color:white">Spatiale filtre</h3>

</div>

<div class="col">
</br>
</br>
<img src="./img/schema.png" width="450">

</div>
</div>

---

![bg](./img/first.png)

<h1 style="font-size:60px;color:white">Query types</h1>

<img src="./img/query_types.png" width="1200">

---

![bg](./img/first.png)

<h1 style="font-size:60px;color:white">Filter inputs</h1>

<img src="./img/filterinputs.png" width="700">

---

![bg](./img/first.png)

<h1 style="font-size:60px;color:white">Spatial Filters</h1>

<img src="./img/spfilters.png" width="900">

---

![bg](./img/first.png)

<h1 style="font-size:60px;color:white">Metadata</h1>

<img src="./img/metadata.png" width="800">

---
![bg](./img/first.png)

<h1 style="font-size:40px;color:white">Hvordan sender man en POST forespørgsel ?</h1>

Det er kompliceret at lave en GET - det er bedre at sende et POST request afsted.

* Dette kan gøres med en række forskellige stykker software - Jeg bruger Postman til mine eksempler:

www.postman.com/

---
![bg](./img/first.png)

<h1 style="font-size:40px;color:white">Basis request</h1>

Endpoint : graphql.datafordeler.dk/DAR/v1?apiKey=**API-KEY**

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

---
![bg](./img/first.png)

<img src="./img/postman1.png" width="850">

---
![bg](./img/first.png)

<img src="./img/postman2.png" width="850">

---
![bg](./img/first.png)

<img src="./img/postman3.png" width="850">

---



![bg](./img/green.png)

<h1 style="font-size:50px;color:white">Hands on eksempler på GraphQL forespørgsler </h1>

---

![bg](./img/first.png)

<h1 style="font-size:50px;color:white">Alle ressourcer vi har brugt i dag, kan hentes på Github </h1>

</br>

Github.com/MFuglsang/graphql_datafordeleren

</br>

Her er bla. en pdf med en lang række eksempler - langt flere end vi har vist her...

---

![bg](./img/green.png)

<h1 style="font-size:50px;color:white">Spørgsmål, opsamling og afslutning</h1>


---



![bg](./img/geoforum_green.png)


<h1 style="font-size:80px;color:white">Tak for i dag...</h1>