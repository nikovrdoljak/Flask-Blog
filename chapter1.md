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
    {% raw %}
    {% block head %}
    <title>{% block title %}{% endblock %} - Moja aplikacija</title>
    {% endblock %}
    {% endraw %}
</head>
<body>
    {% raw %}
    {% block body %}
    {% endblock %}
    {% endraw %}
</body>
</html>
```
**Objašnjenje**: Ovaj predložak definira osnovnu strukturu HTML stranice i koristi blokove (`{% raw %}{% block title %}{% endraw %} i {% raw %}{% block body %}{% endraw %}`) koje će nasljedne stranice moći popuniti vlastitim sadržajem.

Ažurirajte `index.html`
Sada ažurirajte index.html da koristi base.html kao osnovni predložak. Izmijenite index.html na sljedeći način:
```html
{% raw %}
{% extends "base.html" %}
{% block title %}Početna stranica{% endblock %}
{% block head %}
	{{ super() }}
	<style>
	</style>
{% endblock %}

{% block body %}
<h1>Dobrodošli u moju Flask aplikaciju!</h1>
{% endblock %}
{% endraw %}
```
**Objašnjenje**: Ovdje koristimo `{% raw %}{% extends "base.html" %}{% endraw %}` da naznačimo da `index.html` nasljeđuje strukturu iz `base.html`. Blokovi `title` i `body` se koriste za postavljanje specifičnog sadržaja za ovu stranicu.

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
        {% raw %}
        {% block styles %}
            {{ bootstrap.load_css() }}
        {% endblock %}
        {% endraw %}
```
te `{% raw %}{{ bootstrap.load_js() }}{% endraw %}` prije `body end` taga:
```
    {% raw %}
    {% block scripts %}
        {{ bootstrap.load_js() }}
    {% endblock %}
    {% endraw %}
```
**Objašnjenje**: Ovaj kod uključuje Bootstrap CSS iz CDN-a, omogućavajući vam da koristite Bootstrap stilove u vašem projektu.


Izmijenite i `base.html` tako da "uključimo" `<div class="container">` kao osnovni Bootstrap element u koji ćemo dodavati buduće HTML stranice ovog projekta.
```html
    <div class="container mt-5">
        {% raw %}
        {% block body %}
        {% endblock %}
        {% endraw %}
    </div>
```
**Objašnjenje**: Ovdje smo dodali Bootstrap klasu  `container` za stilizaciju glavnog dijela aplikacije.

Sada bi vaša aplikacija trebala koristiti Bootstrap stilove i komponente, čineći je vizualno privlačnijom. Ovim koracima ste uspješno dodali `bootstrap-flask` biblioteku u vašu Flask aplikaciju.

