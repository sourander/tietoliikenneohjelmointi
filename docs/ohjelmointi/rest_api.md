REST on arkkitehtuurinen tyyli palveluiden rakentamiseen. Palvelua, joka on toteutettu REST-tyylin mukaisesti, kutsutaan sanalla REREST ei sinänsä vaadi, että sen on hyödynnettävä nimenomaan HTTP-protokollaa, mutta käytännön tasolla REST:iin törmää nimenomaan HTTP:n yhteydessä. Palvelun luoja saattaa joitakin resursseja käyttäjien saataville URI:n avulla. REST mahdollistaa näiden resurssien luomisen (create), lukemisen (read), päivittämisen (update) sekä poistamisen (delete). Sanoista muodostuu lyhenne CRUD; tämä on tyypillinen lyhenne tiedon materisoinnista tietojenkäsittelyssä. Mikäli REST hyödyntää HTTP:tä, käytetään HTTP-protokollan metodeja eli verbejä suorittamaan eri operaatioita. Kaikki HTTP:n verbit on listattu esimerkiksi [Mozillan sivuilla](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods), mutta REST tarvitsee niistä vain muutamaa, jotka on listattu alla olevassa taulukossa.

| CRUD-operaatio | REST API:n HTTP-verbi |
| :------------: | :-------------------: |
|     Create     |     POST tai PUT      |
|      Read      |          GET          |
|     Update     |     PUT tai PATCH     |
|     Delete     |        DELETE         |

!!! question "Tehtävä"
    Selvitä, miten REST eroaa yhdestä edeltäjästään nimeltään SOAP. Voidaan sanoa, että REST on vähemmän riippuvainen verbeistä kuin SOAP, ja nojaa enemmän substantiiveihin (eng. nouns). Mitä tämä tarkoittaa?



## Feikatut esimerkit

Alla esimerkki, jossa haetaan asiakkaan 123 osoite numero 42. Miksi asiakkaalla on 42 osoitetta? Ei puututa siihen.

### Hae

Pyyntö:

```http
GET /customer/123/address/42 HTTP/1.1
Host: dummyapi.com
```

Ja vastaus:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "id": 42,
    "street": "Tieto 1"
}
```



### Päivitä

Osoitteessa on virhe. Päivitetään `Tieto 1 => Taito 1` REST-rajapinnan yli. 

Pyyntö:

```http
PATCH /customer/123/address/42 HTTP/1.1
Host: dummyapi.com

{"id": 42, street": "Taito 1"}
```

Ja vastaus:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "id": 42,
    "street": "Taito 1"
}
```

### Poista

Päätetään sittenkin poistaa koko osoite.

Pyyntö:

```http
PATCH /customer/123/address/42 HTTP/1.1
Host: dummyapi.com
Data: {"id": 42, street": "Taito 1"}
```

Ja vastaus:

```http
HTTP/1.1 200 OK
Content-Type: application/json
```



## Komentoriviltä käyttö

```sh
# Nouda blogipostaus, jonka id on 1.
http https://jsonplaceholder.typicode.com/posts/1

# Luo uusi blogipostaus (leikisti)
http -f POST https://jsonplaceholder.typicode.com/posts street="Taito 1"
```

!!! question "Tehtävä"
    Yllä olevat esimerkit on ajettu HTTPie-ohjelmalla. Sinulla ei ole tätä todennäköisesti asennettuna, mutta `curl` voi hyvinkin löytyä. Selvitä, kuinka teet samat kyselyt `curl`:lla.



## Mistä dataa?

Valtaosa mielenkiintoisesta datasta on yrit4yksille tärkeää, strategista salaisuutta, mutta myös avoimesti käytettävää dataa on saatavilla. Luethan datan lisenssin tai käyttöehdot tapauskohtaisesti.

* [Rautatieliikenne | Digitraffic - Fintraffic](https://www.digitraffic.fi/rautatieliikenne/)
* [Avoin data | Tieto Traficom](https://tieto.traficom.fi/fi/tietotraficom/avoin-data?toggle=Kyberturvallisuus)
* [Avoin data - Ilmatieteen laitos](https://www.ilmatieteenlaitos.fi/avoin-data)
* Lisäksi [avoindata.fi](https://www.avoindata.fi/data/fi/apiset)-sivusto sisältää useiden eri lähteiden rajapintoja koostetusti yhdessä paikassa.
