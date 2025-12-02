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


<h1 style="font-size:80px;color:white">Fleksibel opslagslogik og GraphQL</h1>

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

<h1 style="font-size:50px;color:white">Hvad skal vi igennem ?</h1>

* Adgang til den fleksible opslagslogik 

* Det fleksible schema

* Konstruktion af forespørgsler

* Eksempler på anvendelse 

* Kendte begrænsninger

---
![bg](./img/first.png)
<h1 style="font-size:50px;color:white">Adgang til den fleksible opslagslogik </h1>



---
![bg](./img/first.png)
<h1 style="font-size:50px;color:white">Det fleksible schema</h1>

* Realtioner i grunddataprogrammet

* Som text

* Som diagrammer

---
![bg](./img/first.png)
<h1 style="font-size:50px;color:white">Konstruktion af forespørgsler</h1>

* ChatGPT ?

* Postman

* Øvrige

---




![bg](./img/first.png)
<h1 style="font-size:50px;color:white">Eksempler på anvendelse </h1>

---

![bg](./img/first.png)
<h1 style="font-size:50px;color:white">Kendte begrænsninger </h1>

* Max 20 joins pr. forespørgsel

* Ingen brug af alias'er

* Kun en root-node pr. forespørgsel


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