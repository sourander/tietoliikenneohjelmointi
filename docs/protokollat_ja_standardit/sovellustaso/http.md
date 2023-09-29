HTTP on protokolla, jota käytetään välittämään muun muassa HTML-sivuja. HTTP:n avulla voi siirtää käytännössä mitä tahansa dataa, kunhan data on enkoodattu tavalliseksi ASCII-merkistöksi. Tutkitaan tätä hieman käytännössä.

## Teoria

TODO

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