---
title: SmogAPI

language_tabs:
  - shell
  - javascript

toc_footers:
  - <a href='https://github.com/tripit/slate'>Powered by Slate</a>

search: true
---

# Wstęp

> http://api.smog.info.pl

SmogAPI to REST API zawierające listę pomiarów powietrza, które powstało aby ułatwić developerom (i innym zainteresowanym) dostęp do danych o jakości powietrza w Polsce. SmogAPI agreguje dane z dostępnych źródeł i prezentuje je w formie prostego i [skalowalnego](#skalowalno) API nawet wtedy kiedy źródła danych są obciążone lub niedostępne.

<div style="padding: 5px 28px;">
  <div class="fb-like" data-href="http://docs.smog.info.pl" data-layout="button" data-action="like" data-size="large" data-show-faces="false" data-share="true"></div>
</div>

<a class="twitter-share-button"
  href="https://twitter.com/intent/tweet?text=SmogAPI:%20API%20z%20pomiarami%20jakości%20powietrza&hashtags=smog"
  data-size="large">
  <link rel="canonical" href="http://docs.smog.info.pl">
  <link rel="me" href="https://twitter.com/bartn_">
Tweet</a>

# O API

## Format odpowiedzi

SmogAPI prezentuje dane w formacie `hal+json` ponieważ ułatwia on eksplorowanie API. Więcej informacji o formacie można znaleźć tu: [Hypertext Application Language](https://en.wikipedia.org/wiki/Hypertext_Application_Language) lub w poniższej dokumentacji.

## Lista zmian

Aktualnie SmogAPI znajduje się w fazie beta. Oznacza to, że mogą jeszcze nastąpić zmiany zasugerowane przez beta testerów.

Po fazie beta nie będą wprowadzane tzw. _breaking changes_ czyli zmiany, które mogą uszkodzić istniejące integracje (usunięcie pola z obiektu, zmiana nazwy pola, etc). Natomiast mogą pojawić się nowe pola w istniejących obiektach lub nowe endpointy.

* 2017-02-05: Wyszukiwanie okolicznych stacji, dodano współrzędne geograficzne do obiektu `station`.
* 2017-02-12: Poprawione zduplikowane nagłówki. Dzięki bujal!

## Dobre praktyki

* Mimo że SmogAPI udostępniane jest bez opłat i bez limitu zapytań zalecane jest o cacheowanie danych na własnym serwerze lub urządzeniu użytkownika. Wskazania starsze niż kilka godzin prawdopodobnie już się nie zmienią.
* Jeśli nie korzystasz z API przez przeglądarkę dołącz do swojego zapytania nagłówek HTTP: [`Referer`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referer) (podaj link do twojej appki w Google Play albo App Store) lub [`User-Agent`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent). Dane posłużą do celów statystycznych.
* Jeśli korzystasz z tego API umieść informację o tym w swojej aplikacji lub na swojej stronie. Nie jest to wymagane ale to miłe. Bądź miły!

## Skalowalność

Aktualna architektura chmurowa SmogAPI pozwala na obsługę liczby zapytań zdecydowanie przekraczającej obecne zapotrzebowanie.

## Dostęp z innych domen

Istnieje możliwość dostępu do SmogAPI z aplikacji webowych w innych domenach ([CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS)). API zwraca odpowiednie nagłówki HTTP które to umożliwiają.

## HTTP vs HTTPS

Dostęp do endpointa `/stations/nearby` powinien odbywać się przy użyciu bezpiecznego protokołu HTTPS ponieważ przesyłane są dane na temat lokalizacji użytkownika. Adres HTTPS:

`https://api.smog.info.pl`

W pozostałych przypadkach można korzystać z protokołu HTTP ponieważ SmogAPI zawiera publicznie dostępne dane. Adres HTTP:

`http://api.smog.info.pl`

## Plany

* Więcej źródeł danych.
* Dostęp do prognoz przez API.
* Umożliwienie udostępniania wyników posiadaczom przydomowych urządzeń pomiarowych.

## Zgłaszanie błędów

Błędy związane z API: [https://github.com/smogapi/api/issues](https://github.com/smogapi/api/issues)

Błędy związane z dokumentacją: [https://github.com/smogapi/docs/issues](https://github.com/smogapi/docs/issues)

## Autor

Bartek Nowotarski <small>[Twitter](https://twitter.com/bartn_) [LinkedIn](https://pl.linkedin.com/in/bartlomiejnowotarski)</small>

# Stacje pomiarowe

## Obiekt `station`

```json
{
  "_links": {
    "self": {
      "href": "/cities/krakow/stations/dietla"
    },
    "city": {
      "href": "/cities/krakow"
    },
    "measurementsLatest": {
      "href": "/cities/krakow/stations/dietla/measurements"
    },
    "measurements": {
      "href": "/cities/krakow/stations/dietla/measurements/{date}",
      "templated": true
    }
  },
  "id": "dietla",
  "address": "ul. Dietla",
  "city": "Kraków",
  "latitude": 50.057447,
  "longitude": 19.946008
}
```

Obiekt `station` zawiera informacje o stacji pomiarowej. 

### Opis pól

Pole | Opis
--------- | -------
`id` | ID stacji
`address` | Adres pod którym znajduje się stacja.
`name` | Nazwa miasta
`latitude` | Szerokość geograficzna
`longitude` | Długość geograficzna
`_links.self` | Relatywny link do samego siebie
`_links.city` | Relatywny link do miasta
`_links.measurementsLatest` | Relatywny link do ostatnich pomiarów
`_links.measurements` | Relatywny link do pomiarów z danego dnia

## Lista okolicznych stacji

```shell
curl "https://api.smog.info.pl/stations/nearby?latitude=49.6502&longitude=19.2226"
```

```javascript
var axios = require('axios');

var latitude = '49.6502';
var longitude = '19.2226';

axios.get('https://api.smog.info.pl/stations/nearby?latitude='+latitude+'longitude='+longitude)
  .then(function(response) {
    console.log(response.data);
  });
```

> Przykładowa odpowiedź API:

```json
{
  "_links": {
    "self": {
      "href": "/stations/nearby?latitude=49.6502&longitude=19.2226"
    }
  },
  "_embedded": {
    "records": [
      {
        "_links": {
          "self": {
            "href": "/cities/zywiec/stations/kopernika"
          },
          "city": {
            "href": "/cities/zywiec"
          },
          "measurementsLatest": {
            "href": "/cities/zywiec/stations/kopernika/measurements"
          },
          "measurements": {
            "href": "/cities/zywiec/stations/kopernika/measurements/{date}",
            "templated": true
          }
        },
        "id": "kopernika",
        "address": "ul. Kopernika 83 a",
        "city": "Żywiec",
        "latitude": 49.671602,
        "longitude": 19.234446
      },
      {
        "_links": {
          "self": {
            "href": "/cities/bielsko-biala/stations/kossak-szczuckiej"
          },
          "city": {
            "href": "/cities/bielsko-biala"
          },
          "measurementsLatest": {
            "href": "/cities/bielsko-biala/stations/kossak-szczuckiej/measurements"
          },
          "measurements": {
            "href": "/cities/bielsko-biala/stations/kossak-szczuckiej/measurements/{date}",
            "templated": true
          }
        },
        "id": "kossak-szczuckiej",
        "address": "ul. Kossak-Szczuckiej",
        "city": "Bielsko-Biała",
        "latitude": 49.813464,
        "longitude": 19.027318
      },
      {
        "_links": {
          "self": {
            "href": "/cities/ustron/stations/sanatoryjna"
          },
          "city": {
            "href": "/cities/ustron"
          },
          "measurementsLatest": {
            "href": "/cities/ustron/stations/sanatoryjna/measurements"
          },
          "measurements": {
            "href": "/cities/ustron/stations/sanatoryjna/measurements/{date}",
            "templated": true
          }
        },
        "id": "sanatoryjna",
        "address": "ul. Sanatoryjna 7",
        "city": "Ustroń",
        "latitude": 49.719731,
        "longitude": 18.826722
      },
      {
        "_links": {
          "self": {
            "href": "/cities/cieszyn/stations/mickiewicza"
          },
          "city": {
            "href": "/cities/cieszyn"
          },
          "measurementsLatest": {
            "href": "/cities/cieszyn/stations/mickiewicza/measurements"
          },
          "measurements": {
            "href": "/cities/cieszyn/stations/mickiewicza/measurements/{date}",
            "templated": true
          }
        },
        "id": "mickiewicza",
        "address": "ul. Mickiewicza 13",
        "city": "Cieszyn",
        "latitude": 49.738136,
        "longitude": 18.639069
      },
      {
        "_links": {
          "self": {
            "href": "/cities/tychy/stations/tolstoja"
          },
          "city": {
            "href": "/cities/tychy"
          },
          "measurementsLatest": {
            "href": "/cities/tychy/stations/tolstoja/measurements"
          },
          "measurements": {
            "href": "/cities/tychy/stations/tolstoja/measurements/{date}",
            "templated": true
          }
        },
        "id": "tolstoja",
        "address": "ul. Tołstoja 1",
        "city": "Tychy",
        "latitude": 50.099903,
        "longitude": 18.990236
      },
      {
        "_links": {
          "self": {
            "href": "/cities/kaszow/stations/bory"
          },
          "city": {
            "href": "/cities/kaszow"
          },
          "measurementsLatest": {
            "href": "/cities/kaszow/stations/bory/measurements"
          },
          "measurements": {
            "href": "/cities/kaszow/stations/bory/measurements/{date}",
            "templated": true
          }
        },
        "id": "bory",
        "address": "Bory",
        "city": "Kaszów",
        "latitude": 50.025028,
        "longitude": 19.726833
      },
      {
        "_links": {
          "self": {
            "href": "/cities/skawina/stations/ogrody"
          },
          "city": {
            "href": "/cities/skawina"
          },
          "measurementsLatest": {
            "href": "/cities/skawina/stations/ogrody/measurements"
          },
          "measurements": {
            "href": "/cities/skawina/stations/ogrody/measurements/{date}",
            "templated": true
          }
        },
        "id": "ogrody",
        "address": "os. Ogrody",
        "city": "Skawina",
        "latitude": 49.971047,
        "longitude": 19.830422
      },
      {
        "_links": {
          "self": {
            "href": "/cities/zory/stations/sikorskiego"
          },
          "city": {
            "href": "/cities/zory"
          },
          "measurementsLatest": {
            "href": "/cities/zory/stations/sikorskiego/measurements"
          },
          "measurements": {
            "href": "/cities/zory/stations/sikorskiego/measurements/{date}",
            "templated": true
          }
        },
        "id": "sikorskiego",
        "address": "ul. Sikorskiego 52",
        "city": "Żory",
        "latitude": 50.029416,
        "longitude": 18.689527
      },
      {
        "_links": {
          "self": {
            "href": "/cities/trzebinia/stations/zwm"
          },
          "city": {
            "href": "/cities/trzebinia"
          },
          "measurementsLatest": {
            "href": "/cities/trzebinia/stations/zwm/measurements"
          },
          "measurements": {
            "href": "/cities/trzebinia/stations/zwm/measurements/{date}",
            "templated": true
          }
        },
        "id": "zwm",
        "address": "os. Związku Walki Młodych",
        "city": "Trzebinia",
        "latitude": 50.159406,
        "longitude": 19.477464
      },
      {
        "_links": {
          "self": {
            "href": "/cities/nowy-targ/stations/plac-slowackiego"
          },
          "city": {
            "href": "/cities/nowy-targ"
          },
          "measurementsLatest": {
            "href": "/cities/nowy-targ/stations/plac-slowackiego/measurements"
          },
          "measurements": {
            "href": "/cities/nowy-targ/stations/plac-slowackiego/measurements/{date}",
            "templated": true
          }
        },
        "id": "plac-slowackiego",
        "address": "Plac Słowackiego",
        "city": "Nowy Targ",
        "latitude": 49.483597,
        "longitude": 20.028992
      }
    ]
  }
}
```

Zapytanie do tego endpointu zwróci listę 10 najbliższych stacji od punktu określonego przez parametry `latitude` i `longitude`. Lista będzie posortowana od najbliższych stacji i będzie zawierać maksymalnie 10 stacji.

### Zapytanie

`GET https://api.smog.info.pl/stations/nearby?latitude={latitude}&longitude={longitude}`

### Parametry

Parameter | Opis
--------- | -------
`{latitude}` | Szerokość geograficzna (dla Polski pomiędzy 49 a 55)
`{longitude}` | Długość geograficzna (dla Polski pomiędzy 14 a 25)

### Odpowiedź

Pole | Opis
--------- | -------
`_links.self` | Relatywny link do samego siebie
`_links.city` | Relatywny link do miasta
`_embedded.records` | Lista obiektów [`station`](#obiekt-station).

## Lista stacji w danym mieście

```shell
curl "http://api.smog.info.pl/cities/{city}/stations"
```

```javascript
var axios = require('axios');

var cityId = 'krakow';

axios.get('http://api.smog.info.pl/cities/'+cityId+'/stations')
  .then(function(response) {
    console.log(response.data);
  });
```

> Przykładowa odpowiedź API:

```json
{
  "_links": {
    "self": {
      "href": "/cities/krakow/stations"
    },
    "city": {
      "href": "/cities/krakow"
    }
  },
  "_embedded": {
    "records": [
      {
        "_links": {
          "self": {
            "href": "/cities/krakow/stations/bujaka"
          },
          "city": {
            "href": "/cities/krakow"
          },
          "measurementsLatest": {
            "href": "/cities/krakow/stations/bujaka/measurements"
          },
          "measurements": {
            "href": "/cities/krakow/stations/bujaka/measurements/{date}",
            "templated": true
          }
        },
        "id": "bujaka",
        "address": "ul. Bujaka",
        "city": "Kraków",
        "latitude": 50.010575,
        "longitude": 19.949189
      },
      {
        "_links": {
          "self": {
            "href": "/cities/krakow/stations/bulwarowa"
          },
          "city": {
            "href": "/cities/krakow"
          },
          "measurementsLatest": {
            "href": "/cities/krakow/stations/bulwarowa/measurements"
          },
          "measurements": {
            "href": "/cities/krakow/stations/bulwarowa/measurements/{date}",
            "templated": true
          }
        },
        "id": "bulwarowa",
        "address": "ul. Bulwarowa",
        "city": "Kraków",
        "latitude": 50.069308,
        "longitude": 20.053492
      },
      {
        "_links": {
          "self": {
            "href": "/cities/krakow/stations/dietla"
          },
          "city": {
            "href": "/cities/krakow"
          },
          "measurementsLatest": {
            "href": "/cities/krakow/stations/dietla/measurements"
          },
          "measurements": {
            "href": "/cities/krakow/stations/dietla/measurements/{date}",
            "templated": true
          }
        },
        "id": "dietla",
        "address": "ul. Dietla",
        "city": "Kraków",
        "latitude": 50.057447,
        "longitude": 19.946008
      },
      {
        "_links": {
          "self": {
            "href": "/cities/krakow/stations/krasinskiego"
          },
          "city": {
            "href": "/cities/krakow"
          },
          "measurementsLatest": {
            "href": "/cities/krakow/stations/krasinskiego/measurements"
          },
          "measurements": {
            "href": "/cities/krakow/stations/krasinskiego/measurements/{date}",
            "templated": true
          }
        },
        "id": "krasinskiego",
        "address": "al. Krasińskiego",
        "city": "Kraków",
        "latitude": 50.057678,
        "longitude": 19.926189
      },
      {
        "_links": {
          "self": {
            "href": "/cities/krakow/stations/piastow"
          },
          "city": {
            "href": "/cities/krakow"
          },
          "measurementsLatest": {
            "href": "/cities/krakow/stations/piastow/measurements"
          },
          "measurements": {
            "href": "/cities/krakow/stations/piastow/measurements/{date}",
            "templated": true
          }
        },
        "id": "piastow",
        "address": "os. Piastów",
        "city": "Kraków",
        "latitude": 50.099361,
        "longitude": 20.018317
      },
      {
        "_links": {
          "self": {
            "href": "/cities/krakow/stations/telimeny"
          },
          "city": {
            "href": "/cities/krakow"
          },
          "measurementsLatest": {
            "href": "/cities/krakow/stations/telimeny/measurements"
          },
          "measurements": {
            "href": "/cities/krakow/stations/telimeny/measurements/{date}",
            "templated": true
          }
        },
        "id": "telimeny",
        "address": "ul. Telimeny 9",
        "city": "Kraków",
        "latitude": 50.0192,
        "longitude": 20.016803
      }
    ]
  }
}
```

Zapytanie do tego endpointu zwróci listę stacji pomiarowych dla danego miasta dostępnych przez SmogAPI.

### Zapytanie

`GET http://api.smog.info.pl/cities/{city}/stations`

### Parametry URL

Parameter | Opis
--------- | -------
`{city}` | ID miasta

### Odpowiedź

Pole | Opis
--------- | -------
`_links.self` | Relatywny link do samego siebie
`_links.city` | Relatywny link do miasta
`_embedded.records` | Lista obiektów [`station`](#obiekt-station)

## Pojedyncza stacja pomiarowa

```shell
curl "http://api.smog.info.pl/cities/krakow/stations/dietla"
```

```javascript
var axios = require('axios');

var cityId = 'krakow';
var stationId = 'dietla';

axios.get('http://api.smog.info.pl/cities/'+cityId+'/stations/'+stationId)
  .then(function(response) {
    console.log(response.data);
  });
```

> Przykładowa odpowiedź API:

```json
{
  "_links": {
    "self": {
      "href": "/cities/krakow/stations/dietla"
    },
    "city": {
      "href": "/cities/krakow"
    },
    "measurementsLatest": {
      "href": "/cities/krakow/stations/dietla/measurements"
    },
    "measurements": {
      "href": "/cities/krakow/stations/dietla/measurements/{date}",
      "templated": true
    }
  },
  "id": "dietla",
  "address": "ul. Dietla",
  "city": "Kraków",
  "latitude": 50.057447,
  "longitude": 19.946008
}
```

Zapytanie do tego endpointu zwróci informację o konkretnej stacji pomiarowej (obiekt [`station`](#obiekt-station)).

### Zapytanie

`GET http://api.smog.info.pl/cities/{city}/stations/{station}`

### Parametry URL

Parameter | Opis
--------- | -------
`{city}` | ID miasta
`{station}` | ID stacji pomiarowej

### Odpowiedź

Zobacz: obiekt [`station`](#obiekt-station).

# Pomiary

## Obiekt `measurement`

```json
{
  "_links": {
    "city": {
      "href": "/cities/warszawa"
    },
    "station": {
      "href": "/cities/warszawa/stations/niepodleglosci"
    },
    "source": {
      "href": "http://acme.com/ahcgahsueyha"
    }
  },
  "measuredAt": "2017-01-31T11:00:00+01:00",
  "pm10": 82.4,
  "pm25": 62.7,
  "o3": 13,
  "no2": 63.2,
  "so2": 70,
  "c6h6": 4.4,
  "co": 0.6,
  "source": {
    "name": "ACME",
    "contact": "0123456"
  }
}
```

Obiekt `measurement` zawiera informacje o pojedynczym pomiarze. 

### Opis pól

Pole | Opis
--------- | -------
`measuredAt` | Czas pomiaru w formacie RFC 3339
`pm10` | Wyniki pomiaru stężenia pyłu PM10 w µg/m<sup>3</sup>
`pm25` | Wyniki pomiaru stężenia pyłu PM2.5 w µg/m<sup>3</sup>
`o3` | Wyniki pomiaru stężenia ozonu w µg/m<sup>3</sup>
`no2` | Wyniki pomiaru stężenia dwutlenku azotu w µg/m<sup>3</sup>
`so2` | Wyniki pomiaru stężenia dwutlenku siarki w µg/m<sup>3</sup>
`c6h6` | Wyniki pomiaru stężenia benzenu w µg/m<sup>3</sup>
`co` | Wyniki pomiaru stężenia tlenku węgla w µg/m<sup>3</sup>
`source.name` | Nazwa organizacji będącej źródłem danych
`source.contact` | Dane kontaktowe organizacji będącej źródłem danych
`_links.city` | Relatywny link do miasta
`_links.station` | Relatywny link do stacji pomiarowej
`_links.source` | Absolutny link do źródła danych

<aside class="notice">
Niektóre pola z wynikami pomiarów (pola <code>pm10</code>, <code>pm25</code>, <code>o3</code>, <code>no2</code>, <code>so2</code>, <code>c6h6</code>, <code>co</code>) mogą być nieobecne jeśli dana stacja nie prowadzi tych pomiarów lub nie są one jeszcze dostępne.
</aside>

## Ostatnie pomiary z danej stacji

```shell
curl "http://api.smog.info.pl/cities/krakow/stations/dietla/measurements"
```

```javascript
var axios = require('axios');

var cityId = 'krakow';
var stationId = 'dietla';

axios.get('http://api.smog.info.pl/cities/'+cityId+'/stations/'+stationId+'/measurements')
  .then(function(response) {
    console.log(response.data);
  });
```

> Przykładowa odpowiedź API:

```json
{
  "_links": {
    "self": {
      "href": "/cities/krakow/stations/dietla/measurements"
    },
    "prev": {
      "href": "/cities/krakow/stations/dietla/measurements/{date}",
      "templated": true
    },
    "city": {
      "href": "/cities/krakow"
    },
    "station": {
      "href": "/cities/krakow/stations/dietla"
    }
  },
  "_embedded": {
    "records": [
      {
        "_links": {
          "city": {
            "href": "/cities/krakow"
          },
          "station": {
            "href": "/cities/krakow/stations/dietla"
          },
          "source": {
            "href": "http://powietrze.gios.gov.pl/pjp/current/station_details/table/10121/3/1"
          }
        },
        "measuredAt": "2017-01-31T09:00:00+01:00",
        "no2": 90.8,
        "source": {
          "name": "Główny Inspektorat Ochrony Środowiska",
          "contact": "ul. Wawelska 52/54, 00-922 Warszawa, Tel. (0 22) 36-92-208"
        }
      },
      {
        "_links": {
          "city": {
            "href": "/cities/krakow"
          },
          "station": {
            "href": "/cities/krakow/stations/dietla"
          },
          "source": {
            "href": "http://powietrze.gios.gov.pl/pjp/current/station_details/table/10121/3/1"
          }
        },
        "measuredAt": "2017-01-31T08:00:00+01:00",
        "pm10": 203.9,
        "no2": 75.6,
        "source": {
          "name": "Główny Inspektorat Ochrony Środowiska",
          "contact": "ul. Wawelska 52/54, 00-922 Warszawa, Tel. (0 22) 36-92-208"
        }
      },
      {
        "_links": {
          "city": {
            "href": "/cities/krakow"
          },
          "station": {
            "href": "/cities/krakow/stations/dietla"
          },
          "source": {
            "href": "http://powietrze.gios.gov.pl/pjp/current/station_details/table/10121/3/1"
          }
        },
        "measuredAt": "2017-01-31T07:00:00+01:00",
        "pm10": 197.9,
        "no2": 69.2,
        "source": {
          "name": "Główny Inspektorat Ochrony Środowiska",
          "contact": "ul. Wawelska 52/54, 00-922 Warszawa, Tel. (0 22) 36-92-208"
        }
      }
    ]
  }
}
```

Zapytanie do tego endpointu zwróci listę ostatnich (max. 24) pomiarów z danej stacji pomiarowej. Lista będzie posortowana po czasie pomiaru, od najnowszych.

### Zapytanie

`GET http://api.smog.info.pl/cities/{city}/stations/{station}/measurements`

### Parametry URL

Parameter | Opis
--------- | -------
`{city}` | ID miasta
`{station}` | ID stacji

### Odpowiedź

Pole | Opis
--------- | -------
`_links.self` | Relatywny link do samego siebie
`_links.prev` | Relatywny link do listy pomiarów z poprzednich dni
`_links.city` | Relatywny link do miasta
`_links.station` | Relatywny link do stacji
`_embedded.records` | Lista obiektów [`measurement`](#obiekt-measurement)

<aside class="notice">
Zauważ, że pierwszy na liście pomiarów ("Przykładowa odpowiedź API" po prawej) nie zawiera wskazania <code>pm10</code> ponieważ nie jest ono jeszcze dostępne dla tej stacji.
</aside>

## Lista pomiarów z danego dnia

```shell
curl "http://api.smog.info.pl/cities/krakow/stations/dietla/measurements/2017-01-30"
```

```javascript
var axios = require('axios');

var cityId = 'krakow';
var stationId = 'dietla';
var date = '2017-01-30';

axios.get('http://api.smog.info.pl/cities/'+cityId+'/stations/'+stationId+'/measurements/'+date)
  .then(function(response) {
    console.log(response.data);
  });
```

> Przykładowa odpowiedź API:

```json
{
  "_links": {
    "self": {
      "href": "/cities/krakow/stations/dietla/measurements/2017-01-30"
    },
    "next": {
      "href": "/cities/krakow/stations/dietla/measurements/2017-01-31"
    },
    "prev": {
      "href": "/cities/krakow/stations/dietla/measurements/2017-01-29"
    },
    "city": {
      "href": "/cities/krakow"
    },
    "station": {
      "href": "/cities/krakow/stations/dietla"
    }
  },
  "_embedded": {
    "records": [
      {
        "_links": {
          "city": {
            "href": "/cities/krakow"
          },
          "station": {
            "href": "/cities/krakow/stations/dietla"
          },
          "source": {
            "href": "http://powietrze.gios.gov.pl/pjp/current/station_details/table/10121/3/0"
          }
        },
        "measuredAt": "2017-01-30T00:00:00+01:00",
        "pm10": 313,
        "no2": 89.4,
        "source": {
          "name": "Główny Inspektorat Ochrony Środowiska",
          "contact": "ul. Wawelska 52/54, 00-922 Warszawa, Tel. (0 22) 36-92-208"
        }
      },
      {
        "_links": {
          "city": {
            "href": "/cities/krakow"
          },
          "station": {
            "href": "/cities/krakow/stations/dietla"
          },
          "source": {
            "href": "http://powietrze.gios.gov.pl/pjp/current/station_details/table/10121/3/0"
          }
        },
        "measuredAt": "2017-01-30T01:00:00+01:00",
        "pm10": 322.1,
        "no2": 79.7,
        "source": {
          "name": "Główny Inspektorat Ochrony Środowiska",
          "contact": "ul. Wawelska 52/54, 00-922 Warszawa, Tel. (0 22) 36-92-208"
        }
      },
      {
        "_links": {
          "city": {
            "href": "/cities/krakow"
          },
          "station": {
            "href": "/cities/krakow/stations/dietla"
          },
          "source": {
            "href": "http://powietrze.gios.gov.pl/pjp/current/station_details/table/10121/3/0"
          }
        },
        "measuredAt": "2017-01-30T02:00:00+01:00",
        "pm10": 335.3,
        "no2": 71.2,
        "source": {
          "name": "Główny Inspektorat Ochrony Środowiska",
          "contact": "ul. Wawelska 52/54, 00-922 Warszawa, Tel. (0 22) 36-92-208"
        }
      },
      // ...
      {
        "_links": {
          "city": {
            "href": "/cities/krakow"
          },
          "station": {
            "href": "/cities/krakow/stations/dietla"
          },
          "source": {
            "href": "http://powietrze.gios.gov.pl/pjp/current/station_details/table/10121/3/0"
          }
        },
        "measuredAt": "2017-01-30T22:00:00+01:00",
        "pm10": 352.4,
        "no2": 144.6,
        "source": {
          "name": "Główny Inspektorat Ochrony Środowiska",
          "contact": "ul. Wawelska 52/54, 00-922 Warszawa, Tel. (0 22) 36-92-208"
        }
      },
      {
        "_links": {
          "city": {
            "href": "/cities/krakow"
          },
          "station": {
            "href": "/cities/krakow/stations/dietla"
          },
          "source": {
            "href": "http://powietrze.gios.gov.pl/pjp/current/station_details/table/10121/3/0"
          }
        },
        "measuredAt": "2017-01-30T23:00:00+01:00",
        "pm10": 313.2,
        "no2": 115,
        "source": {
          "name": "Główny Inspektorat Ochrony Środowiska",
          "contact": "ul. Wawelska 52/54, 00-922 Warszawa, Tel. (0 22) 36-92-208"
        }
      }
    ]
  }
}
```

Zapytanie do tego endpointu zwróci listę wszystkich dostępnych pomiarów z danej stacji pomiarowej z danego dnia. Lista będzie posortowana po godzinie pomiaru rosnąco.

### Zapytanie

`GET http://api.smog.info.pl/cities/{city}/stations/{station}/measurements/{date}`

### Parametry URL

Parameter | Opis
--------- | -------
`{city}` | ID miasta
`{station}` | ID stacji
`{date}` | Data w formacie `YYYY-MM-DD`

### Odpowiedź

Pole | Opis
--------- | -------
`_links.self` | Relatywny link do samego siebie
`_links.next` | Relatywny link do listy pomiarów z kolejnego dnia (może jeszcze nie istnieć!)
`_links.prev` | Relatywny link do listy pomiarów z poprzedniego dnia (może nie istnieć)
`_links.city` | Relatywny link do miasta
`_links.station` | Relatywny link do stacji
`_embedded.records` | Lista obiektów [`measurement`](#obiekt-measurement)

# Miasta

## Obiekt `city`

```json
{
  "_links": {
    "self": {
      "href": "/cities/krakow"
    },
    "stations": {
      "href": "/cities/krakow/stations"
    }
  },
  "id": "krakow",
  "name": "Kraków"
}
```

Obiekt `city` zawiera informacje o pojedynczym mieście.

### Opis pól

Pole | Opis
--------- | -------
`id` | ID miasta
`name` | Nazwa miasta
`_links.self` | Relatywny link do samego siebie
`_links.stations` | Relatywny link do listy stacji pomiarowych w danym mieście

## Lista miast

```shell
curl "http://api.smog.info.pl/cities"
```

```javascript
var axios = require('axios');

axios.get('http://api.smog.info.pl/cities')
  .then(function(response) {
    console.log(response.data);
  });
```

> Przykładowa odpowiedź API:

```json
{
  "_links": {
    "self": {
      "href": "/cities"
    }
  },
  "_embedded": {
    "records": [
      {
        "_links": {
          "self": {
            "href": "/cities/augustow"
          },
          "stations": {
            "href": "/cities/augustow/stations"
          }
        },
        "id": "augustow",
        "name": "Augustów"
      },
      {
        "_links": {
          "self": {
            "href": "/cities/belsk-duzy"
          },
          "stations": {
            "href": "/cities/belsk-duzy/stations"
          }
        },
        "id": "belsk-duzy",
        "name": "Belsk Duży"
      },
      {
        "_links": {
          "self": {
            "href": "/cities/biala-podlaska"
          },
          "stations": {
            "href": "/cities/biala-podlaska/stations"
          }
        },
        "id": "biala-podlaska",
        "name": "Biała Podlaska"
      },
      // ....
    ]
  }
}
```

Zapytanie do tego endpointu zwróci listę miast dostępnych przez SmogAPI.

### Zapytanie

`GET http://api.smog.info.pl/cities`

### Odpowiedź

Pole | Opis
--------- | -------
`_links.self` | Relatywny link do samego siebie
`_embedded.records` | Lista obiektów [`city`](#obiekt-city)

## Pojedyncze miasto

```shell
curl "http://api.smog.info.pl/cities/krakow"
```

```javascript
var axios = require('axios');

var cityId = 'krakow';

axios.get('http://api.smog.info.pl/cities/'+cityId)
  .then(function(response) {
    console.log(response.data);
  });
```

> Przykładowa odpowiedź API:

```json
{
  "_links": {
    "self": {
      "href": "/cities/krakow"
    },
    "stations": {
      "href": "/cities/krakow/stations"
    }
  },
  "id": "krakow",
  "name": "Kraków"
}
```

Zapytanie do tego endpointu zwróci informację o konkretnym mieście (obiekt [`city`](#obiekt-city)).

### Zapytanie

`GET http://api.smog.info.pl/cities/{city}`

### Parametry URL

Parameter | Opis
--------- | -------
`{city}` | ID miasta

### Odpowiedź

Zobacz: obiekt [`city`](#obiekt-city).
