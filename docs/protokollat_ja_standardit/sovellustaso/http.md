HTTP on protokolla, jota käytetään välittämään muun muassa HTML-sivuja. HTTP:n avulla voi siirtää käytännössä mitä tahansa dataa, kunhan data on enkoodattu tavalliseksi ASCII-merkistöksi. Tutkitaan tätä hieman käytännössä.

## Teoria

On tärkeää tiedostaa, että Internet ja World Wide Web eivät ole sama asia. Internet on se valtava verkko, johon on kytkeytynyt useita eri verkkoja (eng. interconnected networks, internetworks). World Wide Web eli WWW on järjestelmä, joka mahdollistaa dokumenttien jakamisen Internetissä, ja se käyttää kommunikaatioon protokollaa nimeltään HTTP (Hypertext Transfer Protocol). HTTP on siis protokolla eli käytäntö, jota web-selaimet kuten Chrome käyttävät pyytäessään HTML-dokumentteja tai muita Webiin kuuluvia dokumentteja ja tiedostoja. Alkuperäinen HTTP on salaamatonta tietoa, joten kuka tahansa välikäsi voi lukea viestin sisällön. Nykyisin tyypilisimmin nettisivut käyttävät HTTP:ää, josta voit lukea seuraavasta luvusta lisää.

Hypertext Transfer Protocol siirtää siis paljon muutakin kuin varsinaista Hypertextiä kuten HTML-dokumentteja. Samaa protokollaa käytetään myös muiden assettien, kuten kuvien ja JavaScript-tiedostojen, hakemiseen. Sana *hakeminen* on tärkeä. HTTP on hyvin yksinkertaisiin **kyselyihin** ja **vastauksiin** (eng. request and response) perustuva protokolla.

