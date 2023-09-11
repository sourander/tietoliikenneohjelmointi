Mikäli IP-paketin kohteeksi merkattu IP-osoite ei ole lähettäjän kanssa samassa verkossa, tarvitaan reititin tai useita reittitimiä, joka tai jotka sitovat eri verkot yhteen. Lähettävä tietokone voi päätellä, onko kohdetietokone samassa verkossa katsomalla ip-osoitetta ja (ali)verkon peitettä. Katso alla olevaa Kuviota 2. Kuvitellaan, että kaikissa kuvion verkoissa aliverkon peite on 255.255.255.0. Kuvitellaan myös, että yksikään laite ei ole NAT:n takana. Jätetään myös TCP-portit huomioitta.

![tcp_router_router_tcp](../images/tcp_router_router_tcp.svg)

**Kuvio 2:** *Kahden tietokoneen välinen kommunikaatio reitittimien läpi, kuvitteellisen merikaapelin läpi. MAC-osoitteet lyhennetty 24-bittisiksi tiiviimmän ilmaisun toivossa. Kuvitellaan aliverkon peitteeksi kaikissa tapauksissa /24 eli 255.255.255.0*

Asiakas `10.0.1.11` (Kuvio 2:ssa) haluaa lähettää HTTP-pyynnön palvelimelle `10.0.3.22`. Hän on ehkä saanut tämän osoitteen DNS-palvelimelta, tai ehkä se on rautakoodattu sovellukseen.



#### Vaihe 1

Asiakas laskee, kuuluuko kohdekone hänen kanssaan samaan verkkoon. `10.0.1.0/24` verkkoon kuuluvat osoitteet `10.0.1.0`-`10.0.1.255`, joten kone ei ole samassa verkossa. Tämä operaatio tapahtuu kerroksen Internet, johon kuuluvat IP-osoitteen, tasolla.



#### Vaihe 2

Asiakas lähettää paketin reitittimelle. Tämän tiedot hän on saanut joko rautakoodattuina tai DHCP-protokollan avulla saadessaan IP-osoitteen. Reitittimen osoite, eli default gateway, on tässä tapauksessa `11.0.1.1`. Henkilö, joka käyttää asiakastietokonetta, löytäisi tämän tiedon `ipconfig`-komennon avulla. Default gateway on, kuten sen on pakko olla, samassa verkossa asiakkaan kanssa. 

Siirrytään kerrokseen Network, tai tarkemminkin ARP-protokollaan, joka on liima kerrosten Internet ja Network välillä, eli IP- ja MAC-osoitteen toisiinsa liittävä protokolla. Asiakas tarkistaa omasta ARP-taulukostaan, mikä on kyseisen IP-osoitteen MAC-avain. Henkilö, joka käyttää asiakastietokonetta, löytäisi saman tiedon `arp -a`-komennon avulla. Mikäli taulukosta ei löydy tätä tietoa, eli taulukon tieto on vanhentunut, asiakas lähettää broadcast-osoitteeseen `255.255.255.255` eli fyyfiseltä osoitteeltaan `ff-ff-ff-ff-ff-ff` ARP-kyselyn. Kysely on kärjistäen `Hei kaikki, kuka teistä on 10.0.1.1 ?`. Reititin luonnollisesti vastaisi tähän kyselyyn, muut lähiverkon koneet eivät. Reititin ei välitä tätä kyselyä Internetiin: syntyisi kaaos, jos kaikki Internetin tietokoneet huhuilisivat ristiin.

Kun asiakaskone tietää reitittimen eli `10.0.1.1`:n MAC-osoitteen, joka on tässä esimerkissä helposti muistettava ja lyhennetty `aa-aa-aa`, se on valmis lähettämään paketin reittittimen MAC-osoitteeseen. Physical-tasolla tieto vaihdetaan ykkösiksi ja nolliksi eli biteiksi ja siirretään kohti mediaa, joka on tässä tapauksessa tavallinen parikaapeli.



#### Vaihe 3

