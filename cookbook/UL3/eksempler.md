# GraphQL eksempler mod Datafordeleren

Denne guide viser en række praktiske eksempler på, hvordan man bygger GraphQL forespørgsler mod datafordelerens flexible opslagslogik.
Det antages i denne guide, at du har din egen API-key til Datafordeleren. Denne skal indsættes i alle eksemmpler, hvor der er anført **API-KEY**
Hvis du ikke allerede er bekendt med GraphQL på Datafordeleren, så se eksemplerne under UL2 først.

Denne guide tager udgangspunkt i udviklingsmiljøet test06-graphql.datafordeler.dk 

**Forfatter: Morten Fuglsang, Septima** 

# Indhold
1. [Hentning af schema](#Hentning)
2. [Basis forespørgsler internt i register](#basis)
   1. [Simpelt opslag i DAR](#simple1)
   2. [Adresse med NavngivenVej](#simple2)
   3. [Opslag fra Adresse via Husnummer til Adgangspunkt med geometri](#simple3)

3. [Forespørgsler mellem relationer](#relationer)

   1. [Adresse med Jordstykke og geometri](#simple4)
   2. [Jordstykker for BFE-nummer](#simple5)
   3. [BFE-nummer til BBR-grund(e)](#simple6)
   4. [Geodanmark bygning med BBR enhed(er)](#simple7)

4. [DAWA udtræk ?](#DAWA)

   1. [DAWA adresse enkeltopslag](#DAWA1)
   2. [DAWA husnummer enkeltopslag](#DAWA2)
   3. [DAWA husnummer masseopslag](#DAWA3)

5. [Super-queries ](#SUPER)
   1. [Opslag ud fra adresse](#SUPER_ADRESSE)








## Hentning af schema <a name="hentning"></a>

Schemaer kan kaldes med en GET kommando, og returnerer en graphql schema-fil hvis den kaldes i en browser. Denne kan med fordel indlæses i diverse editors.
Dette schema er tungt - der er da dette blev skrevet ca. 72.000 linjer i det, så det er ikke let at læse.

### FLEXIBLE
https://test06-graphql.datafordeler.dk/flexible/v1/schema?apiKey=**API-KEY**


## Basis forespørgsler internt i register <a name="basis"></a>

Vi kan i det flexible schema godt lave 'simple' forespørgsler - informationerne fra de simple schemaer ligger også heri

### Simpelt opslag i DAR <a name="simple1"></a>

```graphql
query {
  DAR_Adresse(
    registreringstid: "2025-06-04T00:00:00Z"
    virkningstid:  "2025-06-04T00:00:00Z"
    first: 2
  ) {
    nodes {
      adressebetegnelse
      id_lokalId
      husnummer
      doerbetegnelse
      status
      
    }
  }
}
```

Datafordeleren svarer:

```json
{
    "data": {
        "DAR_Adresse": {
            "nodes": [
                {
                    "adressebetegnelse": "Østre Stationsvej 2F, 6. 211, 5000 Odense C",
                    "id_lokalId": "0000090e-e9f3-4ffe-a0a5-2852666d158c",
                    "husnummer": "405bec91-1609-46de-89e0-94e646a898d2",
                    "doerbetegnelse": "211",
                    "status": "4"
                },
                {
                    "adressebetegnelse": "Vestergade 41A, 2. tv, 4850 Stubbekøbing",
                    "id_lokalId": "000021c5-e9ee-411d-b2d8-ec9161780ccd",
                    "husnummer": "0a3f5086-e9cd-32b8-e044-0003ba298018",
                    "doerbetegnelse": "tv",
                    "status": "3"
                }
            ]
        }
    }
}
```

### Adresse med NavngivenVej <a name="simple2"></a>

```graphql
query {
  DAR_Adresse(
    registreringstid: "2025-06-04T00:00:00Z"
    virkningstid: "2025-06-04T00:00:00Z"
    first: 1
    where: {
      id_lokalId: { eq: "0000090e-e9f3-4ffe-a0a5-2852666d158c" }
    }
  ) {
    nodes {
      adressebetegnelse
      id_lokalId
      husnummer
      doerbetegnelse
      status

      # Adresse → Husnummer
      adresseHarHusnummer {
        id_lokalId
        husnummertekst
        postnummer

        # Husnummer → NavngivenVej
        husnummerHoererTilNavngivenVej {
          id_lokalId
          vejnavn
          udtaltVejnavn


        }
      }
    }
  }
}

```

Datafordeleren svarer:

```json
{
    "data": {
        "DAR_Adresse": {
            "nodes": [
                {
                    "adressebetegnelse": "Østre Stationsvej 2F, 6. 211, 5000 Odense C",
                    "id_lokalId": "0000090e-e9f3-4ffe-a0a5-2852666d158c",
                    "husnummer": "405bec91-1609-46de-89e0-94e646a898d2",
                    "doerbetegnelse": "211",
                    "status": "4",
                    "adresseHarHusnummer": {
                        "id_lokalId": "405bec91-1609-46de-89e0-94e646a898d2",
                        "husnummertekst": "2F",
                        "postnummer": "4a532e97-1491-4041-bf37-7411e3095a3e",
                        "husnummerHoererTilNavngivenVej": {
                            "id_lokalId": "c2669908-55fd-4a16-84b3-484a5a4a03fa",
                            "vejnavn": "Østre Stationsvej",
                            "udtaltVejnavn": "Østre Stationsvej"
                        }
                    }
                }
            ]
        }
    }
}
```

### Opslag fra Adresse via Husnummer til Adgangspunkt med geometri <a name="simple3"></a>

```graphql
# Enter your graphQL query here.
query {
  DAR_Adresse(
    registreringstid: "2025-06-04T00:00:00Z"
    virkningstid: "2025-06-04T00:00:00Z"
    first: 1
    where: {
      adressebetegnelse: { eq: "Lærkevej 5, 2600 Glostrup" }
    }
  ) {
    nodes {
      adressebetegnelse
      id_lokalId
      husnummer
      doerbetegnelse
      status
      # Adresse → Husnummer
      adresseHarHusnummer {
        id_lokalId
        husnummertekst
        postnummer       
        # Husnummer → Adgangspunkt
        HusnummerHarAdgangspunkt {
          id_lokalId
          position {
            wkt
            crs
          }
        }
      }
    }
  }
}
```

Datafordeleren svarer:

```json
{
    "data": {
        "DAR_Adresse": {
            "nodes": [
                {
                    "adressebetegnelse": "Lærkevej 5, 2600 Glostrup",
                    "id_lokalId": "0a3f50a4-bd21-32b8-e044-0003ba298018",
                    "husnummer": "0a3f507c-6352-32b8-e044-0003ba298018",
                    "doerbetegnelse": "",
                    "status": "3",
                    "adresseHarHusnummer": {
                        "id_lokalId": "0a3f507c-6352-32b8-e044-0003ba298018",
                        "husnummertekst": "5",
                        "postnummer": "a26174bd-f602-463c-9a4a-92413ffba23f",
                        "HusnummerHarAdgangspunkt": {
                            "id_lokalId": "0a3f507c-6352-32b8-e044-0003ba298018",
                            "position": {
                                "wkt": "POINT (714148.68 6175195.39)",
                                "crs": 25832
                            }
                        }
                    }
                }
            ]
        }
    }
}
```

## Forespørgsler mellem relationer <a name="relationer"></a>

### Adresse med Jordstykke og geometri <a name="simple4"></a>

```graphql
query {
  DAR_Adresse(
    registreringstid: "2025-06-04T00:00:00Z"
    virkningstid: "2025-06-04T00:00:00Z"
    first: 2
    where: {
      adressebetegnelse: { eq: "Lundagervej 43, 2740 Skovlunde" }
    }
  ) {
    nodes {
      adressebetegnelse
      id_lokalId
      husnummer
      doerbetegnelse
      status

      # Adresse → Husnummer (to-one)
      adresseHarHusnummer {
        id_lokalId
        husnummertekst
        postnummer

        # Husnummer → Jordstykke (to-many)
        husnummerErPlaceretPaaJordstykke(first: 10) {
          nodes {
            matrikelnummer
            ejerlavLokalId

            # Jordstykke → Lodflade (to-many): hent polygon-geometri
            lodfladeRepraesentationJordstykke(first: 10) {
              nodes {
                id_lokalId
                geometri {
                  wkt        # polygon i EPSG:25832
                  crs
                }
              }
            }
          }
        }
      }
    }
  }
}

```

Datafordeleren svarer:

```json
{
    "data": {
        "DAR_Adresse": {
            "nodes": [
                {
                    "adressebetegnelse": "Lundagervej 43, 2740 Skovlunde",
                    "id_lokalId": "0a3f50a3-27d5-32b8-e044-0003ba298018",
                    "husnummer": "0a3f507b-83d6-32b8-e044-0003ba298018",
                    "doerbetegnelse": "",
                    "status": "3",
                    "adresseHarHusnummer": {
                        "id_lokalId": "0a3f507b-83d6-32b8-e044-0003ba298018",
                        "husnummertekst": "43",
                        "postnummer": "73e72b62-616c-4b58-9124-52d00774a336",
                        "husnummerErPlaceretPaaJordstykke": {
                            "nodes": [
                                {
                                    "matrikelnummer": "6hh",
                                    "ejerlavLokalId": "21751",
                                    "lodfladeRepraesentationJordstykke": {
                                        "nodes": [
                                            {
                                                "id_lokalId": "1538971",
                                                "geometri": {
                                                    "wkt": "POLYGON ((713066.259 6179958.381, 713040.778 6179929.5, 713055.872 6179915.949, 713084.212 6179947.816, 713072.556 6179958.52, 713066.259 6179958.381))",
                                                    "crs": 25832
                                                }
                                            }
                                        ]
                                    }
                                }
                            ]
                        }
                    }
                }
            ]
        }
    }
}
```


### Jordstykker for BFE-nummer <a name="simple5"></a>
```graphql
query {
  MAT_SamletFastEjendom(
    registreringstid: "2025-06-04T00:00:00Z"
    virkningstid: "2025-06-04T00:00:00Z"
    first: 10
    where: { BFEnummer: { eq: 2154708 } }
  ) {
    nodes { 
      id_lokalId
      BFEnummer
      landbrugsnotering

      jordstykkeSamlesISamletFastEjendom(first: 500) {
        nodes {
          id_lokalId
          matrikelnummer
          ejerlavLokalId

          jordstykkeLiggerIEjerlav {
            ejerlavskode
            ejerlavsnavn
          }

          lodfladeRepraesentationJordstykke(first: 1) {
            nodes {
              id_lokalId
              geometri { wkt crs type dimension }
            }
          }
        }
      }
    }
  }
}

```

Datafordeleren svarer:

```json
{
    "data": {
        "MAT_SamletFastEjendom": {
            "nodes": [
                {
                    "id_lokalId": "2154708",
                    "BFEnummer": 2154708,
                    "landbrugsnotering": null,
                    "jordstykkeSamlesISamletFastEjendom": {
                        "nodes": [
                            {
                                "id_lokalId": "1538971",
                                "matrikelnummer": "6hh",
                                "ejerlavLokalId": "21751",
                                "jordstykkeLiggerIEjerlav": {
                                    "ejerlavskode": 21751,
                                    "ejerlavsnavn": "Skovlunde By, Skovlunde"
                                },
                                "lodfladeRepraesentationJordstykke": {
                                    "nodes": [
                                        {
                                            "id_lokalId": "1538971",
                                            "geometri": {
                                                "wkt": "POLYGON ((713066.259 6179958.381, 713040.778 6179929.5, 713055.872 6179915.949, 713084.212 6179947.816, 713072.556 6179958.52, 713066.259 6179958.381))",
                                                "crs": 25832,
                                                "type": "Polygon",
                                                "dimension": "XY"
                                            }
                                        }
                                    ]
                                }
                            }
                        ]
                    }
                }
            ]
        }
    }
}
```

### BFE-nummer til BBR-grund(e) <a name="simple6"></a>
```graphql
query {
  BBR_Ejendomsrelation(
    registreringstid: "2025-06-04T00:00:00Z"
    virkningstid: "2025-06-04T00:00:00Z"
    first: 10
    where: { bfeNummer: { eq: 2154708 } }
  ) {
    nodes {
      id_lokalId
      bfeNummer
      ejendomstype

      # → BBR_Grund (to-many)
      grundSamletFastEjendom(first: 500) {
        nodes {
          id_lokalId
          status
          kommunekode
          bestemtFastEjendom   
          gru009Vandforsyning
          gru010Afloebsforhold

        }
      }
    }
  }
}

```
Datafordeleren svarer:

```json
{
    "data": {
        "BBR_Ejendomsrelation": {
            "nodes": [
                {
                    "id_lokalId": "24f5f7d6-e105-4504-811a-910a9a5479d7",
                    "bfeNummer": 2154708,
                    "ejendomstype": "1",
                    "grundSamletFastEjendom": {
                        "nodes": [
                            {
                                "id_lokalId": "4d0cf96a-596f-40e1-b69b-011f9dab3c59",
                                "status": "7",
                                "kommunekode": "0151",
                                "bestemtFastEjendom": "24f5f7d6-e105-4504-811a-910a9a5479d7",
                                "gru009Vandforsyning": "1",
                                "gru010Afloebsforhold": "5"
                            }
                        ]
                    }
                }
            ]
        }
    }
}
```



### Geodanmark bygning med BBR enhed(er) <a name="simple7"></a>
```graphql
query {
  GEODKV_Bygning(
    registreringstid: "2025-06-04T00:00:00Z"
    virkningstid: "2025-06-04T00:00:00Z"
    first: 1
    where: { id_lokalId: { eq: "1069796594" } }
  ) {
    nodes {
      id_lokalId
      bygningstype
      status
      geometri { wkt }

      # GeoDK → BBR_Bygning (to-one)
      referererTil {
        id_lokalId                 
        kommunekode
        byg007Bygningsnummer
        status
        byg021BygningensAnvendelse

        # BBR_Bygning → BBR_Enhed (to-many)
        liggerIBygning(first: 500
        ) {
          nodes {
            id_lokalId
            status
            enh020EnhedensAnvendelse
            adresseIdentificerer  
          }
        }
      }
    }
  }
}
```
Datafordeleren svarer:

```json
{
    "data": {
        "GEODKV_Bygning": {
            "nodes": [
                {
                    "id_lokalId": "1069796594",
                    "bygningstype": "Bygning",
                    "status": "Anlagt",
                    "geometri": {
                        "wkt": "POLYGON Z((713066.46 6179936.62 32.23, 713067.19 6179935.97 32.23, 713064.41 6179932.89 32.23, 713061.26 6179935.73 32.23, 713060.71 6179935.12 32.23, 713055.68 6179939.69 32.33, 713068.61 6179953.93 32.33, 713075.98 6179947.23 32.33, 713074.37 6179945.44 32.31, 713077.25 6179942.86 32.31, 713073.03 6179938.16 32.27, 713070.15 6179940.74 32.27, 713066.46 6179936.62 32.23))"
                    },
                    "referererTil": {
                        "id_lokalId": "f207466c-fe2e-4de6-a727-53c38d22faaf",
                        "kommunekode": "0151",
                        "byg007Bygningsnummer": 1,
                        "status": "6",
                        "byg021BygningensAnvendelse": "120",
                        "liggerIBygning": {
                            "nodes": [
                                {
                                    "id_lokalId": "22a02012-6a56-45c5-a02e-29ff2f002562",
                                    "status": "9",
                                    "enh020EnhedensAnvendelse": null,
                                    "adresseIdentificerer": "0a3f50a3-27d5-32b8-e044-0003ba298018"
                                },
                                {
                                    "id_lokalId": "c0b216a2-eee5-4635-814f-a9babb0e474e",
                                    "status": "6",
                                    "enh020EnhedensAnvendelse": "120",
                                    "adresseIdentificerer": "0a3f50a3-27d5-32b8-e044-0003ba298018"
                                }
                            ]
                        }
                    }
                }
            ]
        }
    }
}
```


## DAWA udtræk ? <a name="DAWA"></a>

DAWA indeholdt i sin tid nogle super gode udtræk på adresser og husnumre. Disse kan i ret stort omfang nu reproduceres via Datafordeleren...



### DAWA adresse enkeltopslag <a name="DAWA1"></a>
```graphql
query {
  DAR_Adresse(
    virkningstid: "2025-06-04T00:00:00Z"
    registreringstid: "2025-06-04T00:00:00Z"
    first: 20
    where: { adressebetegnelse: { eq: "Lærkevej 5, 2600 Glostrup" } }

  ) {
    nodes {
      id_lokalId
      adressebetegnelse
      status
      etagebetegnelse
      doerbetegnelse
      datafordelerOpdateringstid

      adresseHarHusnummer {
        id_lokalId
        husnummertekst
        geoDanmarkBygning
        adgangsadressebetegnelse
        adgangTilBygning
        
        husnummerHoererTilSupplerendeBynavn {
          navn
        }

        husnummerHoererTilIPostnummer {
          postnr
          navn
        }

        husnummerLiggerIKommuneinddeling {
          kommunekode
          navn
          kommuneLiggerIRegion {
            regionskode
            navn
          }
        }

        husnummerHoererTilNavngivenVej {
          vejnavn
          vejadresseringsnavn
          udtaltVejnavn
          administreresAfKommune
          vejnavnebeliggenhed_vejnavnelinje { wkt crs }
          vejnavnebeliggenhed_oprindelse_noejagtighedsklasse
          vejnavnebeliggenhed_oprindelse_tekniskStandard

        navngivenVejBestaarAfNavngivenVejKommunedel(first: 1) {
            nodes {
              id_lokalId
              kommune         
              vejkode          
              status
              datafordelerOpdateringstid
            }
          }
        }

        HusnummerHarAdgangspunkt {
          id_lokalId
          position { wkt crs }
        }

        HusnummerHarVejpunkt {
          id_lokalId
          position { wkt crs }
          oprindelse_noejagtighedsklasse
          oprindelse_tekniskStandard
          oprindelse_kilde
        }

        husnummerErPlaceretPaaJordstykke(first: 10) {
          nodes {
            matrikelnummer
            jordstykkeLiggerIEjerlav {
              ejerlavskode
              ejerlavsnavn
            }
            

            jordstykkeSamlesISamletFastEjendom(first: 1) {
            nodes {
                BFEnummer
                }
            }    
        }
      }
    }
  }
}
}
```
Datafordeleren svarer:

```json
{
    "data": {
        "DAR_Adresse": {
            "nodes": [
                {
                    "id_lokalId": "0a3f50a4-bd21-32b8-e044-0003ba298018",
                    "adressebetegnelse": "Lærkevej 5, 2600 Glostrup",
                    "status": "3",
                    "etagebetegnelse": "",
                    "doerbetegnelse": "",
                    "datafordelerOpdateringstid": "2021-01-05T19:06:01.084304Z",
                    "adresseHarHusnummer": {
                        "id_lokalId": "0a3f507c-6352-32b8-e044-0003ba298018",
                        "husnummertekst": "5",
                        "geoDanmarkBygning": "1069919848",
                        "adgangsadressebetegnelse": "Lærkevej 5, 2600 Glostrup",
                        "adgangTilBygning": "2d2d8a51-a399-4dd9-a360-2e100dd85b9f",
                        "husnummerHoererTilSupplerendeBynavn": null,
                        "husnummerHoererTilIPostnummer": {
                            "postnr": "2600",
                            "navn": "Glostrup"
                        },
                        "husnummerLiggerIKommuneinddeling": {
                            "kommunekode": "0161",
                            "navn": "Glostrup",
                            "kommuneLiggerIRegion": {
                                "regionskode": "1084",
                                "navn": "Region Hovedstaden"
                            }
                        },
                        "husnummerHoererTilNavngivenVej": {
                            "vejnavn": "Lærkevej",
                            "vejadresseringsnavn": "Lærkevej",
                            "udtaltVejnavn": "Lærkevej",
                            "administreresAfKommune": "0161",
                            "vejnavnebeliggenhed_vejnavnelinje": {
                                "wkt": "MULTILINESTRING Z((714090.81 6175175.58 0, 714111.61 6175175.78 0, 714116.33 6175176.2 0, 714119.22 6175176.46 0, 714128.46 6175177.69 0, 714131.58 6175178.11 0, 714138.06 6175179.12 0, 714142.31 6175179.78 0, 714146.41 6175180.41 0, 714152.68 6175181.39 0, 714157.5 6175182.13 0, 714162.11 6175182.8 0, 714166.38 6175183.38 0, 714167.99 6175183.6 0, 714174.2 6175184.44 0, 714178.81 6175185.16 0, 714183.5 6175185.9 0, 714185.34 6175186.19 0, 714190.26 6175186.96 0, 714194.72 6175187.66 0, 714199.2 6175188.36 0, 714201.26 6175188.69 0, 714204.92 6175189.26 0, 714209.61 6175190 0, 714214.47 6175190.76 0, 714218.61 6175191.41 0, 714226.67 6175192.67 0, 714230.17 6175193.22 0, 714234.85 6175193.84 0, 714238.51 6175194.32 0, 714243.26 6175194.94 0, 714248.84 6175195.67 0, 714251.52 6175196.03 0, 714254.68 6175196.44 0, 714258.58 6175197.06 0, 714262.39 6175197.67 0, 714265.82 6175198.22 0, 714270.71 6175199 0, 714278.83 6175200.29 0), (714359.07 6175212.66 0, 714364.45 6175214.68 0), (714278.83 6175200.29 0, 714291.24 6175202.09 0, 714295.69 6175202.74 0, 714300.34 6175203.41 0, 714305.01 6175204.09 0, 714308.91 6175204.66 0, 714313.42 6175205.31 0), (714313.42 6175205.31 0, 714318.04 6175206.03 0, 714322.8 6175206.77 0, 714327.4 6175207.48 0, 714332.18 6175208.22 0, 714336.77 6175208.93 0, 714341.23 6175209.62 0, 714351.66 6175211.24 0, 714353.17 6175211.53 0, 714359.07 6175212.66 0))",
                                "crs": 25832
                            },
                            "vejnavnebeliggenhed_oprindelse_noejagtighedsklasse": "B",
                            "vejnavnebeliggenhed_oprindelse_tekniskStandard": "NO",
                            "navngivenVejBestaarAfNavngivenVejKommunedel": {
                                "nodes": [
                                    {
                                        "id_lokalId": "5c9aea6c-4eae-11e8-93fd-066cff24d637",
                                        "kommune": "0161",
                                        "vejkode": "0318",
                                        "status": "3",
                                        "datafordelerOpdateringstid": "2021-01-06T03:34:57.636900Z"
                                    }
                                ]
                            }
                        },
                        "HusnummerHarAdgangspunkt": {
                            "id_lokalId": "0a3f507c-6352-32b8-e044-0003ba298018",
                            "position": {
                                "wkt": "POINT (714148.68 6175195.39)",
                                "crs": 25832
                            }
                        },
                        "HusnummerHarVejpunkt": {
                            "id_lokalId": "12546d7e-af45-11e7-847e-066cff24d637",
                            "position": {
                                "wkt": "POINT (714150.911403932 6175181.11356872)",
                                "crs": 25832
                            },
                            "oprindelse_noejagtighedsklasse": "B",
                            "oprindelse_tekniskStandard": "V0",
                            "oprindelse_kilde": "Ekstern"
                        },
                        "husnummerErPlaceretPaaJordstykke": {
                            "nodes": [
                                {
                                    "matrikelnummer": "13kz",
                                    "jordstykkeLiggerIEjerlav": {
                                        "ejerlavskode": 20453,
                                        "ejerlavsnavn": "Hvissinge By, Glostrup"
                                    },
                                    "jordstykkeSamlesISamletFastEjendom": {
                                        "nodes": [
                                            {
                                                "BFEnummer": 2121460
                                            }
                                        ]
                                    }
                                }
                            ]
                        }
                    }
                }
            ]
        }
    }
}
```


### DAWA husnummer enkeltopslag <a name="DAWA2"></a>
```graphql
query {
  DAR_Husnummer(
    first: 1
    virkningstid: "2025-06-04T00:00:00Z"
    registreringstid: "2025-06-04T00:00:00Z"
    where: {
      adgangsadressebetegnelse: { eq: "Brombær Alle 6, Rønhøjgård, 2750 Ballerup" }
    }
  ) {
    nodes {
      id_lokalId
      husnummertekst
      geoDanmarkBygning
      adgangsadressebetegnelse
      adgangTilBygning          

      husnummerHoererTilNavngivenVej {  
        vejnavn
        vejadresseringsnavn
        udtaltVejnavn
        administreresAfKommune
        navngivenVejBestaarAfNavngivenVejKommunedel(first: 1) {
          nodes { vejkode kommune status }
        }
      }

      husnummerHoererTilSupplerendeBynavn { navn }
      husnummerHoererTilIPostnummer { postnr navn }
      husnummerLiggerIKommuneinddeling {
        kommunekode
        navn
        kommuneLiggerIRegion { regionskode navn }
      }

      HusnummerHarAdgangspunkt {
        id_lokalId
        position { wkt crs }
      }

      HusnummerHarVejpunkt {
        id_lokalId
        position { wkt crs }
        oprindelse_noejagtighedsklasse
        oprindelse_tekniskStandard
        oprindelse_kilde
      }

      husnummerErPlaceretPaaJordstykke(first: 1) {
        nodes {
          matrikelnummer
          jordstykkeLiggerIEjerlav { ejerlavskode ejerlavsnavn }
        }
      }
    }
  }
}


```
Datafordeleren svarer:

```json
{
    "data": {
        "DAR_Husnummer": {
            "nodes": [
                {
                    "id_lokalId": "0005376a-6e48-4f5a-812f-2cea9031b30d",
                    "husnummertekst": "6",
                    "geoDanmarkBygning": "1005216703",
                    "adgangsadressebetegnelse": "Brombær Alle 6, Rønhøjgård, 2750 Ballerup",
                    "adgangTilBygning": "08b312f0-30ff-4486-85ab-0df7e318095e",
                    "husnummerHoererTilNavngivenVej": {
                        "vejnavn": "Brombær Alle",
                        "vejadresseringsnavn": "Brombær Alle",
                        "udtaltVejnavn": "Brombær Alle",
                        "administreresAfKommune": "0151",
                        "navngivenVejBestaarAfNavngivenVejKommunedel": {
                            "nodes": [
                                {
                                    "vejkode": "0955",
                                    "kommune": "0151",
                                    "status": "3"
                                }
                            ]
                        }
                    },
                    "husnummerHoererTilSupplerendeBynavn": {
                        "navn": "Rønhøjgård"
                    },
                    "husnummerHoererTilIPostnummer": {
                        "postnr": "2750",
                        "navn": "Ballerup"
                    },
                    "husnummerLiggerIKommuneinddeling": {
                        "kommunekode": "0151",
                        "navn": "Ballerup",
                        "kommuneLiggerIRegion": {
                            "regionskode": "1084",
                            "navn": "Region Hovedstaden"
                        }
                    },
                    "HusnummerHarAdgangspunkt": {
                        "id_lokalId": "39265acf-762b-4230-bea4-a27cd7f44456",
                        "position": {
                            "wkt": "POINT (709714.18 6179903.34)",
                            "crs": 25832
                        }
                    },
                    "HusnummerHarVejpunkt": {
                        "id_lokalId": "11604d99-af45-11e7-847e-066cff24d637",
                        "position": {
                            "wkt": "POINT (709716.048157215 6179860.34364319)",
                            "crs": 25832
                        },
                        "oprindelse_noejagtighedsklasse": "B",
                        "oprindelse_tekniskStandard": "V0",
                        "oprindelse_kilde": "Ekstern"
                    },
                    "husnummerErPlaceretPaaJordstykke": {
                        "nodes": [
                            {
                                "matrikelnummer": "1f",
                                "jordstykkeLiggerIEjerlav": {
                                    "ejerlavskode": 22151,
                                    "ejerlavsnavn": "Ågerup By, Pederstrup"
                                }
                            }
                        ]
                    }
                }
            ]
        }
    }
}
```

### DAWA husnummer masseopslag <a name="DAWA3"></a>
```graphql
query {
  DAR_Husnummer(
    first: 100
    virkningstid: "2025-06-04T00:00:00Z"
    registreringstid: "2025-06-04T00:00:00Z"
    where: {
      kommuneinddeling: { eq: "389105" } 
    }
  ) {
    pageInfo {
      hasNextPage
      endCursor
    }
    nodes {
        id_lokalId
        husnummertekst
        geoDanmarkBygning
        adgangsadressebetegnelse
        adgangTilBygning          

      husnummerHoererTilNavngivenVej {  
        vejnavn
        vejadresseringsnavn
        udtaltVejnavn
        administreresAfKommune
        navngivenVejBestaarAfNavngivenVejKommunedel(first: 1) {
          nodes { vejkode kommune status }
        }
      }

      husnummerHoererTilSupplerendeBynavn { navn }       
      husnummerHoererTilIPostnummer { postnr navn }      
      husnummerLiggerIKommuneinddeling {            
        kommunekode
        navn
        kommuneLiggerIRegion { regionskode navn }
      }

      HusnummerHarAdgangspunkt {                         
        id_lokalId
        position { wkt crs }
      }

      HusnummerHarVejpunkt {                            
        id_lokalId
        position { wkt crs }
        oprindelse_noejagtighedsklasse
        oprindelse_tekniskStandard
        oprindelse_kilde
      }

      husnummerErPlaceretPaaJordstykke(first: 1) {       
        nodes {
          matrikelnummer
          jordstykkeLiggerIEjerlav { ejerlavskode ejerlavsnavn }
        }
      }
    }
  }
}

```
Datafordeleren svarer:

```json
{
    "data": {
        "DAR_Husnummer": {
            "pageInfo": {
                "hasNextPage": true,
                "endCursor": "TURBeE1UZzFORGd0T1RsaE5pMDBOelUwTFdKbU1qUXROelprWWpKaFlXTmpOVGsxO01qQXhPQzB3Tnkwd05GUXhOam93TURvd01DNHdNREF3TURBd0t6QXdPakF3O01qQXhPQzB3Tnkwd05GUXhOam93TURvd01DNHdNREF3TURBd0t6QXdPakF3"
            },
            "nodes": [
                {
                    "id_lokalId": "0005376a-6e48-4f5a-812f-2cea9031b30d",
                    "husnummertekst": "6",
                    "geoDanmarkBygning": "1005216703",
                    "adgangsadressebetegnelse": "Brombær Alle 6, Rønhøjgård, 2750 Ballerup",
                    "adgangTilBygning": "08b312f0-30ff-4486-85ab-0df7e318095e",
                    "husnummerHoererTilNavngivenVej": {
                        "vejnavn": "Brombær Alle",
                        "vejadresseringsnavn": "Brombær Alle",
                        "udtaltVejnavn": "Brombær Alle",
                        "administreresAfKommune": "0151",
                        "navngivenVejBestaarAfNavngivenVejKommunedel": {
                            "nodes": [
                                {
                                    "vejkode": "0955",
                                    "kommune": "0151",
                                    "status": "3"
                                }
                            ]
                        }
                    },
                    "husnummerHoererTilSupplerendeBynavn": {
                        "navn": "Rønhøjgård"
                    },
                    "husnummerHoererTilIPostnummer": {
                        "postnr": "2750",
                        "navn": "Ballerup"
                    },
                    "husnummerLiggerIKommuneinddeling": {
                        "kommunekode": "0151",
                        "navn": "Ballerup",
                        "kommuneLiggerIRegion": {
                            "regionskode": "1084",
                            "navn": "Region Hovedstaden"
                        }
                    },
                    "HusnummerHarAdgangspunkt": {
                        "id_lokalId": "39265acf-762b-4230-bea4-a27cd7f44456",
                        "position": {
                            "wkt": "POINT (709714.18 6179903.34)",
                            "crs": 25832
                        }
                    },
                    "HusnummerHarVejpunkt": {
                        "id_lokalId": "11604d99-af45-11e7-847e-066cff24d637",
                        "position": {
                            "wkt": "POINT (709716.048157215 6179860.34364319)",
                            "crs": 25832
                        },
                        "oprindelse_noejagtighedsklasse": "B",
                        "oprindelse_tekniskStandard": "V0",
                        "oprindelse_kilde": "Ekstern"
                    },
                    "husnummerErPlaceretPaaJordstykke": {
                        "nodes": [
                            {
                                "matrikelnummer": "1f",
                                "jordstykkeLiggerIEjerlav": {
                                    "ejerlavskode": 22151,
                                    "ejerlavsnavn": "Ågerup By, Pederstrup"
                                }
                            }
                        ]
                    }
                },
                {
                    "id_lokalId": "00118548-99a6-4754-bf24-76db2aacc595",
                    "husnummertekst": "43",
                    "geoDanmarkBygning": "1005207825",
                    "adgangsadressebetegnelse": "Tranebær Alle 43, Rønhøjgård, 2750 Ballerup",
                    "adgangTilBygning": "14c6339b-07c4-4396-a475-09005d851614",
                    "husnummerHoererTilNavngivenVej": {
                        "vejnavn": "Tranebær Alle",
                        "vejadresseringsnavn": "Tranebær Alle",
                        "udtaltVejnavn": "Tranebær Alle",
                        "administreresAfKommune": "0151",
                        "navngivenVejBestaarAfNavngivenVejKommunedel": {
                            "nodes": [
                                {
                                    "vejkode": "0963",
                                    "kommune": "0151",
                                    "status": "3"
                                }
                            ]
                        }
                    },
                    "husnummerHoererTilSupplerendeBynavn": {
                        "navn": "Rønhøjgård"
                    },
                    "husnummerHoererTilIPostnummer": {
                        "postnr": "2750",
                        "navn": "Ballerup"
                    },
                    "husnummerLiggerIKommuneinddeling": {
                        "kommunekode": "0151",
                        "navn": "Ballerup",
                        "kommuneLiggerIRegion": {
                            "regionskode": "1084",
                            "navn": "Region Hovedstaden"
                        }
                    },
                    "HusnummerHarAdgangspunkt": {
                        "id_lokalId": "300a3df8-5554-48d9-b6c1-25994a380770",
                        "position": {
                            "wkt": "POINT (709512.21 6179484.96)",
                            "crs": 25832
                        }
                    },
                    "HusnummerHarVejpunkt": {
                        "id_lokalId": "11604dbf-af45-11e7-847e-066cff24d637",
                        "position": {
                            "wkt": "POINT (709514.55141766 6179411.12729654)",
                            "crs": 25832
                        },
                        "oprindelse_noejagtighedsklasse": "B",
                        "oprindelse_tekniskStandard": "V0",
                        "oprindelse_kilde": "Ekstern"
                    },
                    "husnummerErPlaceretPaaJordstykke": {
                        "nodes": [
                            {
                                "matrikelnummer": "2a",
                                "jordstykkeLiggerIEjerlav": {
                                    "ejerlavskode": 22151,
                                    "ejerlavsnavn": "Ågerup By, Pederstrup"
                                }
                            }
                        ]
                    }
                }
            ]
        }
    }
}
```


## Super-queries <a name="SUPER"></a>

Disse queries er lavet for at demonstrere bredden af muligheder, de er nok ikke specielt operative.
Det kan også godt tænkes, at COST på disse bliver for høj ift. hvad der tillades senere hen...

## Informationer ud fra Adresse<a name="SUPER_ADRESSE"></a>

Med denne query hentes med udgangspunkt i adressen:

  * Adresse
  * Husnummer
  * NavngivenVej
  * NavngivenVej kommunedel
  * BBR Enhed (For adressen)
  * SupplerendeBynavn
  * Adgangspunkt
  * Postnummer
  * Kommune
  * Region
  * Afstemningsomraade
  * Jordstykke
  * Lodflade
  * SFE (For matriklen som adressen ligger på)
  * BBR Bygninger (For matriklen som adressen ligger på)
  * BBR teknisk anlæg (For matriklen som adressen ligger på)

  Performance... den er som den er :-)



```graphql
query {
  DAR_Adresse(
    registreringstid: "2025-06-04T00:00:00Z"
    virkningstid: "2025-06-04T00:00:00Z"
    first: 2
    where: { adressebetegnelse: { eq: "Lundagervej 43, 2740 Skovlunde" } }
  ) {
    # Root node : Adresse
    nodes {
      adressebetegnelse
      id_lokalId
      husnummer
      doerbetegnelse
      status
      etagebetegnelse
      doerbetegnelse
      datafordelerOpdateringstid

      # Adresse → BBR Enhed
      AdresseHarEnhed(
        first: 20
        where: { enh023Boligtype: { eq: "1" } }) {
        nodes {
            id_lokalId
            datafordelerOpdateringstid
            bygning
            enh024KondemneretBoligenhed
            enh020EnhedensAnvendelse
            enh023Boligtype
            enh026EnhedensSamledeAreal
            enh027ArealTilBeboelse
            enh028ArealTilErhverv
            enh031AntalVaerelser
            enh032Toiletforhold
            enh065AntalVandskylledeToiletter
            enh033Badeforhold
            enh034Koekkenforhold
            enh035Energiforsyning
            enh053SupplerendeVarme
            }
        }

      # Adresse → Husnummer
      adresseHarHusnummer {
        id_lokalId
        datafordelerOpdateringstid
        husnummertekst
        geoDanmarkBygning
        vejmidte
        husnummerretning { wkt crs }
        husnummertekst
        adgangTilBygning

        # Husnummer → SupplerendeBynavn
        husnummerHoererTilSupplerendeBynavn {
        id_lokalId
        navn
        status
        }

        # Husnummer → Adgangspunkt
        HusnummerHarAdgangspunkt {
        id_lokalId
        position { wkt crs }
        }

        # Husnummer → NavngivenVej
        husnummerHoererTilNavngivenVej {
            id_lokalId
            datafordelerOpdateringstid
            status
            vejnavn
            udtaltVejnavn
            vejadresseringsnavn
            udtaltVejnavn
            administreresAfKommune
            vejnavnebeliggenhed_vejnavnelinje { wkt crs }
            vejnavnebeliggenhed_oprindelse_noejagtighedsklasse
            vejnavnebeliggenhed_oprindelse_tekniskStandard

            navngivenVejBestaarAfNavngivenVejKommunedel(first: 1) {
            nodes { 
                id_lokalId
                vejkode
                kommune
                status }
            }
            
            }

        # Husnummer → Postnummer
        husnummerHoererTilIPostnummer {
          id_lokalId
          datafordelerOpdateringstid
          postnr
          navn
          postnummerinddeling
        }

        # Husnummer → Kommune
        husnummerLiggerIKommuneinddeling {
        id_lokalId
        kommunekode
        navn
        regionLokalid

        # Kommune  → Region
        kommuneLiggerIRegion {
            id_lokalId
            regionskode
            navn
        }
        }


        # Husnummer → Afstemningsomraade
        husnummerLiggerIAfstemningsomraade {
          id_lokalId
          afstemningsomraadenummer
          afstemningsstedNavn
        }
        # Husnummer → Jordstykke
        husnummerErPlaceretPaaJordstykke(first: 10) {
          nodes {
            matrikelnummer
            ejerlavLokalId
            

            # Jordstykke → SFE (Samlet fast ejendom)
            jordstykkeSamlesISamletFastEjendom(first: 1) {
            nodes {
                BFEnummer
            }
            }

            # Jordstykke → Bygninger
            id_lokalId_18_Bygning_jordstykke_ref(first: 50) {
            nodes {
                id_lokalId
                byg007Bygningsnummer
                byg021BygningensAnvendelse
                byg026Opfoerelsesaar
                byg041BebyggetAreal
                byg038SamletBygningsareal
                byg032YdervaeggensMateriale
                byg033Tagdaekningsmateriale
                byg056Varmeinstallation
                byg404Koordinat { wkt crs }   # punkt i EPSG:25832
                }

                
            }

            # Jordstykke → BBR teknisk anlæg
            id_lokalId_18_TekniskAnlaeg_jordstykke_ref(first: 50) {
                nodes {
                    id_lokalId
                    grund
                    husnummer
                    jordstykke
                    status
                    tek007Anlaegsnummer
                    tek020Klassifikation
                    tek024Etableringsaar
                    tek032Stoerrelse
                    tek034IndholdOlietank
                    tek109Koordinat { wkt crs }
                }
            }

            # Jordstykke → Lodflade 
            lodfladeRepraesentationJordstykke(first: 10) {
              nodes {
                id_lokalId
                geometri {
                  wkt        # polygon i EPSG:25832
                  crs
                }
              }
            }
          }
        }
      }
    }
  }
}
```
Datafordeleren svarer:

```json
{
    "data": {
        "DAR_Adresse": {
            "nodes": [
                {
                    "adressebetegnelse": "Lundagervej 43, 2740 Skovlunde",
                    "id_lokalId": "0a3f50a3-27d5-32b8-e044-0003ba298018",
                    "husnummer": "0a3f507b-83d6-32b8-e044-0003ba298018",
                    "doerbetegnelse": "",
                    "status": "3",
                    "etagebetegnelse": "",
                    "datafordelerOpdateringstid": "2021-01-05T19:29:22.816433Z",
                    "AdresseHarEnhed": {
                        "nodes": [
                            {
                                "id_lokalId": "c0b216a2-eee5-4635-814f-a9babb0e474e",
                                "datafordelerOpdateringstid": "2021-04-06T05:07:28.172317Z",
                                "bygning": "f207466c-fe2e-4de6-a727-53c38d22faaf",
                                "enh024KondemneretBoligenhed": "0",
                                "enh020EnhedensAnvendelse": "120",
                                "enh023Boligtype": "1",
                                "enh026EnhedensSamledeAreal": 173,
                                "enh027ArealTilBeboelse": 173,
                                "enh028ArealTilErhverv": null,
                                "enh031AntalVaerelser": 5,
                                "enh032Toiletforhold": "T",
                                "enh065AntalVandskylledeToiletter": 3,
                                "enh033Badeforhold": "V",
                                "enh034Koekkenforhold": "E",
                                "enh035Energiforsyning": null,
                                "enh053SupplerendeVarme": null
                            }
                        ]
                    },
                    "adresseHarHusnummer": {
                        "id_lokalId": "0a3f507b-83d6-32b8-e044-0003ba298018",
                        "datafordelerOpdateringstid": "2021-01-06T01:34:17.880020Z",
                        "husnummertekst": "43",
                        "geoDanmarkBygning": "1069796594",
                        "vejmidte": "0151-0613",
                        "husnummerretning": {
                            "wkt": "POINT (1 0.000000000000000122460635382238)",
                            "crs": 25832
                        },
                        "adgangTilBygning": "f207466c-fe2e-4de6-a727-53c38d22faaf",
                        "husnummerHoererTilSupplerendeBynavn": null,
                        "HusnummerHarAdgangspunkt": {
                            "id_lokalId": "0a3f507b-83d6-32b8-e044-0003ba298018",
                            "position": {
                                "wkt": "POINT (713071.33 6179947.35)",
                                "crs": 25832
                            }
                        },
                        "husnummerHoererTilNavngivenVej": {
                            "id_lokalId": "b44e03f0-7375-49e1-838a-64953bb6d9b5",
                            "datafordelerOpdateringstid": "2021-01-06T03:29:51.813005Z",
                            "status": "3",
                            "vejnavn": "Lundagervej",
                            "udtaltVejnavn": "Lundagervej",
                            "vejadresseringsnavn": "Lundagervej",
                            "administreresAfKommune": "0151",
                            "vejnavnebeliggenhed_vejnavnelinje": {
                                "wkt": "MULTILINESTRING Z((713479.4 6179842.1 0, 713484.08 6179842.78 0), (713067.78 6179968.9 0, 713095.43 6179943.93 0, 713102.77 6179937.81 0), (713336 6179828.79 0, 713350.86 6179827.38 0, 713369.49 6179827.3 0, 713387.47 6179828.7 0, 713479.4 6179842.1 0), (713102.77 6179937.81 0, 713114.65 6179927.9 0, 713133.52 6179914.27 0, 713155.44 6179900.47 0, 713170.04 6179892.79 0, 713222.66 6179869.26 0, 713286.25 6179841.85 0, 713302.44 6179836 0, 713321.3 6179831.29 0, 713336 6179828.79 0))",
                                "crs": 25832
                            },
                            "vejnavnebeliggenhed_oprindelse_noejagtighedsklasse": "B",
                            "vejnavnebeliggenhed_oprindelse_tekniskStandard": "NO",
                            "navngivenVejBestaarAfNavngivenVejKommunedel": {
                                "nodes": [
                                    {
                                        "id_lokalId": "5c99b550-4eae-11e8-93fd-066cff24d637",
                                        "vejkode": "0613",
                                        "kommune": "0151",
                                        "status": "3"
                                    }
                                ]
                            }
                        },
                        "husnummerHoererTilIPostnummer": {
                            "id_lokalId": "73e72b62-616c-4b58-9124-52d00774a336",
                            "datafordelerOpdateringstid": "2023-03-24T16:10:44.805713Z",
                            "postnr": "2740",
                            "navn": "Skovlunde",
                            "postnummerinddeling": "192740"
                        },
                        "husnummerLiggerIKommuneinddeling": {
                            "id_lokalId": "389105",
                            "kommunekode": "0151",
                            "navn": "Ballerup",
                            "regionLokalid": "389099",
                            "kommuneLiggerIRegion": {
                                "id_lokalId": "389099",
                                "regionskode": "1084",
                                "navn": "Region Hovedstaden"
                            }
                        },
                        "husnummerLiggerIAfstemningsomraade": {
                            "id_lokalId": "705652",
                            "afstemningsomraadenummer": "05",
                            "afstemningsstedNavn": "Idrætshallen"
                        },
                        "husnummerErPlaceretPaaJordstykke": {
                            "nodes": [
                                {
                                    "matrikelnummer": "6hh",
                                    "ejerlavLokalId": "21751",
                                    "jordstykkeSamlesISamletFastEjendom": {
                                        "nodes": [
                                            {
                                                "BFEnummer": 2154708
                                            }
                                        ]
                                    },
                                    "id_lokalId_18_Bygning_jordstykke_ref": {
                                        "nodes": [
                                            {
                                                "id_lokalId": "35e90904-be0d-41ac-b8aa-883d7392bf43",
                                                "byg007Bygningsnummer": 2,
                                                "byg021BygningensAnvendelse": "910",
                                                "byg026Opfoerelsesaar": null,
                                                "byg041BebyggetAreal": null,
                                                "byg038SamletBygningsareal": null,
                                                "byg032YdervaeggensMateriale": null,
                                                "byg033Tagdaekningsmateriale": null,
                                                "byg056Varmeinstallation": null,
                                                "byg404Koordinat": null
                                            },
                                            {
                                                "id_lokalId": "6cce8976-08c9-413f-bd37-c7b4e828f810",
                                                "byg007Bygningsnummer": 1,
                                                "byg021BygningensAnvendelse": "120",
                                                "byg026Opfoerelsesaar": null,
                                                "byg041BebyggetAreal": 34,
                                                "byg038SamletBygningsareal": 34,
                                                "byg032YdervaeggensMateriale": null,
                                                "byg033Tagdaekningsmateriale": null,
                                                "byg056Varmeinstallation": null,
                                                "byg404Koordinat": null
                                            },
                                            {
                                                "id_lokalId": "75425f09-86af-4494-b128-94bd7a92039a",
                                                "byg007Bygningsnummer": 2,
                                                "byg021BygningensAnvendelse": "910",
                                                "byg026Opfoerelsesaar": 1000,
                                                "byg041BebyggetAreal": 28,
                                                "byg038SamletBygningsareal": null,
                                                "byg032YdervaeggensMateriale": "2",
                                                "byg033Tagdaekningsmateriale": "4",
                                                "byg056Varmeinstallation": null,
                                                "byg404Koordinat": {
                                                    "wkt": "POINT (713074.78 6179943.31)",
                                                    "crs": 25832
                                                }
                                            },
                                            {
                                                "id_lokalId": "a7a49ad1-974b-46e6-a393-2c225b92aebb",
                                                "byg007Bygningsnummer": 3,
                                                "byg021BygningensAnvendelse": "930",
                                                "byg026Opfoerelsesaar": 2013,
                                                "byg041BebyggetAreal": 10,
                                                "byg038SamletBygningsareal": null,
                                                "byg032YdervaeggensMateriale": "5",
                                                "byg033Tagdaekningsmateriale": "2",
                                                "byg056Varmeinstallation": null,
                                                "byg404Koordinat": {
                                                    "wkt": "POINT (713054.04 6179933.69)",
                                                    "crs": 25832
                                                }
                                            },
                                            {
                                                "id_lokalId": "b072147a-ff59-4f00-859f-62ed441eaa77",
                                                "byg007Bygningsnummer": 5,
                                                "byg021BygningensAnvendelse": "930",
                                                "byg026Opfoerelsesaar": 2013,
                                                "byg041BebyggetAreal": 8,
                                                "byg038SamletBygningsareal": null,
                                                "byg032YdervaeggensMateriale": "12",
                                                "byg033Tagdaekningsmateriale": "12",
                                                "byg056Varmeinstallation": null,
                                                "byg404Koordinat": {
                                                    "wkt": "POINT (713067.91 6179932.18)",
                                                    "crs": 25832
                                                }
                                            },
                                            {
                                                "id_lokalId": "dd02ed61-d1b1-43ef-a621-536554d0d096",
                                                "byg007Bygningsnummer": 4,
                                                "byg021BygningensAnvendelse": "930",
                                                "byg026Opfoerelsesaar": 2013,
                                                "byg041BebyggetAreal": 2,
                                                "byg038SamletBygningsareal": null,
                                                "byg032YdervaeggensMateriale": "5",
                                                "byg033Tagdaekningsmateriale": "2",
                                                "byg056Varmeinstallation": null,
                                                "byg404Koordinat": {
                                                    "wkt": "POINT (713054.84 6179919.03)",
                                                    "crs": 25832
                                                }
                                            },
                                            {
                                                "id_lokalId": "f207466c-fe2e-4de6-a727-53c38d22faaf",
                                                "byg007Bygningsnummer": 1,
                                                "byg021BygningensAnvendelse": "120",
                                                "byg026Opfoerelsesaar": 1967,
                                                "byg041BebyggetAreal": 173,
                                                "byg038SamletBygningsareal": 173,
                                                "byg032YdervaeggensMateriale": "2",
                                                "byg033Tagdaekningsmateriale": "5",
                                                "byg056Varmeinstallation": "2",
                                                "byg404Koordinat": {
                                                    "wkt": "POINT (713067.09 6179944.72)",
                                                    "crs": 25832
                                                }
                                            }
                                        ]
                                    },
                                    "id_lokalId_18_TekniskAnlaeg_jordstykke_ref": {
                                        "nodes": [
                                            {
                                                "id_lokalId": "4ba0891b-6bec-4296-b5c7-c9ecf7a38dfa",
                                                "grund": "4d0cf96a-596f-40e1-b69b-011f9dab3c59",
                                                "husnummer": "0a3f507b-83d6-32b8-e044-0003ba298018",
                                                "jordstykke": "1538971",
                                                "status": "10",
                                                "tek007Anlaegsnummer": 1,
                                                "tek020Klassifikation": "1110",
                                                "tek024Etableringsaar": 1983,
                                                "tek032Stoerrelse": null,
                                                "tek034IndholdOlietank": "10",
                                                "tek109Koordinat": null
                                            }
                                        ]
                                    },
                                    "lodfladeRepraesentationJordstykke": {
                                        "nodes": [
                                            {
                                                "id_lokalId": "1538971",
                                                "geometri": {
                                                    "wkt": "POLYGON ((713066.259 6179958.381, 713040.778 6179929.5, 713055.872 6179915.949, 713084.212 6179947.816, 713072.556 6179958.52, 713066.259 6179958.381))",
                                                    "crs": 25832
                                                }
                                            }
                                        ]
                                    }
                                }
                            ]
                        }
                    }
                }
            ]
        }
    }
}
```











































```graphql

```
Datafordeleren svarer:

```json

```