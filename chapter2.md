<link rel="stylesheet" href="assets/css/custom.css">

# Uvod u baze podataka i njihovu primjenu u aplikacijama
U ovom poglavlju specificirat ćemo zahtjeve naše aplikacije za upravljanje blog postovima, te započeti s njenom implementacijom. No prije toga ćemo ukratko predstaviti koncept baze podataka, koju ćemo koristiti za spremanje postova i ostalih entiteta naše aplikacije. 

Baze podataka su ključne za pohranjivanje podataka koje aplikacija koristi i obrađuje, poput korisničkih informacija, sadržaja i drugih vrsta podataka. One omogućuju trajno spremanje podataka, pristup njima, pretragu, ažuriranje i brisanje na strukturiran način. Umjesto pohranjivanja podataka u privremenu memoriju ili datoteke, baze podataka omogućuju rad s velikim količinama podataka na učinkovit i siguran način, čuvajući integritet podataka.

U aplikacijama poput bloga (koju ćemo mi raditi), baza podataka omogućuje spremanje članaka, komentara, korisničkih informacija i drugih elemenata potrebnih za funkcionalnost bloga. Korištenjem baze podataka, možemo jednostavno dohvatiti, filtrirati i prikazati postove.

## MongoDB – NoSQL baza podataka
**MongoDB** je NoSQL baza podataka, što znači da podaci nisu pohranjeni u tabličnom obliku kao u klasičnim relacijskim bazama podataka (npr. MySQL). Umjesto toga, MongoDB koristi JSON-slične dokumente (u BSON formatu) za pohranu podataka, što omogućava fleksibilnije modeliranje podataka. Ovaj pristup je prikladan za aplikacije u kojima se podaci često mijenjaju ili imaju različite strukture.

MongoDB je popularan zbog:
* Fleksibilnosti – Dokumenti u bazi mogu imati različite strukture.
* Skalabilnosti – Podržava horizontalno skaliranje.
* Jednostavne integracije – MongoDB se lako integrira s različitim programskim jezicima, uključujući Python, što ga čini idealnim za Flask aplikacije.
U našoj blog aplikaciji, svaki blog post možemo pohraniti kao dokument s ključevima kao što su naslov, sadržaj, autor i datum.

## Instalacija MongoDB-a na lokalni uređaj
Da biste koristili MongoDB lokalno, slijedite ove korake:

**Preuzimanje**: Idite na službenu MongoDB stranicu za preuzimanje i preuzmite MongoDB Community Server za vaš operativni sustav:
* https://www.mongodb.com/try/download/community (MongoDB Community Server - s njim ide i Compass)
* Opcionalno:
    * https://www.mongodb.com/try/download/compass (MongoDB Compass)
    * https://www.mongodb.com/try/download/shell (MongoDB Shell)


### Instalacija:

* **Windows**: Pokrenite instalacijski program (.msi datoteku) i slijedite upute. Preporučuje se uključivanje opcije Install MongoDB as a Service kako bi MongoDB bio automatski pokrenut prilikom podizanja sustava.
* **macOS**: Koristite Homebrew za instalaciju:
```bash
brew tap mongodb/brew
brew install mongodb-community
```
* **Linux**: Instalacija varira ovisno o distribuciji, ali obično uključuje dodavanje MongoDB repozitorija, ažuriranje popisa paketa, i instaliranje pomoću paketa mongodb-org.

### Pokretanje MongoDB-a:
* Na Windowsu, MongoDB servis će automatski započeti (ako ste ga uključili prilikom instalacije).
* Na macOS i Linux sustavima možete pokrenuti MongoDB naredbom:
```bash
mongod --dbpath /putanja/do/vašeg/db/foldera
```
* Preporučljivo je stvoriti poseban direktorij gdje će MongoDB pohranjivati podatke.
* Povezivanje s MongoDB-om: Nakon instalacije, MongoDB će raditi na localhostu, portu 27017. Koristit ćemo ga našoj u Flask aplikaciji pomoću Pythona i biblioteke kao što je pymongo.

U sljedećem koraku, integrirat ćemo MongoDB s Flask aplikacijom za spremanje i dohvaćanje blog postova.

## Specifikacija Blog aplikacije

Aplikacija će koristiti Flask, Bootstrap-Flask za stilizaciju, Flask-WTF za obradu formi i MongoDB kao bazu podataka za pohranu podataka o postovima. U ovom poglavlju naš cilj je stvoriti jednostavnu, ali funkcionalnu aplikaciju koja omogućava korisnicima kreiranje, uređivanje, brisanje i pregled blog postova.

