<link rel="stylesheet" href="assets/css/custom.css">

[Naslovna stranica](README.md) | [Prethodno poglavlje: Uvod u baze podataka](chapter2.md)| 


# Autentikacija

**Autentikacija** je proces identifikacije korisnika u aplikaciji kako bi im se omogućio pristup zaštićenim resursima. 
Flask nudi razne biblioteke i alate za jednostavnu implementaciju autentifikacije. 
Jedna od najpopularnijih biblioteka za to je **Flask-Login**, koja omogućava rukovanje prijavom, odjavom i zaštitom ruta.

## Osnovni pojmovi
* **Prijava (Login):** Korisnik unosi svoje vjerodajnice kako bi se identificirao.
* **Odjava (Logout):** Proces prekida korisničke sesije.
* **Zaštićene rute:** Stranice kojima mogu pristupiti samo prijavljeni korisnici.
* **Sesije:** Koriste se za praćenje stanja korisnika nakon prijave.

## Faktori autentikacije
* **Faktor znanja** – ono što korisnik zna – zaporka, fraza, PIN, odgovor na pitanje
* **Faktor vlasništva** – ono što korisnik ima – kartica, narukvica, telefon…
* **Faktor pripadnosti** – ono što korisnik jest ili radi – otisak prsta, mrežnica, glas, lice, potpis…
* **Faktor vremena**
* **Faktor lokacije**

Da bi se osoba pozitivno autenticirala, poželjno je da elementi barem dva faktora budu verificirani.

## Tipovi autentikacije
* Single-factor
    * Provjera autentičnosti s jednim faktorom
    * Najslabija i nepoželjna za transakcije koje traže visok nivo zaštite
* Two-factor
    * Bankomat – nešto što korisnik ima (kartica) i zna (PIN)
    * Ili zaporka (znamo) + token (imamo; na uređaju)
* Multi-factor