Posjetite [https://bootstrap-flask-example.azurewebsites.net/](https://bootstrap-flask-example.azurewebsites.net/) da vidite demo prikaz jedne Flask Bootstrap aplikacije koristeći sve komponente ove biblioteke.

## Rad s HTML web obrascima
Web obrasci (ili forme) su ključni elementi web stranica koji omogućuju korisnicima interakciju s aplikacijom ili web stranicom. Obrasci omogućuju unos i slanje podataka serveru, što je temelj za funkcionalnosti poput prijava, registracija, pretraživanja i mnogo više.

### Struktura Web Obrasca
Obrazac se definira pomoću HTML elementa <form>, unutar kojeg se nalaze različiti elementi za unos podataka. Najčešći elementi u obrascima su:

```<input>```: Koristi se za različite vrste unosa, poput teksta, lozinki, brojeva i još mnogo toga. Atributom type definiramo vrstu unosa, npr. type="text" za unos teksta.
```<button>```: Gumb za slanje ili poništavanje podataka. Uobičajeni type atributi za gumb su submit (za slanje podataka) i reset (za poništavanje unosa).

Primjer jednostavnog obrasca:
```html
<form action="/submit" method="post">
    <label for="name">Ime:</label>
    <input type="text" id="name" name="name">
    <button type="submit">Pošalji</button>
</form>
```

### Atributi Obrasca
* action: Definira URL na koji će se podaci slati. Na primjer, action="/submit" šalje podatke na /submit rutu.
* method: Određuje način slanja podataka. Najčešće korištene metode su:
    * GET: Podaci se šalju putem URL-a kao dio upitnog niza (query string). Koristi se za jednostavne zahtjeve poput pretraživanja.
    * POST: Podaci se šalju u tijelu zahtjeva i ne prikazuju se u URL-u. Koristi se za osjetljive ili veće podatke, poput prijava i registracija.
#### Upitni Niz (Query String)
Kod GET metoda, podaci se dodaju u URL kao upitni niz. Primjerice, ako obrazac pošalje podatke s unosom name=Ana, URL bi mogao izgledati ovako:
http://localhost:5000/?name=Ana

Ova vrsta prijenosa korisna je jer omogućava lako dohvaćanje parametara s URL-a te ponavljanje i dijeljenje URL-a s podacima.

Osnovne Prakse Kodiranja
* **Provjera podataka**: Iako se podaci mogu provjeriti na strani klijenta (JavaScript), ključna provjera mora biti provedena na strani servera.
* **Prijateljski izgled**: Koristite HTML label element za označavanje unosa, čineći obrazac pristupačnijim i preglednijim.
* **Sigurnost**: Kod korištenja POST metode, podatke je bolje slati putem HTTPS protokola za zaštitu osjetljivih informacija.
Web obrasci čine aplikaciju dinamičnom i interaktivnom, pružajući jednostavan način za komunikaciju između korisnika i aplikacije. U sljedećim poglavljima istražit ćemo kako rukovati podacima iz obrazaca u Flask aplikaciji, spremiti ih u bazu podataka i dodatno proširiti funkcionalnost.

#### Implementacija jednostavnog web obrasca:
Dodajmo obrazac u index.html:
Primjer jednostavnog obrasca:
```html
<form>
    <label for="name">Ime:</label>
    <input type="text" id="name" name="name">
    <button type="submit">Pošalji</button>
</form>
```
U app.py ćemo dohvatiti vrijednost name iz upitnog niza pomoću request.args.get.

```python
from flask import Flask, render_template, request
from flask_bootstrap import Bootstrap5

app = Flask(__name__)
bootstrap = Bootstrap5(app)

@app.route("/")
def home():
    name = request.args.get("name", "")  # Preuzimamo "name" iz upitnog niza, ili prazan string ako nije postavljeno
    return render_template("index.html", name = name) 
```

Dodatno, iznad forme dodajmo sljedeći kod:
```
{% raw %}
{% if name %}
	<h1>Hello, {{ name }}!</h1>
{% else %}
	<h1>Hello, Stranger!</h1>
{% endif %}
{% endblock %}
{% endraw %}
```
**Objašnjenje**
* Obrazac: U index.html koristimo form element s method="get" (što je zadano) kako bi se podaci poslali putem upitnog niza.
* Dohvat podataka: U app.py, funkcija index() koristi request.args.get("name") za dohvat unesenog naziva iz upitnog niza.
* Uvjetni prikaz u predlošku: Ako name nije unesen, prikazat će se tekst "Hello, Stranger", a ako jest, prikazat će se pozdrav s imenom.
**Rezultat**
Prilikom otvaranja stranice bez unesenog naziva prikazat će se "Hello, Stranger". Nakon unosa imena i klika na "Pošalji", stranica će se ponovno učitati i prikazati pozdrav s unesenim imenom, npr. "Hello, Ana".

#### Dodajmo i Bootstrap CSS klase u formu:
```html
    <form>
        <div class="mb-3">
            <label for="name" class="form-label">Ime</label>
            <input type="text" class="form-control" id="name" name="name">
        </div>
        <button type="submit" class="btn btn-primary">Pošalji</button>
    </form>
```
Objašnjenje Bootstrap klasa
* ```container```: Ova klasa postavlja glavni sadržaj u centralizirani i responzivni okvir na stranici. container se koristi za stvaranje ograničene širine sadržaja koja automatski odgovara veličini ekrana, što doprinosi urednijem i preglednijem izgledu stranice.
* ```mt-5```: Ova klasa dodaje "margin-top" (gornji razmak) od 5 jedinica prema Bootstrap skali (jedinica obično iznosi 0,25rem). mt-5 pomaže odmaknuti sadržaj od vrha stranice, čineći ga preglednijim i ugodnijim za čitanje.
* ```mb-3```: Dodaje "margin-bottom" (donji razmak) od 3 jedinice ispod pojedinog elementa unutar obrasca. Ovdje ga koristimo za odvajanje input polja od gumba, čime unos postaje jasniji i pregledniji.
* ```form-label```: Ova klasa se primjenjuje na <label> elemente u obrazcima kako bi se osigurao dosljedan stil oznaka, prilagođen izgled obrazaca i čitljivost na svim uređajima.
* ```form-control```: Jedna od najvažnijih Bootstrap klasa za unos. form-control dodaje stil input poljima i proširuje ih na punu širinu njihovog roditeljskog elementa. Također omogućava prilagodljivost unosa, što znači da će unos izgledati uredno i biti lako dostupan na mobilnim uređajima.
* ```btn i btn-primary```: btn je osnovna klasa za sve gumbe u Bootstrapu, dok btn-primary dodaje stil primarnog gumba, koji obično dolazi s plavom pozadinom i bijelim tekstom (boje se mogu prilagoditi). Ovaj stil naglašava gumb za slanje, čineći ga prepoznatljivim i lako vidljivim korisnicima.

Kombinacija ovih klasa osigurava responzivnost, preglednost i funkcionalnost obrasca na svim uređajima. S ovim osnovnim Bootstrap klasama, možemo postaviti profesionalno stilizirane oblike koji će automatski prilagoditi svoj izgled veličini ekrana, pružajući bolje korisničko iskustvo.

#### Prilagodba rute za GET i POST metode
Dodajmo logiku koja omogućuje našoj index() ruti prihvaćanje obje metode, GET i POST. Ovisno o metodi zahtjeva, podaci će se dohvaćati iz request.args za GET ili request.form za POST.

Primijenimo ```method="post"``` u form elementu kako bismo poslali podatke putem POST metode: ```<form method="post">```

Prilagodba app.py da prihvati GET i POST metode
U app.py, prilagodit ćemo rutu kako bi prihvatila i GET i POST metode te obrađivala podatke na temelju metode koja je korištena.
```python
@app.route("/")
def home():
    name = ""
    if request.method == "POST":
        # Ako je POST, podatke dobijemo iz request.form
        name = request.form.get("name", "")
    else:
        # Ako je GET, podatke dobijemo iz request.args
        name = request.args.get("name", "")    
    return render_template("index.html", name = name) 
```

**Objašnjenje koda**
* ```methods=["GET", "POST"]```: Dodavanjem ovog argumenta ruti omogućujemo da index() funkcija odgovara na oba zahtjeva, GET i POST.
* Provjera metode zahtjeva: Koristimo request.method za provjeru metode zahtjeva:
    * Ako je metoda POST, dohvaćamo vrijednost name iz request.form, što znači da dolazi iz tijela zahtjeva.
    * Ako je metoda GET, dohvaćamo name iz request.args, što znači da dolazi iz upitnog niza.

#### Problem: "Confirm Form Resubmission"
Kada korisnik pošalje obrazac s POST metodom i osvježi stranicu, može se pojaviti upozorenje "Confirm Form Resubmission" (Potvrdi ponovno slanje obrasca). Ovo se događa jer preglednik prepoznaje POST zahtjev i traži potvrdu za ponovno slanje kako bi spriječio slučajno dupliciranje podataka.
*Napomena*: Ovaj ćemo problem riješiti kasnije koristeći tehniku "Post/Redirect/Get" (PRG) koja sprječava ponovno slanje podataka prilikom osvježavanja stranice. Trenutno ćemo ostaviti rutu ovakvom, kako prikazali problem, a zatim ćemo ga kasnije otkloniti.

## WTForms
**WTForms** je Python biblioteka koja omogućava lako kreiranje i obradu web obrazaca u aplikacijama. U Flask okruženju često se koristi proširenje Flask-WTF, koje integrira WTForms sa Flask aplikacijama i dodaje korisne značajke kao što su validacija unosa, CSRF zaštita i jednostavnija obrada podataka. Flask-WTF olakšava rad s obrascima jer omogućava razvijateljima da definiraju i validiraju obrasce unutar Python koda, bez potrebe za pisanjem HTML i JavaScript koda za svaki obrazac. Na taj način se smanjuje složenost i poboljšava sigurnost aplikacije.

Jedan od glavnih razloga za korištenje WTForms je validacija podataka. Validacija osigurava da korisnik unosi točne i sigurne podatke u aplikaciju, a WTForms omogućava različite ugrađene validatore, kao što su provjera duljine unosa, formatiranja e-maila ili raspona brojeva. Također, omogućava korištenje prilagođenih validatora za specifične zahtjeve aplikacije. WTForms automatski prikazuje greške korisnicima ako podaci nisu ispravni, čime olakšava i poboljšava korisničko iskustvo.


#### Što je Flask-WTF i kako ga instalirati
Flask-WTF je proširenje za Flask koje olakšava rad s web obrascima. Omogućava nam korištenje različitih validacija, generira CSRF (Cross-Site Request Forgery) token za sigurnost i olakšava obradu podataka unutar ruta.

Za instalaciju Flask-WTF koristimo pip naredbu:
```
pip install flask-wtf
```

#### Kreiranje NameForm klase s validatorima
Sada ćemo kreirati klasu forme NameForm koja će imati jedno polje, name, koje traži unos korisnika. Koristit ćemo DataRequired validator da osiguramo da je polje obavezno.
```python
from flask_wtf import FlaskForm
from wtforms import StringField, SubmitField
from wtforms.validators import DataRequired

class NameForm(FlaskForm):
    name = StringField("Ime", validators=[DataRequired()])
    submit = SubmitField("Pošalji")
```
**Objašnjenje koda**
* **NameForm**: Ovo je klasa forme koja nasljeđuje FlaskForm iz Flask-WTF. Ova klasa koristi WTForms za definiranje i validaciju podataka.
* **name**: Definirali smo polje name kao StringField, koje očekuje tekstualni unos.
* **validators=[DataRequired()]**: Koristimo validator DataRequired, koji osigurava da polje nije prazno. Ako korisnik ne unese podatak, prikazuje se greška.
* **submit**: Ovo je gumb za slanje forme, definiran kao SubmitField.


U WTForms-u možemo koristiti razna polja obrazaca za rukovanje korisničkim unosom. Evo popisa često korištenih polja:

1. **StringField** - **String polje**
2. **TextAreaField** - **Tekstualno područje**
3. **BooleanField** - **Boolovsko polje**
4. **PasswordField** - **Polje lozinke**
5. **HiddenField** - **Skriveno polje**
6. **IntegerField** - **Cjelobrojno polje**
7. **FloatField** - **Float polje**
8. **DecimalField** - **Decimalno polje**
9. **SelectField** - **Polje izbora**
10. **SelectMultipleField** - **Polje višestrukog izbora**
11. **FileField** - **Polje za datoteke**
12. **SubmitField** - **Polje za slanje**
13. **DateField** - **Polje za datum**
14. **DateTimeField** - **Polje za datum i vrijeme**
15. **TimeField** - **Polje za vrijeme**
16. **EmailField** - **Polje za e-poštu**
17. **URLField** - **Polje za URL**
18. **SearchField** - **Polje za pretraživanje**
19. **ColorField** - **Polje za boje**

**Objašnjenje validatora**
Validatori u WTForms provjeravaju vrijednosti koje korisnici unesu u polja. Ako unos ne odgovara pravilima, prikazuje se poruka o grešci.

Lista i objašnjenje validatora u WTForms:
* DataRequired: Provjerava je li polje ispunjeno. Obavezno polje.
* Email: Provjerava je li uneseni podatak ispravna email adresa.
* Length: Osigurava da je duljina unosa između minimalne i maksimalne vrijednosti.
* NumberRange: Provjerava je li broj unutar određenog raspona.
* Optional: Omogućava da polje ostane prazno i preskače validaciju ako je prazno.
* Regexp: Provjerava unos prema regularnom izrazu.
* URL: Provjerava je li unos valjani URL.
* AnyOf i NoneOf: Provjeravaju sadrži li polje određene vrijednosti ili izbjegava li određene vrijednosti.

**Prilagodba obrasca u index.html i koda u ruti**
U index.html sada ćemo koristiti Flask-WTF za prikaz forme s prilagođenim poljem name i gumbom submit.
```html
    <form method="post">
        <div class="mb-3">
            {{ form.name.label(class="form-label") }}
            {{ form.name(class="form-control") }}
        </div>
        {{ form.submit(class="btn btn-primary") }}
    </form>
```
U ruti index() sada ćemo koristiti NameForm:
```python
@app.route("/", methods=["GET", "POST"])
def index():
    form = NameForm()
    name = form.name.data if request.method == "POST" else "Stranger"
    
    return render_template("index.html", pozdrav=pozdrav, form=form)
```
Ako sad pokrenemo aplikaciju dobit ćemo grešku *"RuntimeError: A secret key is required to use CSRF."* Razlog za to jest da Flask-WTF automatski uključuje CSRF zaštitu.
Kako bismo omogućili CSRF zaštitu, Flask aplikacija treba imati SECRET_KEY. Dodajmo ga u kod:
```python
app = Flask(__name__)
app.secret_key = 'tajni_ključ'  # Ovo postavljamo kao tajni ključ za sigurnost
```
U index.html, dodajmo i ```form.hidden_tag()```. 
```html
<form method="post">
    {{ form.hidden_tag() }}

```
Ova metoda automatski dodaje skriveni **CSRF token** u formu. CSRF token je važan jer štiti našu aplikaciju od napada poznatih kao Cross-Site Request Forgery. Ovaj napad koristi se kako bi neovlašteno izvršio zahtjev kao da dolazi od 
ovlaštenog korisnika. Token omogućava aplikaciji da razlikuje legitimne zahtjeve od onih koji bi mogli biti zlonamjerni.
Ako pokrenete aplikaciju i pogledate izvorni HTML kod, primjetit ćete token:
```html
<input id="csrf_token" name="csrf_token" type="hidden" value="Ijc0ZWVkOTg3YjkwOWVmMjIzZjA5N2UzYmViYTVjM2QxYTU5MDhkYTMi.ZyN4dQ.OeumUug9Knq2TfqCbSDsDbEeUJ4">
```
Ako pokušate poslati obrazac bez da ste upisali ime, na klijentskoj strani ćete dobiti poruku *Please fill in this field.* što znači da sad naša validacija obrasca funkcionira.
No bez obzira na ovo, još uvijek nismo uključili validaciju na poslužiteljskom kodu. Stoga dodajmo još jednu promijenu:
```python
    name = form.name.data if form.validate_on_submit() else "" 
```
Ukoliko pokušamo promijeniti CSRF token, forma neće biti ispravna u ovom slučaju.

**bootstrap-flask i render_form()**
Koristeći ```render_form(form)``` iz knjižnice bootstrap-flask, možemo neke stvari pojednostavniti i stvoriti dobro stiliziranu formu u aplikaciji koja se pridržava dizajnerskih principa Bootstrapa. Ova funkcija pojednostavljuje proces renderiranja, osiguravajući da su sva polja pravilno formatirana i da se poruke o pogreškama ispravno prikazuju, poboljšavajući ukupno korisničko iskustvo.
Stoga u index.html zamijenimo cijeli ```<form>...</form>``` blok s:
```
{{ raw }}
{% from 'bootstrap5/form.html' import render_form %}
{{ render_form(form) }}
{{ endraw }}
```
Ako osvježimo stranicu, vidjet ćemo da je rezultat isti, no ovo nam uvelike može pomoći kod većih obrazaca.

**Primjeri**
Istražite sljedeću stranicu i pogledajte što sve možete raditi s WTF i Boostrap-flask bibliotekom u vašim projektima:
[https://bootstrap-flask-example.azurewebsites.net/form](https://bootstrap-flask-example.azurewebsites.net/form)