### Glavne funkcionalnosti aplikacije:
* **Lista postova**: Korisnici će moći pregledavati sve blog postove na jednoj stranici. Svaki post će sadržavati naslov, sažetak i osnovne informacije poput autora i datuma.
* **Pregled pojedinačnih postova**: Klikom na post, korisnici će biti preusmjereni na stranicu koja prikazuje puni sadržaj postа, uključujući slike i tagove.
* **Kreiranje postova**: Aplikacija će omogućiti autorima da kreiraju nove postove putem forme. Ova forma će uključivati potrebna polja kao što su naslov, sadržaj, autor, datum, tagove i slika.
* **Uređivanje postova**: Autori će moći uređivati postojeće postove. To će uključivati ponovno prikazivanje forme s prethodno unesenim podacima, omogućujući autorima da izvrše izmjene.
* **Brisanje postova**: Korisnici će moći brisati postove, čime će se trajno ukloniti podaci iz baze.

### Struktura modela podataka
Svaki blog post će imati sljedeće atribute:
* Naslov (title): Kratak opis postа koji će biti prikazan na listi postova.
* Sadržaj (content): Glavni tekst postа.
* Autor (author): Ime osobe koja je napisala post.
* Datum (date): Datum kada je post kreiran.
* Tagovi (tags): Kategorije ili ključne riječi povezane s postom, omogućujući korisnicima lakše pretraživanje.
* Slika (image): veza prema slici koja će biti prikazana u postu.

## Implementacija Blog aplikacije

Da bismo započeli s implementacijom aplikacije za upravljanje blog postovima, prvo ćemo kreirati klasu BlogPostForm koja će koristiti Flask-WTF za obradu formi, zatim ćemo implementirati rutu za kreiranje novog posta i pohraniti post u MongoDB. Evo koraka koje trebate slijediti:

### Instalacija pymongo
Prije nego što počnemo raditi s MongoDB-om u našoj Flask aplikaciji, trebamo instalirati biblioteku pymongo, koja omogućava povezivanje Flask aplikacije s MongoDB-om. Za instalaciju pokrenite naredbu u terminalu:
```bash
pip install pymongo
```
Ova biblioteka omogućit će nam rad s kolekcijama i dokumentima unutar MongoDB baze podataka.

### Postavljanje koda za spajanje na bazu podataka
Dodajmo slijedeći kod na vrh aplikacije ispod intsaciranje ```app``` objekta:
```python
client = MongoClient('mongodb://localhost:27017/')
db = client['pzw_blog_database']
posts_collection = db['posts']
```
Pojašnjenje svake linije koda:

```python
client = MongoClient('mongodb://localhost:27017/')
```

Ova linija inicijalizira MongoDB klijenta koristeći ```MongoClient``` iz ```pymongo``` biblioteke. MongoClient objekt se povezuje s MongoDB instancom koja radi na ```localhost``` (vaše lokalno računalo) i sluša na standardnom MongoDB portu ```27017```. Ova adresa (```mongodb://localhost:27017/```) je standardna adresa za lokalni pristup MongoDB-u, koja omogućuje aplikaciji povezivanje s bazom podataka.

```python
db = client['pzw_blog_database']
```

Ova linija pristupa bazi podataka unutar MongoDB-a s imenom ```pzw_blog_database```. Ako baza podataka s ovim imenom ne postoji, MongoDB će je automatski kreirati kada dodate podatke u nju. Objekt ```db``` omogućava aplikaciji rad s bazom podataka, uključujući stvaranje kolekcija (poput tablica u SQL bazama podataka) i obavljanje upita.

```python
posts_collection = db['posts']
```

Ovdje definiramo kolekciju unutar baze podataka nazvanu ```posts```. Kolekcija je slična tablici u SQL bazama podataka i koristi se za pohranu više dokumenata (redaka) s podacima o blog postovima. MongoDB koristi JSON-sličan format pod nazivom BSON (Binary JSON) za pohranu podataka, što ga čini prikladnim za pohranu nestrukturiranih podataka poput blog postova.

Dakle, ovaj kod omogućuje aplikaciji da:
* Pristupi MongoDB serveru na lokalnom računalu.
* Radi s bazom podataka ```pzw_blog_database```.
* Pristupi kolekciji ```posts```, gdje će se pohranjivati i preuzimati podaci o postovima bloga.


### Kreiranje BlogPostForm klase

Prvo, definirat ćemo formu koja će sadržavati potrebna polja za blog post. Klasa će izgledati ovako:

