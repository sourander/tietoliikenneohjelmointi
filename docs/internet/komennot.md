Tämä sivu kerää yhteen komennot, jotka ovat hyödyllisiä Internetiin liittyvien nimien, osoitteiden, porttien, latenssien ja muun selvittämisessä sekä tarjoaa yhden tai kahden rivin esimerkit niiden ajamisesta.

Aja kaikki alla olevat komennot komentokehotteessa, joka on mieluiten Windowsissa **Git Bash**, Linuxissa esimerkiksi **Bash** ja macOS:ssä sen nykyinen default **Zsh**.

Mikäli tarvitset komennon ajamiseen apua, kokeile sen omaa helppiä.

```bash
# Windows (Git Bash)
$ komento --help
$ komento /?

# Linux/macOS
$ man komento
```

Huomaathan, että voit ajaa komentoja myös esimerkiksi virtuaalikoneessa. Virtuaalikoneiden ohessa toinen vaihtoehto on ajaa komennot Online-palveluiden avulla. Näistä yksi mielenkiintoinen vaihtoehto ovat erilaiset Looking Glass -palvelut. Nämä ovat palvelimia, jotka tarjoavat käyttäjilleen mahdollisuuden ajaa komentoja operaattorin (reitittimen) näkökulmasta. Näitä palveluita on listattuna sivustolla [LookingGlass.org](https://lookinglass.org/)

Sivuston kautta voi löytää itsensä esimerkiksi [Telia Looking Glass](https://lg.telia.net/)-sivulle, jolla voit ajaa BGP, ping tai trace komentoja Telian näkökulmasta. AS-numeroita, domaineita ja IP-osoitteita voit penkoa seuraavien palveluiden avulla:

* [Hurricane Electric BGP Toolkit](https://bgp.he.net/)
    * Oma IP-osoite
    * Oma IP-verkko
    * Oma AS-numero
* [PeeringDB](https://www.peeringdb.com/)
    * Tietoa AS-numeroista
    * Tietoa IP-verkkojen välisistä peering-sopimuksista
* [BGP tools](https://bgp.tools/)
    * Oma IP, verkko ja AS-numero
    * Tietoa AS-numeroista ja niiden upstreameista.
* [IANA Whois](https://www.iana.org/whois)
    * WHOIS-tietoa domaineista (ja ip-osoitteista)
* [Google Admin Toolbox Dig](https://toolbox.googleapps.com/apps/dig/)
    * Helppo tapa tehdä DNS-hakuja.


## Ping

Ping-komento lähettää ICMP-viestin tyyppiä 8 (Echo Request). Palvelin vastaa, jos vastaa, ICMP-viestillä, jonka tyyppi on 0 (Echo Reply). Näistä lasketaan edestakainen eli round-trip viive. Alla komento, joka lähettää tasan kaksi viestiä.

```bash
# Git Bash
ping -n 2 www.example.com

# Linux/macOS
$ ping -c 2 www.example.com
```



## Trace route

Trace route hyödyntää UDP-datasähkeen Time-To-Live kenttää, asettaen sen nousevasti arvoihin `(1, 2, 3, ...)`. Reititin, jonka kohdalla hop count ylittää TTL-arvon, vastaa lähettäjälle ICMP-viestillä tyyppiä 11 (Time Exceeded).

```bash
# Git Bash
$ tracert www.example.com

# Linux
$ traceroute www.example.com
```

Linuxin ja macOS:n traceroutet tukevat suurempaa määrää optioneita kuin Windowsin vastine tracert. Eri käyttöjärjestelmien ja eri versioiden väleillä voi silti olla eroja. Tämän kirjoitushetkellä Ubuntu 22.04 ja macOS 13.5.2 tukevat alla näkyviä komentoja. 

Komento probettaa vain kerran kutakin reititintä kolmen kerran sijasta. Lisäksi komento tulostaa, mihin AS:ään reititin kuuluu. AS:t eli autonomoys systemit on esitelty aiemmin tällä kursilla: kertaa jos et muista.

```bash
# Ubuntu
$ traceroute -A -q1 www.example.com

# macOS
$ traceroute -a -q1 www.example.com
```



## ARP

ARP-protokollaa voi käskyttää työkalulla nimeltään `arp`. Alla oleva komento tulostaa kaikki ARP-taulukosta löytyvät entryt.

```bash
# Git Bash/macOS
arp -a

# Linux
arp
```

Työkalulla voi myös lisätä ja poistaa entryjä.



## Name Server Lookup

Komento on vanhempi kuin alla olevat host ja dig, mutta on yhä käytettävissä ja toimii Windowsissa vakiona.

```bash
# Git Bash/Linux/macOS
$ nslookup www.example.com
```



## Host

Mikäli DNS Lookup -työkalu `host` on asennettuna käyttöjärjestelmässäsi, voit käyttää sitä korvaamaan `nslookup`-komennon. Kannattaa kokeilla `-v` optionia, joka tulostaa verbose outputin.

```bash
# Windows
# N/A

# Linux/macOS
$ host www.example.com

# Tee ei-rekursiivinen haku suoraan juurinimipalvelimelle
$ host -r -v www.cnn.com a.root-servers.net
```



## Dig

Mikäli DNS Lookup -työkalu `dig` on asennettuna käyttöjärjestelmässäsi, myös sitä voi käyttää `nslookup`-komennon korvaajana.

```bash
# Windows
# ks. online-vaihtoehto tämän luvun yltä

# Linux/macOS
dig www.example.com

# Vaihtoehtoinen sovellus
# ks. https://www.nlnetlabs.nl/documentation/ldns/
drill www.example.com
```



## Whois

```bash
# Windows
# Lataa: https://learn.microsoft.com/en-us/sysinternals/downloads/whois

# Linux/macOS
whois example.com
```


## Netstat

```bash
# Windows
# Kaikki TCP-yhteydet ja niiden PID (Process ID)
# Ilman DNS-selvitettä
netstat -ano -p TCP

# macOS
# Sama mutta PID puuttuu
netstat -an -p TCP
```



## LSOF

Komento `lsof` toimii Linuxissa ja macOS:ssa. Komennon nimi tulee sanoista "List open files", mikä voi tuntua irrelevantilta tämän kurssin osalta, mutta Linuxissa lähes kaikki entiteetit ovat olemassa tiedostoina Linuxin file systemissä. Näin myös avoimet yhteydet. Ainakaan macOS:ssä `netstat` ei palauta yhteyden PIDiä, joten `lsof` on hyvä vaihtoehto tähän.

```bash
sudo lsof -i UDP:57621
```
