HTTPS ei varsinaisesti ole oma protokollansa. Applikaatio-tason viesti on yhä HTTP-viesti, mutta se salataan alemmilla kerroksilla. Salaukseen käytetty protokolal on nykyisin TLS, mutta sitä kutsutaan yhä usein aiemmalla prototokollan nimellä SSL. HTTP-palvelimet kuuntelevat tyypillisesti TCP-porttia 80, kun taas HTTPS:n vakioportti on 443.

Kun osoitekentän URI alkaa `https://`-skeemalla, asiakas ja palvelin suorittavat SSL/TLS-kättelyn, ja keskustelun ajan viestit salataan kyseisen keskustelun ajaksi sovittua symmetrista avainta käyttäen. Kättelyssä asiakas ja palvelin sopivat muun muassa käytetyn salausalgoritmin. Itse neuvottelun tekemiseen käytetään asymmetrisia avaimia, ja tähän riittää, että palvelimella on oma avaimensa. Palvelimen avaimen on signeerannut varmenneviranomainen (eng. certificate authority, CA)

| TCP-kerros        | OSI-kerros  | Protokolla     |
| ----------------- | ----------- | -------------- |
| Application       | Application | HTTP           |
| Application       | Session     | **SSL/TLS**    |
| Transport         | Transport   | TCP            |
| Internet          | Network     | IP             |
| Network Interface | Data Link   | esim. Ethernet |
| Physical          | ---         |                |

**Kuvio 1:** *TLS sijoittuu TCP/IP-protokollaperheessä applikaatiokerrokselle HTTP-protokollan alle.*

!!! question "Tehtävä"
    Ota selvää, kuinka saat omalle sivustollesi TLS-sertifikaatin Let's Encrypt -palvelua käyttäen. Yksi hyvä paikka aloittaa penkominen on [Certbot Instructions | Certbot (eff.org)](https://certbot.eff.org/instructions).

!!! question "Tehtävä"
    Suuntaa `https://www.kamk.fi` ja `https://www.yle.fi` etusivuille. Selvitä, mikä taho on kunkin sivuston sertifikaatin antanut varmenneviranomainen eli CA. Suuntaa myös sivulle `http://www.example.com/` ja tarkkaile kuinka selain käyttäytyy ei-salatun HTTP-sivuston kohdalla.

