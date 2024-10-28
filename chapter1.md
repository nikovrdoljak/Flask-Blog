<link rel="stylesheet" href="assets/css/custom.css">

# Poglavlje 1: Uvod u Flask i rad s web formama

## Hello world aplikacija
Prvo ćemo pokrenuti novi Git repozitorij kako bismo mogli pratiti promjene u našem projektu. Pokrenite sljedeću naredbu u PowerShellu: 

`git init`

**Objašnjenje**: Ova naredba stvara prazni Git repozitorij u trenutnom direktoriju, omogućavajući vam verzioniranje koda. 

Za izolaciju paketa specifičnih za projekt koristit ćemo Python virtualno okruženje. 

`python -m venv venv`

**Objašnjenje**: Ova naredba stvara direktorij nazvan `venv` koji sadrži Python okruženje samo za ovaj projekt, čuvajući ga od paketa koji su možda instalirani u vašem glavnom Python okruženju. 

Nakon što smo stvorili virtualno okruženje, moramo ga aktivirati. U PowerShellu koristite: 

`.\venv\Scripts\Activate.ps1`

**Objašnjenje**: Aktivacijom virtualnog okruženja osiguravamo da su svi paketi koje instaliramo specifični za ovaj projekt. 

Da bismo izbjegli praćenje neželjenih datoteka u Git repozitoriju, kreirat ćemo `.gitignore` datoteku i dodati direktorij virtualnog okruženja. Kreirajte datoteku `.gitignore` i dodajte sljedeći sadržaj: 

```
venv/ 
pycache/ 
.vscode
*.pyc
```
**Objašnjenje**: `.gitignore` sprečava da se navedene datoteke i direktoriji dodaju u vaš Git repozitorij, čuvajući repozitorij čistim od privremenih datoteka. 

Sada možemo instalirati Flask u virtualno okruženje. Pokrenite: 

`pip install flask`

**Objašnjenje**: Ova naredba instalira Flask u virtualno okruženje, omogućavajući nam razvoj web aplikacija koristeći ovaj mikro-okvir. 

Sada ćemo kreirati našu prvu Flask aplikaciju koja prikazuje poruku "Hello, World!". Kreirajte datoteku pod nazivom `app.py` i dodajte sljedeći kod: 

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello_world():
    return "Hello, World!"
```

**Objašnjenje**: Ovaj kod postavlja jednostavnu Flask aplikaciju s jednim putem (/) koji prikazuje poruku "Hello, World!".

Za pokretanje aplikacije koristite sljedeću naredbu:
```
flask run
```
Objašnjenje: flask run pokreće ugrađeni Flask poslužitelj, omogućavajući nam pristup aplikaciji u pregledniku na adresi http://127.0.0.1:5000/.

S ovim koracima vaša prva Flask aplikacija je spremna i pokrenuta!

Krenimo dalje. Pokreite sljedeću naredbu:
`pip freeze > requirements.txt`

**Objašnjenje**: Ova naredba generira datoteku requirements.txt koja sadrži popis svih Python paketa instaliranih u trenutnom virtualnom okruženju, zajedno s njihovim verzijama. Ova datoteka je korisna za dijeljenje ovisnosti projekta s drugima ili za reprodukciju istog okruženja na drugom računalu. Kada netko želi instalirati iste pakete, može jednostavno pokrenuti `pip install -r requirements.txt`, što će instalirati sve pakete navedene u datoteci.

## Predlošci
Flask omogućava korištenje HTML predložaka za dinamičko generiranje sadržaja. Predlošci se obično pohranjuju u direktoriju nazvanom `templates`. Evo koraka za korištenje predložaka u vašoj Flask aplikaciji.

Prvo, u glavnom direktoriju vaše Flask aplikacije, kreirajte direktorij pod nazivom `templates`.

U direktoriju templates, kreirajte datoteku nazvanu index.html i dodajte HTML sadržaj. Na primjer:
```html
<!DOCTYPE html>
<html lang="hr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Moja Flask Aplikacija</title>
</head>
<body>
    <h1>Dobrodošli u moju Flask aplikaciju!</h1>
</body>
</html>
```
**Objašnjenje**: Ova datoteka sadrži osnovni HTML koji prikazuje poruku dobrodošlice.

Ažurirajte app.py za korištenje predložaka:
U vašoj app.py datoteci, ažurirajte funkciju `index()` da koristi `render_template` za prikaz index.html:
from flask import Flask, render_template
```python
app = Flask(__name__)

@app.route("/")
def index():
    return render_template("index.html")
