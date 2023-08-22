Termit URI, URL ja URN on helppo sekoittaa toisiinsa, ja usein URI:a ja URL:ia käytetäänkin melko huoletta ristiin. 

* **URI** (Uniform Resource Identifier)
    * on ylemmän tason käsite URL:lle ja URN:lle
    * on merkkijono, jolla tunnistetaan (eng. identify) rerurssi Internetissä.
* **URL** (Uniform Resource Locator)
    * on URI:n yksi muoto
    * on merkkijono, jolla tunnistetaan **ja** ohjeistetaan kuinka resurssi kuuluu avata.
* **URN** (Uniform Resource Name)
    * on URI:n yksi muoto
    * on merkkijono, joka antaa uniikin ja pysyvän tunnisteen jollekin resurssille.



```
# URL
https://www.example.com

# URN
URN:NBN:fi-fe2023082198656
```

!!! question "Tehtävä"
	Käy [Theseuksessa](https://www.theseus.fi/) ja etsi hakutyökalulla jokin, esimerkiksi Kajaanin Ammattikorkeakoulun kokoelmaan kuuluva, opinnäytetyö. Etsi mistä löytyy opinnäytetyön pysyvä osoite ja tutki sen muotoa. Mikä osuus tuosta merkkijonosta on URI (sen lyhimmissä muodossaan), mikä URL, ja mikä URN?

Huomaa, että URL on aina URI, koska URI on ylemmän tason käsite. URN on myös aina URI. URI on siis URN:n ja URL:n ylijoukko (eng. superset.) Jos piirrät Venn-kaavion, niin URN ja URL ovat URI:n sisällä.

Jos verkko-osoite sisältää protokollan (esim. `https://`) merkkijonon alussa, ==voit turvallisesti kutsua sitä URL:ksi==.



## URL:n osatekijät

Terminologista pilkunviilausta tärkeämpää lienee ymmärtää, mistä URL ylipäätänsä koostuu. Alla on [Uniform Resource Identifier](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier)-wikistä lainattu selite:

```
          userinfo       host      port
          ┌──┴───┐ ┌──────┴──────┐ ┌┴┐
  https://john.doe@www.example.com:123/forum/questions/?tag=networking&order=newest#top
  └─┬─┘   └─────────────┬────────────┘└───────┬───────┘ └────────────┬────────────┘ └┬┘
  scheme          authority                  path                  query           fragment
```

| Osaktekijä | Selite                                                       |
| ---------- | ------------------------------------------------------------ |
| scheme     | Protokolla (tai access method), jota noudattaen data tulee noutaa. |
| userinfo   | Muotoa `username:password`. Harvoin käytössä HTTP(S)-protokollan kanssa, mutta mikäli URI viittaa tietokantaan, se voi hyvinkin olla muotoa: `mysql://username:password@host:port/database` |
| host       | Oikealta lukien, pisteellä erotettuna, TLD (top level domain) esim. `.com`, SLD (second level domain) esim. `example`, ja optionaalisesti alemman tasoin osoitteita (subdomain), kuten `a.b.c.d.e` tai hyvin yleinen `www.` |
| port       | Protokollan portti. Selain täydentää vakion, mikäli sitä ei ole määritelty. Vakiot ovat HTTP 80, HTTPS 443, FTP 21 jne. |
| path       | Projektin juuresta lukien hakemistopolku haluttuun resurssiin. Mikäli kyseessä on kansio `foo/bar/`, niin selain etsii HTTP(S):n tapauksessa index.html-nimistä tiedostoa. Polku voi sisältää myös tiedostonimen, kuten `egg/ham/spam.php`. |
| query      | Query alkaa kysymysmerkillä ja sisältää et-merkillä erotellun listan parametrejä muodossa:  `<parameter>=<value>`. Palvelimen päässä nämä parsitaan ja niitä käsitellään muuttujina. |
| fragment   | Fragment viittaa tiettyyn kohtaan resurssissa. Kyseessä on ikään kuin kirjanmerkki. Kokeile käytännössä: `https://fi.wikipedia.org/wiki/HTTP#Pyyntö` |

Huomaathan, että path:ia edeltävät osat URL:ssa ovat case-insensitive eli eivät erottele pieniä ja suuria kirjaimia toisistaan: `hTTpS://wWw.GoOgLe.CoM` on sama kuin `https://www.google.com`. Sen sijaan path on case-sensitive, joten `foo/BAR` on eri resrurssi kuin `foo/bar`.

