Telnet on hyvin varhainen Internet-standardi ([IETF STD 8](https://www.rfc-editor.org/info/std8)) ja käyttää pohjalla TCP:tä. Standardiin kuuluvan dokumentin RFC 854 tiivistelmä kertoo olennaisen:

> The purpose of the TELNET Protocol is to provide a fairly general, bi-directional, eight-bit byte oriented communications facility. Its primary goal is to allow a standard method of interfacing terminal devices and terminal-oriented processes to each other. It is envisioned that the protocol may also be used for terminal-terminal communication ("linking") and process-process communication (distributed computation). -- RFC 854 Abstract

Lainauksessa mainittu "eight-bit byte" viittaa siihen, että merkistönä käytettiin US-ASCII:ta, josta yleensä käytetään lyhyempää termiä ASCII. Merkistöön kuuluuvat avaruuden ensimmäiset 128 lukua (`00`-`7F` eli desimaalina `0-127`). Nämä merkit löydät esimerkiksi [ASCII - Wikipedia](https://en.wikipedia.org/wiki/ASCII#Character_set)-sivustolta. Viimeinen merkki (`FF`) on escape character, ja monet muut tavut, joiden merkitsevin eli vasemmanpuoleisin bitti on asetettu tilaan 1, ovat komentoja, jotka syötetään `FF`:n perään.

Telnet mahdollistaa, tai ehkä pikemminkin mahdollisti, sen, että yhtä tietokonetta operoiva henkilö pystyi etäkäyttää verkon yli toista konetta. Tämä voi nykyaikana kuulostaa itsestäänselvyydeltä, mutta ARPANetin alkuaikoina käyttäjällä piti olla yksi terminaali per yksi keskustietokone, mikä on ilmeisen tehotonta. Vielä tehottomampaa oli, jos keskustietokone oli fyysisesti eri paikassa kuin terminaali. Tällöin piti rakentaa dedikoitu piiri terminaalin ja keskustietokoneen välille. Telnet ratkaisi tämän ongelman keskeisellä idealla: "Network Virtual Terminal".

Telnetiä hiottiin jopa melko pitkään. Ensimmäinen telnetiin liittyvä RFC 97 (A First Cut at a Proposed Telnet Protocol) on vuodelta 1971, kun taas RFC 854 on vuodelta 1983. RFC 854:n mukaan Telnet rakentuu kolmen peruspilarin päälle:

1. Network Virtual Terminal
2. Negotiated options
3. Symmetric view of Terminal and Processes

Kohta yksi, NVT, viittaa virtuaaliseen terminaaliin. Kyseisen tietokoneen telnet-ohjelma ottaa vastaan käyttäjältä syötteen, jonka se kääntää NVT:n natiiviin formaattiin. Kohta kaksi mahdollistaa, että asiakas ja palvelin voivat neuvotella eri asetuksia päälle tai pois, kuten "SUPPRESS-GO-AHEAD", mikä mahdollistaa full duplexin. Half duplex vaatii, että viestiä lähettävä taho päättää viestin "GO AHEAD"-komentoon, ja vain toinen taho voi lähettää kerrallaan. Kohta kolme viittaa siihen, että vaikka asiakas käynnistääkin telnet-yhteyden palvelimeen, niin yhteyden muodostamisen jälkeen keskustelukumppanit ovat tasa-arvoisia, ja data sekä neuvottelut aiemmin mainituista asetuksista voivat virrata kumpaan tahansa suuntaan.

!!! question "Tehtävä"
    Mieti yllä olevan kuvauksen perusteella, lukisitko NVT:n kuuluvan OSI-mallin kerrokseen 6 eli Presentation vai 7 eli Application?

!!! question "Tehtävä"
    Mitkä protokollat ovat korvanneet telnetin etäkäytössä? Millä protokollalla käytät Linuxia etänä? Entä Windowsia? Mitä komentoja ovat `rlogin` ja `rcopy`?



## Muut käyttötarkoitukset

Tyypillinen tietokoneen käyttäjä ei ole enää vuosikymmeniin käyttänyt telnetiä etäkäyttöön. Telnet yhdistetään helposti nimenomaan etäkäyttöön, mutta Telnet ei määrittele, käytetäänkö keskustelukanavaa etäkäyttöön vai johonkin muuhun käyttötarkoitukseen. Monet muut protokollat, kuten FTP, SMTP ja HTTP kommunikoivat lähettämällä viestejä Telnetin NVT:tä käyttäen.

Kokeile tätä käytännössä!

1. Mikäli telnet ei ole asennettuna tai aktivoituna, etsi siihen käyttöjärjestelmän/distron mukainen ohje. MacOS:ssä tai Linuxissa voit käyttää myös sovellusta nimeltään nc.
2. Aja komento `telnet example.com 80`, mikä avaa NVT:n porttiin 80. Porttia 80 kuuntelee HTTP-palvelin, joten sille voi syöttää HTTP-protokollan mukaisia viestejä. HTTP ei ymmärrä etäkäytöstä mitään.
3. Kirjoita alla oleva viesti. Paina ++ctrl+shift+enter++ joka rivin välissä ja lopuksi lähetä viesti painamalla ++enter++ -näppäintä kerran tai kahdesti.

```http
GET / HTTP/1.1
Connection: close
Host: example.com
User-Agent: FeikkiSelain/1.0
```

Voit kokeilla myös tiiviimpää muotoa, jossa jätät muun muassa HTTP-protokollan version määrittelemättä:

```http
GET /
Host: Example.com
```