```python
from wtforms import StringField, TextAreaField, DateField, FileField
from wtforms.validators import DataRequired, Length
from flask_wtf.file import FileAllowed
from datetime import datetime, timezone

class BlogPostForm(FlaskForm):
    title = StringField('Naslov', validators=[DataRequired(), Length(min=5, max=100)])
    content = TextAreaField('Sadržaj')
    author = StringField('Autor', validators=[DataRequired()])
    date = DateField('Datum', default=datetime.today)
    status = RadioField('Status', choices=[('draft', 'Skica'), ('published', 'Objavljeno')], default='draft')
    tags = StringField('Oznake')
    image = FileField('Blog Image', validators=[FileAllowed(['jpg', 'png', 'jpeg'], 'Samo slike!')])
    submit = SubmitField('Spremi')
```


### Ruta za kreiranje novog posta
Sada ćemo definirati rutu koja će omogućiti korisnicima da kreiraju novi blog post. Ova ruta će obrađivati GET zahtjeve za prikaz forme i POST zahtjeve za pohranu podataka u MongoDB.
```python
@app.route('/blog/create', methods=["get", "post"])
def post_create():
    form = BlogPostForm()
    if form.validate_on_submit():
        post = {
            'title': form.title.data,
            'content': form.content.data,
            'author': form.author.data,
            'status': form.status.data,
            'date': datetime.combine(form.date.data, datetime.min.time()),
            'tags': form.tags.data,
            'date_created': datetime.utcnow()
        }
        posts_collection.insert_one(post)
        flash('Post je uspješno upisan.', 'success')
        return redirect(url_for('index'))
    return render_template('blog_edit.html', form=form)
```

### Prikaz forme za kreiranje posta
Kreirajte novi predložak ```base.html``` u *templates* mapi i u njega umetnite slijedeći, vrlo jednostavan kod:
```html
{% extends "base.html" %}

{% block title %}Uređivanje Blog posta{% endblock %}

{% block body %}
{% from 'bootstrap5/form.html' import render_form %}
{{ render_form(form) }}
{% endblock %}
```

### Link za kreiranje posta i navigacijska traka
Dodat ćemo link koji vodi na stranicu s formom za kreiranje novog posta. Za tu svrhu dodat ćemo navigacijsku traku s linkom.
Kako bismo dodali navigacijsku traku koristeći Bootstrap u osnovni predložak (base.html), možemo koristiti ugrađene Bootstrap klase. Ova navigacijska traka imat će naslov Flask-Blog i jednu navigacijsku vezu koja vodi na stranicu za kreiranje posta.
Otvorite base.html predložak, te dodajte Bootstrap navigacijsku traku u tijelo stranice pomoću sljedećeg HTML koda, odmah ispod ```<div class="container mt-5">```:
```html
    {% raw %}
    {% from 'bootstrap5/nav.html' import render_nav_item %}
    <nav class="navbar navbar-expand-lg navbar-light bg-light sticky-top mb-5">
        <a class="navbar-brand ms-2" href="{{url_for('index') }}">Flask-Blog</a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav"
            aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>

        <div class="collapse navbar-collapse mt-1" id="navbarNav">
            <ul class="navbar-nav">
                {{ render_nav_item('post_create', 'Novi post', _use_li = True) }}
            </ul>
        </div>
    </nav>
    {% endraw %}
```

**Objašnjenje koda**

* ```navbar navbar-expand-lg navbar-light bg-light```: Ove Bootstrap klase koriste se za stiliziranje navigacijske trake. 
* ```navbar-expand-lg``` osigurava da se traka proširuje na većim ekranima, dok bg-light postavlja svijetlu pozadinu.
* ```navbar-brand```: Ovaj element prikazuje naziv aplikacije, tj. Flask-Blog.
* ```navbar-toggler```: Dodaje ikonu za preklapanje koja omogućuje skrivanje/otvaranje navigacijskog izbornika na manjim zaslonima.
* ```nav-link```: Poveznica unutar navigacijske trake koja vodi na stranicu za kreiranje novog posta pomoću URL-a rute create_post.

Ovaj dizajn navigacijske trake sada omogućava korisnicima pristup glavnoj stranici i stranici za kreiranje posta, a u daljnjim koracima ćemo dodati i dodatne veze prema potrebi.








Ako ste sve uspješno odradili, nova verzija aplikacije će imati navigacijsku traku s linkom "Novi post". Kliknite ga i pojavit će se stranica s obrascem za unos posta. Popunite obrazac, i kliknite gumb "Spremi". Vrijenosti obrasca bit će spremljeni u MongoDB bazu.

Ovim koracima omogućili smo unos podataka za novi blog post putem forme i pohranu tih podataka u MongoDB unutar kolekcije posts_collection. U daljnjim koracima proširit ćemo funkcionalnost kako bismo omogućili prikaz, uređivanje i brisanje blog postova.


[Naslovna stranica](README.md) | [Prethodno poglavlje: Flask i web obrasci](chapter1.md)| [Slijedeće poglavlje: Autentikacija](chapter3.md)
