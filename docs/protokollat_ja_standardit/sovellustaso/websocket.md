Aiemmissa otsakkeissa on esitelty HTTP header, joka lähetetään jokaisen HTTP-pyynnön mukana. HTTP-pyynnön headerit voivat olla kilotavujen kokoluokassa, mikäli headereihin kuuluu keksejä. Kilotavun paketin lähettäminen jatkuvasti tilanteessa, jossa kommunikaatio on chat-applikaation tyyliin tiivistä, olisi haaskausta. WebSocket-protokolla ratkaisee tämän ongelman. WebSocketin frame header on minimaalinen.

Aiemmat esitellyt protokollat ovat perustuneet pääosin client-server -malliin. Mikäli näin haluttaisiin rakentaa chat-applikaatio, toimintaperiaate olisi, että client kävisi jatkuvasti pollaamassa, että "Onko minulle viestejä?". WebSocket ratkaisee myös tämän ongelman. WebSocket-protokolla on kaksisuuntainen, jatkuvasti avoin yhteys palvelimen ja asiakkaan välillä. Tämä mahdollistaa sen, että palvelin voi lähettää viestejä asiakkaalle ilman, että asiakas on pyytänyt niitä. 

On tärkeä muistaa, että protokolla on vain protokolla, ja ohjelmistonkehittäjät voivat luoda sillä haluamiaan ohjelmia. Vaikka yllä puhutaan chat-applikaatiosta, se on vain esimerkki. WebSocket-protokollan avulla voidaan toteuttaa esimerkiksi:

* Tulospalvelu (urheilumatsin aikana)
* Verkkopeli
* IoT-laitteen kommunikointikanava
* Dashboard-näkymä
* Notifikaatiopalvelu

WebSocket-protokolla on kuvattu [RFC 6455](https://tools.ietf.org/html/rfc6455) -standardissa.

## WebSocket kättely

Yhteyttä varten tulee muodostaa base64-enkoodattu SHA1-hash Sec-WebSocket-Key -headerin arvosta ja GUID:sta. Alla tämän osatekijät:

* Sec-WebSocket-Key: 16 tavua pitkä satunnainen merkkijono
* Globally Unique Identifier (GUID): constant 258EAFA5-E914-47DA-95CA-C5AB0DC85B11
* Sec-WebSocket-Accept: SHA1(Sec-WebSocket-Key + GUID) -> base64

Itse kättely on hyvin yksinkertainen. Client lähettää pyynnön, jossa on Sec-WebSocket-Key -header. Palvelin lähettää vastauksen, jossa on Sec-WebSocket-Accept -header. Tämän jälkeen yhteys on muodostettu.

Asiakas eli client muodostaa Sec-WebSocket-Key -headerin arvon seuraavasti:

```python
import os, base64

# Muodosta 16 tavua pitkä satunnainen merkkijono
key = os.urandom(16)

# Base64-enkoodaa merkkijono
sec_websocket_key = base64.b64encode(key).encode(encoding='utf-8')
print(sec_websocket_key)
'x3JJHMbDL1EzLkh9GBhXDw=='
```

Pyyntö palvelimelle:

```http
GET /chat HTTP/1.1
Host: example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: PsBiRKg9c1uZIx/SjDELUg==
Sec-WebSocket-Version: 13
```

Palvelin muodostaa Sec-WebSocket-Accept -headerin arvon seuraavasti:

```python
# Staattinen GUID (ks. RFC 6455)
GUID = "258EAFA5-E914-47DA-95CA-C5AB0DC85B11"

# Liimaa client key ja GUID yhteen
concatenated = client_sec_websocket_key + GUID

# Muodosta SHA1-hash
hashed = hashlib.sha1(concatenated.encode('utf-8')).digest()

# Base64-enkoodaa hash
sec_websocket_accept = base64.b64encode(hashed).decode('utf-8')
print(sec_websocket_accept)
'HSmrc0sMlYUkAGmm5OPpG2HaGWk=
```

Palvelimen vastaus sisältää HTTP-statuskoodin 101, mikäli kättely on suoritettu onnistuneesti.

```http
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: HSmrc0sMlYUkAGmm5OPpG2HaGWk=
```

## WebSocketin kehys

Jatkossa kommunikaatio suoritetaan WebSocket-protokollan osalta WebSocket-kehyksellä, joka välitetään asiakkaan ja palvelimen välillä TCP:n payloadina. Huomaa, että yhteys avattiin HTTP-pyynnöllä, mutta jatkossa WebSocket-kommunikaatio ei tarvitse HTTP headereita laisinkaan. Siinä missä HTTP-pyynnön headerit voivat olla kilotavujen kokoluokassa, WebSocket-kehyksen header on vain muutaman tavun kokoinen.

Voit tutustua kehyksen sisältöön [Mozilla: Writing WebSocket servers](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API/Writing_WebSocket_servers#format)-sivulla.

!!! question "Tehtävä"

    WebSocket-kehyksen muodostaminen käsin ja sen lähettäminen telnetillä olisi tuskaa, joten tuon vaiheen voit ohittaa. Aja sen sijasta lokaalia WebSocket-palvelinta (esim. FastAPI:lla) ja muodosta siihen yhteys clientillä (esim. JavaScript WebSocket API). Kun teet tämän, tutki mitä tietoa palvelimen ja asiakkaan välillä liikkuu Wireshark-sovelluksen avulla.


!!! question "Tehtävä"
    Tutustu, kuinka Slack käyttää websocketia: [Traffic 101: Packets Mostly Flow](https://slack.engineering/traffic-101-packets-mostly-flow/)