```
**Objašnjenje**: Ovdje smo uvezli `render_template` iz Flask biblioteke, koja vraća HTML predložak index.html.
Ponovo pokrenite aplikaciju koristeći `flask run`.
Sada, kada posjetite http://127.0.0.1:5000/, trebali biste vidjeti sadržaj iz `index.html`, a ne samo poruku "Hello, World!".
Ovim koracima ste uspješno uveli predloške u svoju Flask aplikaciju i koristili index.html za generiranje dinamičkog sadržaja.

## Korištenje `base.html` kao osnovnog predloška

Korištenje osnovnog predloška omogućava vam da dijelite zajednički sadržaj, kao što su zaglavlja, podnožja ili CSS stilovi, između više stranica. Evo kako to možete implementirati u vašoj Flask aplikaciji.

U direktoriju `templates`, kreirajte datoteku pod nazivom `base.html` i dodajte sljedeći sadržaj:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    \{% block head %}
    <title>\{% block title %}\{% endblock %} - Moja aplikacija</title>
    \{% endblock %}
</head>
<body>
    \{% block body %}
    \{% endblock %}
</body>
</html>
```
**Objašnjenje**: Ovaj predložak definira osnovnu strukturu HTML stranice i koristi blokove (`\{% block title %} i \{% block body %}`) koje će nasljedne stranice moći popuniti vlastitim sadržajem.

Ažurirajte `index.html`
Sada ažurirajte index.html da koristi base.html kao osnovni predložak. Izmijenite index.html na sljedeći način:
```html
\{% extends "base.html" %}

\{% block title %}Početna stranica\{% endblock %}
\{% block head %}
	\{{ super() }}
	<style>
	</style>
\{% endblock %}

\{% block body %}
<h1>Dobrodošli u moju Flask aplikaciju!</h1>
\{% endblock %}
```
**Objašnjenje**: Ovdje koristimo `\{% extends "base.html" %}` da naznačimo da `index.html` nasljeđuje strukturu iz `base.html`. Blokovi `title` i `body` se koriste za postavljanje specifičnog sadržaja za ovu stranicu.

Ponovo pokrenite aplikaciju koristeći `flask run`.
Kada posjetite http://127.0.0.1:5000/, trebali biste vidjeti sadržaj iz `index.html`, koji koristi osnovni predložak `base.html`. Ovim koracima ste uspješno implementirali osnovni predložak u svoju Flask aplikaciju, omogućavajući bolju organizaciju i ponovno korištenje koda.

## Dodavanje "bootstrap-flask" biblioteke

`bootstrap-flask` je biblioteka koja omogućava jednostavnu integraciju Bootstrap-a s vašom Flask aplikacijom. Sljedeći koraci će vam pokazati kako dodati ovu biblioteku u vašu aplikaciju.

Prvo, instalirajte `bootstrap-flask` koristeći pip. Pokrenite sljedeću naredbu u aktiviranom virtualnom okruženju:

```plaintext
pip install bootstrap-flask
```
Objašnjenje: Ova naredba instalira bootstrap-flask biblioteku koja omogućava korištenje Bootstrap stilova i komponenti u vašoj Flask aplikaciji. Detalji se mogu pronaći na https://bootstrap-flask.readthedocs.io/


Nakon instalacije, trebate ažurirati svoju app.py datoteku da uključite `bootstrap-flask`. Uvezite Bootstrap i inicijalizirajte ga sa svojom Flask aplikacijom:
```python
from flask import Flask, render_template
from flask_bootstrap import Bootstrap5

app = Flask(__name__)
bootstrap = Bootstrap5(app)

@app.route("/")
def home():
    return render_template("index.html")
```

**Objašnjenje**: Ovdje uvozimo Bootstrap iz flask_bootstrap i inicijaliziramo ga s našom aplikacijom. To omogućava korištenje Bootstrap komponenti u našim HTML predlošcima.

Sada ažurirajte vaš base.html predložak da uključuje Bootstrap CSS. Dodajte sljedeći kod u <head> dio datoteke:
```
        \{% block styles %}
            \{{ bootstrap.load_css() }}
        \{% endblock %}
```
te `\{{ bootstrap.load_js() }}` prije `body end` taga:
```
    \{% block scripts %}
        \{{ bootstrap.load_js() }}
    \{% endblock %}
```
**Objašnjenje**: Ovaj kod uključuje Bootstrap CSS iz CDN-a, omogućavajući vam da koristite Bootstrap stilove u vašem projektu.


Izmijenite i base.html tako 
* add container div and nav bar
```html
    <div class="container">
        \{% block body %}
        \{% endblock %}
    </div>
```
**Objašnjenje**: Ovdje smo dodali Bootstrap klase za stilizaciju zaglavlja i glavnog dijela aplikacije, koristeći `container` Bootstrap CSS klasu za pravilno oblikovanje glavnog sadržaja.

Sada bi vaša aplikacija trebala koristiti Bootstrap stilove i komponente, čineći je vizualno privlačnijom. Ovim koracima ste uspješno dodali `bootstrap-flask` biblioteku u vašu Flask aplikaciju.

Posjetite https://bootstrap-flask-example.azurewebsites.net/ da vidite demo prikaz jedne Flask Bootstrap aplikacije koristeći sve komponente ove biblioteke.


