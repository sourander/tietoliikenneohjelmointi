## Projektikansion sisältö

Projektin voi järjestää usealla eri tavalla. Yksi tapa on sijoittaa yhteen hakemistoon hakemistot "frontend" sekä "backend", jolloin niistä tulee *monorepo*-tyylinen projekti.  Koko hakemisto, sisältäen sekä backendin että frontendin, mene samaan git-repositorioon esimerkiksi GitHubiin.

```sh
$ tree /home/user/Code/$GIT_USERNAME/$GIT_PROJECT/
/home/user/Code/gitusername/my-monorepo-project/
├── .git
├── frontend       
│   ├── *           # HTML, CSS, JavaScript
│   └── README.md 
├── backend
│   ├── *          # Python
│   └── README.md 
└── README.md
```

Vaihtoehto on tietenkin `polyrepo`, jossa frontendt ja backend nähdään palveluina, jotka ansaitsevat omat repositorionsa.

```sh
$ tree /home/user/Code/$GIT_USERNAME/$GIT_FRONT_PROJECT/
/home/user/Code/gitusername/my-frontend-project/
├── site
│   └── *       # HTML, CSS, JavaScript
└── README.md


$ tree /home/user/Code/$GIT_USERNAME/$GIT_BACKEND_PROJECT/
/home/user/Code/gitusername/my-backend-project/
├── my-app
│   └── *       # Python
└── README.md
```

Suosimme tämän kurssin pienissä harjoitteissa `monorepo`-rakennetta.

## Backend

Kuvitellaan Pythonilla koodattu REST API -palvelin. Palvelin palauttaa tietomallin `Egg` ja `Ham` mukaisia itemeitä REST API-rajapinnan mukaisesti JSON-tiedostoina. Esimerkiksi kysely `requests.get(f"http://{domain}:{port}/egg/1")` palauttaisi `Egg`:n, jonka uniikki tunniste on `1`. Palautuva JSON voisi näyttää vaikka tältä: `{ "id":1, "name": "Kieku Vapaan Kanan Muna"}`.

Huomaa, että koodia voi myöhemmin refaktoroida ja pilkkoa pienempiin komponentteihin. Mikäli tämän tekee heti ongelmien ilmaannuttua, eikä aina ongelmien kasaantua spagettikeräksi, työ ei välttämättä ole edes valtavan suuri.

```sh
$ tree $GIT_PROJECT/backend/
/home/user/Code/gitusername/my-monorepo-project/backend/
├──app
│   └── main.py          # Käynnistää web serverin. Entry point.
├──models
│   ├── egg.py           # Määrittelee {Itemin} rakenteen.
│   └── ham.py           # Esim. Egg(id, name)
├──routes
│   ├── egg.py           # Entry point .../egg/* urleille
│   └── ham.py           # Entry point .../han/* urleille
├──db
│   ├── egg.py           # Database API
│   └── ham.py           # Noutaa datan tietokannasta
└── README.md
```

## Frontend



```sh
$ tree $GIT_PROJECT/frontend/
/home/user/Code/gitusername/my-monorepo-project/frontend/
├──site
├── static
│   ├── css
│   │   └── style.cs    # View:n tyylien määrittely
│   ├── js
│   │   ├── models
|   |   |   └── egg.js  # REST/Database API
|   |   |   └── ham.js  # Noutaa datan rajapinnasta
│   │   └── main.js
└── index.html          # Single Page App HTML-saitti
└── README.md                 
```