Reititin vastaanottaa datansa parikaapelista. Physical taso työstää bittejä, Network-taso kasaa näistä Ethernet-kehyksiä, ja Internet-taso kasaa näistä IP-paketteja. Reititin lukee IP-paketin header-datasta kohdeosoitteen `destination ip`:n. Reitittimellä on oma [reititystaulu](https://en.wikipedia.org/wiki/Routing_table), joka olisi tässä tapauksessa jotakuinkin:

| Network     | Next Hop | Interface |
| ----------- | -------- | --------- |
| 10.0.1.0/24 | DIRECT   | ethLan    |
| 1.2.3.0/24  | DIRECT   | ethWan    |
| 10.0.3.0/24 | 1.2.3.5  | ethWan    |

Reititin näkee taulukosta, että kohdeosoite `10.0.3.22` on jossakin reitttimen `1.2.3.5` takana, joten IP-paketti sisältöineen pitää välittää reitittimelle `1.2.3.5`. Se katsoo kyseisen `.5`-reitittimen MAC-osoitteen ARP-taulusta, ja jos sitä ei löydy, suorittaa omassa lähiverkossaan eli `1.2.3.0` ARP-kyselyn. Kun lähetettävä IP-paketti saapuu reittimen Network-kerrokselle, siihen kiinnitetään `source MAC`:ksi laitteen oma eli `BB-BB-BB` ja kohteeksi eli `destination MAC`:ksi kohdereitittimen MAC eli `DD-DD-DD`. Ethernet-kehys siirtyy taas Physical-tasolle, jossa se käsitellään biteiksi.



#### Vaihe 4

Seuraava reititin saa datansa parikaapelista. Myös tämä reititin lukee IP-paketin header-datasta kohdeosoiteen eli `destination ip`. Kyseinen IP-osoite, `10.0.3.22`, kuuluu verkkoon `10.0.3.0/24`, johon on suora reitti ethLan-verkkosovittimesta.

| Network     | Next Hop | Interface |
| ----------- | -------- | --------- |
| 10.0.1.0/24 | 1.2.3.4  | ethWan    |
| 1.2.3.0/24  | DIRECT   | ethWan    |
| 10.0.3.0/24 | DIRECT   | ethLan    |

Paketin voi siis välittää perille MAC-osoitetta käyttäen. Reititin katsoo sen omasta ARP-taulustaan, ja mikäli sitä ei löydy, se suorittaa ARP-kyselyn. Kun lähetettävä IP-paketti saapuu reittimen Network-kerrokselle, siihen kiinnitetään `source MAC`:ksi laitteen oma MAC eli `DD-DD-DD`, ja kohteeksi eli `destination MAC`:ksi palvelimen MAC eli `22-22-22`. Data siirretään lähiverkossa perille.



#### Vaihe 5

Palvelin saa viestin ja se kulkeutuu Ethernet ja TCP/IP-protokollaperheen kerrosten läpi, kunnes saapuu lopulta (TCP-portin 80 avulla) HTTP-palvelimen käsiteltäväksi. IP-paketissa lukee yhä alkuperäinen lähettäjä eli `1.2.3.4`, joten palvelin tietää, kenelle vastaus pitää lähettää. Myös `source port` ja `destination port` ovat kulkeneet IP-paketissa jatkuvasti mukana. Vain MAC-osoitteisiin (ja hop counteriin) on puututtu matkan varrella.



!!! question "Tehtävä"
    Aihe on kohtalaisen monimutkainen ja sen parissa kannattaa viettää aikaa. Tutustu aiheisiin myös muiden lähteiden avulla. Yksi suositeltu lähde on [Practical Networking: Networking Fundamentals](https://www.youtube.com/watch?v=bj-Yfakjllc&list=PLIFyRwBY_4bRLmKfP1KnZA6rZbRHtxmXi)-soittolista YouTubessa. Lesson 5 (Pt. I & II) käsittelee reititystä.



!!! question "Tehtävä"
    Yllä olevassa esimerkissä jätettiin NAT käsittelemättä, vaikka lähiverkon ip-osoite `10.x.x.x` vaihtui julkiverkon IP-osoitteeksi `1.x.x.x`. Mikäli NAT tosiaan olisi käytössä, kuinka Internet-tason headereihin, varsinkin `source IP`:hen, olisi kajottu ja missä vaiheissa?