Verkossa siirretään informaatiota, ja yksittäinen informaatiota sisältävä entiteetti on viesti. Vaikka useimmat termit tulevat vastaan vasta myöhemmissä luvuissa, kuten OSI- tai TCP/IP-mallien kerroksia läpi käydessä, on sanasto silti hyvä silmäillä jo alustavasti läpi tässä vaiheessa.



## Tietoon liittyvät termit

Kaikki sanat eivät käänny suomesta englantiin ja takaisin ongelmmitta. [Suomalaisen asiasanasto- ja ontologiapalvelun Finto:n](https://finto.fi/tt/fi/) tietotermejä selatessa on helppo huomata, että tieto kääntyy sanoiksi data, information tai knowledge riippuen aiheyhteydestä. Siitä huolimatta joitakin sanoja, kuten database, on käännetty suomeen siten, että data on käännetty nimenomaan tiedoksi, joka on ontologiassa datan yläkäsite. Käännösongelmien lisäksi samankin kielen sisällä sanoja voidaan käyttää yleiskielisesti toistensa synonyymeinä. Jos sekaannuksesta on vaaraa, kannattaa määritellä termi käytön yhteydessä.

Tässä dokumentaatiossa tärkeimmät termit ovat data ja informaatio. Muiden käyttö on joko minimaalista tai kokonaan puuttuvaa. Informaatio on se viestin sisältö, esimerkiksi HTTP GET -pyyntö, joka pyritään välittämään vastaanottajalle. Alemman kerroksen näkökulmasta tuo kyseinen jono tavuja on dataa; data enkapsuloidaan sille tarkoitettuun kenttään piittaamatta sen formaatista.

| Termi       | In English                 | Lyhyt määritelmä                                                                                                                     |
| ----------- | -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| Data        | Data                       | Raakatietoa eli karusti sanottuna ykkösiä ja nollia. Voi sisältää merkityksen, olla puhdasta mittausdataa, tai olla pelkkää kohinaa. |
| Informaatio | Information                | Dataa, jolla on jokin tunnettu muoto eli rakenne.                                                                                    |
| Tietämys    | Knowledge                  | Syitä ja seurauksia tai muuta pidemmälle jalostettua ymmärrystä informaation pohjalta.                                               |
| Tieto       | Data/Information/Knowledge | Yläkäsite, joka tarkoittaa milloin mitäkin ylemmistä termeistä                                                                       |



## Viestiin liittyvät termit

Muodolliset termit viesteille (tai tietoyksiköille) millä tahansa kerroksella ovat Protocol Data Unit (PDU), suomeksi yhteyskäytännön datayksikö, ja Service Data Unit (SDU), suomeksi palveludatayksikkö. Ajoittain PDU-termiä käytetään vain ylemmillä abtraktion tasoilla eli OSI-kerroksilla 4-7. PDU on kerroksen protokollan oma viestimuoto. Ylemmän kerroksen viestistä, joka enkapsuloidaan alemman kerroksen viestin sisään, käytetään termiä SDU. Käytännössä kuitenkin kirjallisuudessa, esimerkeissä ja dokumentaatiossa puhutaan esimerkiksi paketeista ja kehyksistä. PDU ja SDU ovat muodollisia termejä. Alla yleisesti käytetyt, mutta ei välttämättä täysin muodollisesti pätevät määrittelyt.

Eri protokollat ja mallit voivat käyttää poikkeavia nimiä tai päällekäisiä nimiä muiden käyttämien termien kanssa. Taulukko ujuttaa jo tietoa OSI-mallin tasoista; näihin tutustutaan seuraavissa luvuissa. Nyt voit käyttää sitä järjestyslukuna, joka kuvaa viestin abtraktion tasoa.

| Termi     | In English | OSI-taso | Lyhyt määritelmä                                                                                                      |
| --------- | ---------- | -------- | --------------------------------------------------------------------------------------------------------------------- |
| Viesti    | Message    | ?        | Laajakäyttöinen yleistermi. Periaatteessa mikä tahansa alla olevista tai jokin abstraktimpi viesti (kuten sähköposti) |
| Datasähke | Datagram   | 4        | Ei-kytkentäisissä protokollien viesti, esimerkiksi UDP-datasähke. Termi viittaa sähkeeseen (eng. telegram)            |
| Lohko     | Segment    | 4        | Useimmiten nimenomaan TCP-protokollan viesti.                                                                         |
| Paketti   | Packet     | 3        | OSI-mallin verkkokerroksella (3.) käytetty viestityyppi, esimerkiksi IP-paketti.                                      |
| Kehys     | Frame      | 2        | OSI-mallin datayhteyskerroksella (2.) käytetty viesti, esimerkiksi Ethernet Frame.                                    |
| Bitti     | bit        | 1        | Binääriluku eli ykkönen tai nolla.                                                                                    |

Jokaisella protokollalla on tarkasti määritelty muoto, joka tyypillisesti koostuu vähintään kahdesta seuraavista: ylätunniste, data ja alatunniste (header, data, footer). Alatunnistetta eivät käytä läheskään kaikki protokollat. Data tunnetaan tässä yhteydessä myös nimellä payload eli suomennettuna hyötykuorma. Hyötykuorman sisältö on siis ylemmän kerroksen SDU. Jokaista kerrosta kiinnostaa käytännössä vain ja ainoastaan oma ylätunniste: data on vain dataa, joka siirretään, koska näin on käsketty tehtäväksi.

## SDU ja PDU esimerkki

Alla naiivi esimerkki, jossa joka kerroksen PDU on Python dictionary, ja SDU on JSON-merkkijonoksi serialisoitu (tai muodollisemmin sarjastettu) ylemmän kerroksen PDU. Prosessi, jossa viesti sisällytetään alemman kerroksen PDU:ksi, tunnetaan nimellä kapsulointi (eng. encapsulate):

```python
import json

def serialize(pdu):
    sdu = json.dumps(pdu, indent=2)
    return sdu

# Layer N+1 PDU
n_plus_one = { 
    "header": "N+2 header", 
    "payload": "FAKEPAYLOAD"
} 

# Layer N PDU
n_plus_zero = { 
    "header": "N header", 
    "payload": serialize(n_plus_one),
    "footer": "This layer happens to use footer"
}

# Layer N-1 PDU would use SDU as ...
print(serialize(n_plus_zero))
```

Koodin tuloste näkyy alla. Huomaathan, että tämä tulosteessa näkyvä entiteetti on kerroksen `N` PDU, mutta kerroksen (`N miinus yksi`) SDU.

```json
{
  "header": "N header",
  "payload": "{\n  \"header\": \"N+2 header\",\n  \"payload\": \"FAKEPAYLOAD\"\n}",
  "footer": "This layer happens to use footer"
}
```

Mikäli Python on kielenä vieras, etkä saa esimerkistä tolkkua, palaa esimerkkiin myöhemmin, kun olet lukenut lisää sekä protokollista että Python-kielestä. Tämän luvun tärkein anti on jättää jokin muistikuva käsitteistä ja konsepteista, joihin tulet törmäämään myöhemmin tekstissä.