## Implementacije
Koristit ćemo biblioteku [Flask-Login](https://flask-login.readthedocs.io/en/latest/) za autentifikaciju, a u MongoDB bazu ćemo pohranjivati korisničke podatake.

Prvo instalirajmo flask-login, te dodatak paket *email-validator*:
```bash
pip install flask-login email-validator
```
Paket **email-validator** služi za provjeru valjanosti sintakse e-mail adrese i, opcionalno, za provjeru domena. Osigurava da korisnici unose ispravne e-mail adrese prilikom registracije ili prijave.

## Prijava

Za prijavu korisnika u našu aplikaciju koristit ćemo obrazac za prijavu, u koji će korisnik morati upisati svoju email adresu i zaporku. No prije nego nastavimo, naglasimo da zaporku nikad ne smijemo spremati u izvornom obliku. To predstavlja sigurnosni rizik jer:
* Kompromitirane baze podataka: Ako haker dođe do baze podataka, sve zaporke će biti odmah dostupne u čitljivom obliku.
* Ponovna upotreba zaporki: Mnogi korisnici koriste iste zaporke na više platformi. Kompromitacija jedne baze podataka može omogućiti pristup drugim računima korisnika.
* Pravni i etički standardi: Mnoge sigurnosne norme, poput GDPR-a i ISO 27001, zahtijevaju šifriranje osjetljivih podataka.
* Povjerenje korisnika: Gubitak povjerenja korisnika zbog curenja podataka može uništiti ugled aplikacije.

Umjesto toga, zaporke treba "heširati" (eng. hashing), što znači da se pretvaraju u nečitljiv niz znakova uz korištenje algoritma.

Flask pruža funkcije za sigurnu obradu zaporki putem biblioteke **werkzeug.security**.

```generate_password_hash```
* Generira sigurnan "heš" zaporke koristeći algoritam poput PBKDF2, bcrypt ili scrypt.
* Dodaje salt (nasumični dodatak podacima) kako bi se spriječili napadi pomoću unaprijed izračunatih heševa (npr. *rainbow table attacks*).

```check_password_hash```
* Provjerava podudara li se unesena zaporka s ranije spremljenim hešom.
* Uzima unesenu zaporku, ponovo je hešira koristeći isti algoritam i uspoređuje s postojećim hešom.

### Primjer 
```python
flask shell
>>> from werkzeug.security import generate_password_hash, check_password_hash
>>> hash1 = generate_password_hash('123')
>>> print(hash1)
pbkdf2:sha256:50000$ClVWrTj0$d9ef0819c7bcd9ac996079d284f87f4969f3ba09e504c58a839a169ef10c7193
>>> check_password_hash(hash1, '123')
True
```

### Obrazac za prijavu
Dodajmo najprije klasu za *login* obrazac u ```app.py```:


```python
from wtforms import PasswordField, BooleanField
from wtforms.validators import Email

class LoginForm(FlaskForm):
    email = StringField('E-mail', validators=[DataRequired(), Length(1, 64), Email()])
    password = PasswordField('Zaporka', validators=[DataRequired()])
    remember_me = BooleanField('Ostani prijavljen')
    submit = SubmitField('Prijava')
```

Novi elementi koje susrećemo su:
* ```Email()``` validator - Provjerava je li uneseni tekst valjana e-mail adresa
* ```PasswordField```: Specijalizirano polje za unos zaporke (prikazuje ju kao zvjezdice).
* *Ostani prijavljen*: Koristi se za opcionalnu funkcionalnost koja omogućuje korisniku da ostane prijavljen dulje vrijeme.
  * Flask-Login standardno koristi sesije za praćenje prijavljenih korisnika. Sesija traje samo dok je preglednik otvoren.
  * Ako korisnik označi opciju "Ostani prijavljen", Flask-Login stvara trajni kolačić koji pohranjuje sigurni token. Ovaj kolačić ostaje i nakon završetka sesije.

Dodajmo novu rutu:
```python
@app.route('/login', methods=['GET', 'POST'])
def login():
    form = LoginForm()
    return render_template('login.html', form = form)
```

Dodjamo i novi predložak **login.html**
```html
{% extends "base.html" %}
{% from 'bootstrap5/form.html' import render_form %}

{% block title %}Prijava{% endblock %}

{% block body %}
<div class="container">
    <div class="page-header">
        <h1>Prijava</h1>
    </div>
    <div class="col-md-4">
        {{ render_form(form) }}
    </div>
</div>
{% endblock %}
```

Te link za prijavu u baznom predlošku:
```{{ render_nav_item('login', 'Prijava', _use_li = True) }}```

Pokrenimo aplikaciju, kliknimo na novi link i potvrdimo da je obrazac za prijavu prikazan. Sad slijedi implementacija prijave korisnika.

## Prijava korisnika

**Flask-login** paket s brine o prijavi, odjavi i pamćenju korisnikove sesije tijekom vremena:
* Pamti korisnički ID u sesiji i brine se o prijavi i odjavi
* Omogućava da označite koje rute može samo prijavljeni korisnik vidjeti
* Brine o implementaciji *"zapamti me"* funkcionalnosti
* Omogućava da netko ne može *ukrasti* korisničku sesiju
* Lako se integrira s drugim ekstenzijama poput *flask-principal* za autorizaciju

Ono što moramo sami napraviti je:
* Pobrinuti se gdje ćemo spremati podatke (npr. u bazu )
* Odlučiti koju metodu autentikacije ćemo koristiti (korisnik/zaporka, OpenID, i sl.)
* Brinuti o načinu registracije, aktivacije, obnovi zaporke i sl.

Dodajmo potrebne module:
```python
from flask_login import UserMixin
from flask_login import LoginManager, login_required, current_user, login_user, logout_user
```
Najprije konfigurirajmo aplikaciju da koristi flask-login:
```python
from flask_login import UserMixin, LoginManager
from flask_login import login_required, current_user, login_user, logout_user

login_manager = LoginManager()
login_manager.init_app(app)
login_manager.login_view = 'login'

@login_manager.user_loader
def load_user(user_id):
    return User.get(user_id)
```
Dodajmo ```User``` klasu koja naslijeđuje tzv. ```UserMixin``` klasu:
```python
class User(UserMixin):
    USERS = {
        'jure@unizd.hr': 'sifra1',
        'ana@unizd.hr': 'sifra2',
        'ivana@unizd.hr': 'sifra3'
    }

    def __init__(self, id):
        if not id in self.USERS:
            raise UserNotFoundError()
        self.id = id
        self.password = self.USERS[id]
         
    @classmethod
    def get(self_class, id):
        try:
            return self_class(id)
        except UserNotFoundError:
            return None

class UserNotFoundError(Exception):
    pass
```

**Objašnjenje:**

* **LoginManager** je centralni objekt u Flask-Loginu koji upravlja autentifikacijom korisnika. On omogućuje povezivanje s korisnicima, upravljanje sesijama i omogućuje funkcionalnosti poput prijave, odjave i održavanja prijave.
* ```user_loader``` je funkcija koja omogućuje Flask-Loginu da učita korisnika (iz baze podataka) na temelju ID-a spremljenog u sesiji. Ova funkcija mora biti registrirana u LoginManager i koristi se za autentifikaciju korisnika kad god dođe do novog HTTP zahtjeva.
* Ako korisnik nije prijavljen, LoginManager može automatski preusmjeriti korisnika na stranicu za prijavu (login ruta).
    * ```login_manager.login_view = 'login'``` 
* Flask-Login koristi metodu **login_user()** za prijavu korisnika.
* **logout_user()** se koristi za odjavu korisnika.
* **current_user** se koristi za pristup podacima trenutno prijavljenog korisnika u aplikaciji.
* **UserMixin** je klasa koja pruža osnovnu funkcionalnost potrebnu za rad s Flask-Loginom. Kada kreirate model korisnika, možemo naslijediti UserMixin kako bismo automatski dobili implementaciju metoda potrebnih za autentifikaciju korisnika. Metode koje UserMixin nudi:
    * **is_authenticated()**: Ova metoda vraća True ako je korisnik prijavljen, a False ako nije.
    * **is_active()**: Vraća True ako je korisnički račun aktivan (korisno za deaktivaciju korisnika), a False ako nije.
    * **is_anonymous()**: Vraća True ako je korisnik anonimni korisnik (npr. posjetitelj koji nije prijavljen), a False ako nije.
    * **get_id()**: Vraća jedinstveni identifikator korisnika (obično ID u bazi podataka), koji Flask-Login koristi za pohranu i prepoznavanje korisnika.
* U klasi **User** riječnik **USERS** je statički spremnik koji nam za sad simulira bazu podataka. Ključevi su e-mail adrese korisnika, a vrijednosti su lozinke.
* Kada se instancira objekt klase **User**, provjerava se postoji li korisnik s danim id (e-mail adresa) u rječniku USERS.
  * Ako korisnik ne postoji, baca se prilagođena iznimka UserNotFoundError.
  * Ako korisnik postoji:
    * self.id sprema korisnički ID (e-mail).
    * self.password sprema lozinku korisnika iz rječnika.
* Metoda **get()** olakšava dohvaćanje korisnika na temelju ID-a. Pokušava stvoriti instancu korisnika pozivanjem konstruktora. Ako korisnik ne postoji, vraća None umjesto da se aplikacija sruši. Korištenje ove metode olakšava upravljanje greškama pri dohvaćanju korisnika.
* Klasa **UserNotFoundError** je prilagođena iznimka koja se koristi za označavanje da korisnik nije pronađen u rječniku USERS.


U login ruti implamentirajmo autentikaciju:
```python
@app.route('/login', methods=['GET', 'POST'])
def login():
    form = LoginForm()
    if form.validate_on_submit():
        user = User.get(form.email.data)
        if user is not None and user.password == form.password.data:
            login_user(user, form.remember_me.data)
            next = request.args.get('next')
            if next is None or not next.startswith('/'):
                next = url_for('index')
            flash('Uspješno ste se prijavili!', category='success')
            return redirect(next)
        flash('Neispravno korisničko ime ili zaporka!', category='warning')
    return render_template('login.html', form=form)
```

* Korisnik će biti uspješno prijavljen ako je upisao email adresu i zaportku iz riječnika USERS. Prikazuje se poruka o uspjehu i korisnik se preusmjerava na željenu ili početnu stranicu.
* Ako je korisničko ime ili zaporka neispravno, prikazuje se poruka upozorenja.
* Sigurnosna provjera **next** sigurava da korisnik ne bude preusmjeren na zlonamjerni URL.
  * Ako korisnik pokušava pristupiti zaštićenoj stranici prije prijave, parametar next će sadržavati URL te stranice.
  * Ako next nije valjan (npr. ne počinje s /), korisnik će biti preusmjeren na početnu stranicu aplikacije (index).