HTTP:n on luonut Tim Berners-Lee ja järjestelmä sai alkunsa 1989. Tällöin Berners-Lee työskenteli CERN:llä. Mikäli historia kiinnostaa tarkemmin, tähän voi tutustua W3C:n [A Little History of the World Wide Web](https://www.w3.org/History.html)-aikajanalla, joka sisältää muun muassa linkin alkuperäiseen webin aloittaneeseen dokumenttiin "Information Management: A Proposal". Ensimmäinen ikinä käytössä ollut HTTP-protokolla oli versioltaan HTTP/0.9. Kysely oli tasan yhden rivin pitkä, esimerkiksi `GET /page.html\r\n`. Muita verbejä kuin GET ei ollut määritelty vielä. Vastaus ei sisältänyt mitään metadataa vaan pelkän sivun. Katso Mozillan [Evolution of HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/Evolution_of_HTTP#http0.9_%E2%80%93_the_one-line_protocol) sivulta esimerkki kutsusta ja palautuvasta sivusta.

| Versio   | Spesifikaation vuosi                               |
| -------- | -------------------------------------------------- |
| HTTP/0.9 | 1991                                               |
| HTTP/1.0 | 1996                                               |
| HTTP/1.1 | 1994                                               |
| HTTP/2   | 2015                                               |
| HTTP/3   | [TBA](https://www.rfc-editor.org/rfc/rfc9114.html) |

HTTP/1.0 lisäsi standardiin verbit HAED ja POST, versionumeron, HTTP headerit (esim. `Accept: text/html`), sekä statuskoodit – ja ylipäätänsä palautuvaan viestiin muutakin sisältöä kuin itse dokumentin. Samalla lisättiin tuki myös muun materiaalin kuin `text/html`:n siirtämiseen.

!!! warning
    HTML-dokumentteihin oli siis jatkossa mahdollista upottaa GIF-kuvia tai -animaatioita. Näistä suorastaan pärisevän kuuluisa oli Ally McBealissäkin vilahtanut [Dancing Baby GIF](https://en.wikipedia.org/wiki/Dancing_baby).

!!! question "Tehtävä"
    Etsi 1990-luvun puolivälin verkkosivustoja jostakin Internetin arkistointipalvelusta. Esimerkkinä [Hakupalvelu www.fi](https://web.archive.org/web/19961104154439/http://www.fi/) vuodelta 1996.

HTTP/1.1 julkaistiin vain muutamaa kuukausi HTTP/1.0:n jälkeen. Se lisäsi muutaman ominaisuuden, jotka näin jälkikäteen saattavat tuntua jopa itsestäänselviltä. Näistä ehkä merkittävimmät ovat:

* Host-headerista (`Host: example.com`) tuli pakollinen. Tämä mahdollisti sen, että yhden ja saman ip-osoitteen palvelin palvelee usean eri domainin verkkosivua.
* Yhteyden voi jättää päälle (`Connection: Keep-Alive`), jolloin jokaista pyyntöä varten ei tarvitse luoda uutta yhteyttä.
* Pyyntöjen liukuhihnoittaminen (eng. pipelining) lisättiin, jolloin asiakas voi lähettää uusia `GET`-pyyntöjä, vaikka edellisen käsittely olisi yhä kesken. Tämä nopeuttaa latausta sivuissa, joissa on suuri määrä eri dokumentteja (CSS, JavaScript, kuvia...).
* Sisällön kieleen ja merkistöön liittyvä metadatalle asetettiin muoto ja paikka (esim. `charset= utf-8`).

HTTP/1.1 ei levännyt täysin staattisesti laakereillaan, vaan siihen on lisätty vuosien varrella toiminnallisuuksia, joista osa voidaan luokitella omiksi protokollikseen, kuten WebSocket ja HTTPS.

Yllä mainittiin statuskoodit. Ne tulevat palvelinta kokeillessa tutuksi. Alla kuitenkin muutama statuskoodi, joihin todennäköisesti törmäät jo tämän kurssin aikana:

| Statuskoodin kategoria | Esimerkki                                                    |
| ---------------------- | ------------------------------------------------------------ |
| 2xx                    | **200** (OK). Pyyntöön vastattu onnistuneesti.               |
| 3xx                    | **301** (Moved Permanently). Sivu löytyy toisesta URL:sta, joka lukee vastauksen headerissa. |
| 4xx                    | **404** (Not Found). Dokumentti, jota GET pyysi, ei ole olemassa. |
| 5xx                    | **500** (Internal Server Error). Geneerinen virhe. Palvelinpuolen koodissa on tapahtunut jotain odotamatonta. |

HTTP/2.0 ja uudemmat käsitellään toisessa luvussa.



## Käytäntö

### HTML ja HTTP Forms

Alla olevan harjoitteen seuraamiseen tarvitset seuraavat ohjelmat:
* Python (HTTP-palvelinta varten)
* curl (tulee Git Bashin mukana)
* Wireshark (asenna [heidän nettisivuiltaan](https://www.wireshark.org/))

#### Luo tarvittavat tiedostot

Aloitetaan tutkimalla asiaa tavallisen nettisivun ja HTTP-palvelimen avulla. Luo valitsemaasi kansioon seuraavat tiedostot:

```
.
├── index.html
├── receiver.html
└── static
    └── js
        └── receiver.js
```

Lisää `index.html`-tiedostoon seuraava sisältö:

```html
<!-- Body of index.html -->
<body>
    <form action="receiver.html" method="GET" accept-charset="UTF-8">
        <!-- Param one-->
        <label for="param">Parameter:</label>
        <input type="text" id="param" name="param" value="example">

        <!-- Button -->
        <button type="submit" value="Submit">Submit</button>
    </form>
</body>
```


Yllä on HTML 5 -dokumentin koodi, pois lukien html ja head tagit. Lisää ne itse. HTML-tiedostossa on yhden kentän sisältävä HTML Form eli kaavake. Formin action on "receiver.html", eli `submit`-näppäin lähettää HTTP-pyynnön relatiiviseen osoitteeseen `receiver.html`, mikä on koko osoitteena tässä tapauksessa `http://localhost:8000/receiver.html` - kunhan käynnistät palvelimen.

Lisää `receiver.html`-tiedostoon ja `receiver.js`-tiedostoon alla näkyvät sisällöt. Luomme toisen HTML-tiedoston, jotta pääsemme JavaScriptillä muuttujiin käsiksi.

```html
<!-- Body of receiver.html -->
<body>
    <p>
        Get back to form by clicking <a href="index.html">THIS LINK</a>
    </p>

    <h2>Received GET Parameters:</h2>
    <div id="output"></div>
    <script type="module" src="static/js/receiver.js"></script>
</body>
```

```js
// The contents of static/js/receiver.js

function displayGets() {
    // Check url docs: https://developer.mozilla.org/en-US/docs/Web/API/URL/search
    const queryString = window.location.search;

    // Check docs: https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams
    const mySearchParams = new URLSearchParams(queryString);

    // Get the element where we append these
    const outputElement = document.getElementById('output');

    for (const [key, value] of mySearchParams.entries()) {
        const paramElement = document.createElement('p');
        paramElement.textContent = `${key}: ${value}`;
        outputElement.append(paramElement);
        console.log(key, value)
    }
}  

// Call the function to display GET parameters when the page loads
displayGets();
```

#### Käynnistä palvelin

Navigoi oikeaan kansioon ja aja komento:

```python
python -m http.server -b 127.0.0.1 # [-d subdirectory]
```

#### Testaa

1. Käynnistä Wireshark ja aloita loopback devicen pakettien kaappaus.
2. Navigoi nettiselaimella osoitteeseen `http://127.0.0.1:8000`. Pythonin HTTP-serverin pitäisi tarjota linkki tähän. 
3. Syötä param-kenttään jokin merkkijono, kuten `Späm späm späm * 100`
4. Pysäytä Wiresharkin kaappaus
5. Analysoi dumppia Wiresharkissa

!!! question "Tehtävä"
    Vaihda HTML Formista method `GET` => `POST`. Katso, mitä tapahtuu sekä Wiresharkissa että selaimessa, kun yrität käyttää kaavaketta. Muistathan painaa refreshiä (++f5++) välissä, jotta `index.html` on varmasti ladattu uusiksi.

### HTTP-pyyntö komentoriviltä

Käytetään seuraavaksi verkkoselaimen sijasta huomattavasti yksinkertaisempaa sovellusta nimeltään curl. Tarkista alkuun, että ohjelma on asennettuna. Sen pitäisi olla Git Bash:ssä vakiona:

#### curl localhost

```bash
$ curl --version
curl 8.1.2 (x86_64-apple-darwin22.0) libcurl/8.1.2 (SecureTransport) LibreSSL/3.3.6 zlib/1.2.11 nghttp2/1.51.0
...
```

Kokeile seuraavia komentoja. Tarkkaile myös, mitä tapahtuu

```bash
# Luo shell-istunnon ajaksi muuttuja url
$ url="http://127.0.0.1:8000"

# Kutsu index.html-tiedostoa
$ curl $url

# Kutsu myös receiveriä
$ curl "$url/receiver.html?param=kissa"

# Kokeile myös verbose optionia
$ curl -vvv $url
```

#### curl web

Kokeillaan myös sivuston JSON Placeholder käyttöä. Vierailethan sivuilla, jotta tiedät, mistä on kyse!

```bash
# Luo shell-istunnon ajaksi muuttuja url (jyrää edellisen arvon)
$ url="https://jsonplaceholder.typicode.com"

# Tee POST, GET ja DELETE
```