<link rel="stylesheet" href="assets/css/custom.css">

[Naslovna stranica](README.md) | [Prethodno poglavlje: Autentikacija](chapter3.md)| 

# Autorizacija

**Autorizacija** je proces provjere ima li korisnik prava pristupa određenim resursima ili funkcionalnostima unutar aplikacije. Dok je **autentikacija** usmjerena na provjeru identiteta korisnika (npr. prijava pomoću korisničkog imena i zaporke), autorizacija se bavi time što korisnik smije raditi nakon što se prijavio.

## Flask-Principal

Flask-Principal je proširenje za Flask koje omogućuje definiranje pravila pristupa putem identiteta i dopuštenja. Koristi "identitete" za prepoznavanje korisnika i "uloge" (*roles*) za definiranje prava pristupa.

Glavne značajke:
* Identiteti: Predstavljaju korisnika u aplikaciji.
* Uloge (roles): Koriste se za definiranje uloga, npr. admin, author, user.
* Pravila (permissions): Određuju koje uloge imaju pristup određenim resursima.

U našem primjeru, jednom korisniku ćemo dodijeli **admin** prava, te će taj korisnik imati mogućnost uređivanja podataka ostalih korisnika (koji će imati isključivo **editor** prava). Ostali korisnici će moći uređivati isključivo svoje članke. Takav korisnik će u bazi podataka imati svojstvo **is_admin: true**.

Najprije instalirajmo flask-principal:

```bash
pip install flask-principal
```

Dodajmo sljedeći kod u app.py:
```python
from flask_principal import Principal, Permission, RoleNeed, Identity, identity_changed, identity_loaded, UserNeed


principal = Principal(app)
admin_permission = Permission(RoleNeed('admin'))
author_permission = Permission(RoleNeed('author'))

```

U funkciji **login()** dodajmo sljedeću liniju ispod ```login_user(user, form.remember_me.data)``` kako bi flask-principal znao da se korisnik promijenio.
```python
        identity_changed.send(app, identity=Identity(user.id))
```

Promijenimo i sljedeći dio, tako da prilikom dohvata podataka korisnika znamo da li je korisnik "admin":
```python
@login_manager.user_loader
def load_user(email):
    user_data = users_collection.find_one({"email": email})
    if user_data:
        return User(user_data['email'], user_data.get('is_admin'))
    return None


class User(UserMixin):
    def __init__(self, email, admin=False):
        self.id = email
        self.admin = admin is True

    @property
    def is_admin(self):
        return self.admin
```

Dodajmo još i sljedeću funkciju:
```python
@identity_loaded.connect_via(app)
def on_identity_loaded(sender, identity):
    if current_user.is_authenticated:
        identity.user = current_user
        identity.provides.add(UserNeed(current_user.id))
        identity.provides.add(RoleNeed('author'))
        if current_user.is_admin:
            identity.provides.add(RoleNeed('admin'))
```

Testirajmo kako izgledaju prava korisnika u njegovom profilu. Dodajmo u **profile()** rutu:
```python
def profile():
    is_admin = admin_permission.can()
    is_author = author_permission.can()
```

Te proslijedimo ta dva parametra predlošku:

```python
    return render_template('profile.html', form=form, image_id=user_data["image_id"], is_admin=is_admin, is_author=is_author)
```

U **profile.html** predložak dodajmo:
```html
<p>is_admin: {{is_admin}}</p>
<p>is_author: {{is_author}}</p>
```

Ako se sad prijavimo i odemo u stranicu profila, vidjet ćemo da je korisnik u ulozi "author", ali ne i u ulozi "admin".
Uđimo u bazu podataka, te tom korisniku dodajmo *boolean* svojstvo "**is_admin: true**", osvježimo stranicu, i provjerimo da je korisnik sad i u "admin" ulozi.
Ovu promjenu možete napraviti ručno u Compass aplikaciji ili pozivanjem sljedeće MongoDB Shell naredbe:
```javascript
db.users.updateOne(
    { email: "korisnik@domena.hr" }, // Zamijenite s e-mail adresom korisnika
    { $set: { is_admin: true } }  // Postavlja is_admin na true
)
```

Nakon obavljenog testiranja možemo pobrisati izmjene koje smo dodali u profile rutu i predložak.

Sad ćemo napraviti novu rutu za upravljanje korisnicima, te ju zaštiti tako da joj samo "admin" može pristupiti.

```python
@app.route('/users', methods=['GET', 'POST'])
@login_required
@admin_permission.require(http_exception=403)
def users():
    return "Popis korisnika"
```

Dekorator ```@admin_permission.require(http_exception=403)``` znači da samo korisnik koji je u **admin** ulozi, smije pristupiti traženoj ruti. 

Dodajmo i link u navigacijski izbornik ispod profilnog linka:
```html
    <li>
        <a class="dropdown-item icon-link" href="{{url_for('users') }}"><i class="bi bi-people mb-2"></i>Korisnici</a>
    </li>
```

Te novi predložak **users.html**:
```html
{% raw %}{% extends "base.html" %}
{% block title %}Korisnici{% endblock %}

{% block body %}
<h2>Korisnici</h2>
{% endblock %}{% endraw %}
```

Dodajmo rutu i predložak za grešku **403**:
```python
@app.errorhandler(403)
def access_denied(e):
    return render_template('403.html'), 403
```

403.html:
```html
{% raw %}{% extends "base.html" %}
{% block title %}Zabranjen pristup{% endblock %}

{% block body %}
<h2>Zabranjen pristup</h2>
<p>Nemate ovlasti za pristup ovoj stranici.</p>
{% endblock %}{% endraw %}
```

Testirajte pristup stranici Korisnici za korisnika koji je admin i koji nije.

Sad možemo i sakriti link na tu stranicu u navigacijskom izborniku ako korisnik nije admin:
```html
    {% raw %}{% if current_user.is_admin %}
    <li>
        <a class="dropdown-item icon-link" href="{{url_for('users') }}"><i class="bi bi-people mb-2"></i>Korisnici</a>
    </li>
    {% endif %}{% endraw %}
```

[Naslovna stranica](README.md) | [Prethodno poglavlje: Autentikacija](chapter3.md)| 