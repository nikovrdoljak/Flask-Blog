<link rel="stylesheet" href="assets/css/custom.css">

[Naslovna stranica](README.md) | [Prethodno poglavlje: Autentikacija](chapter3.md) | [Slijedeće poglavlje: Objava aplikacije](chapter5.md)

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
        return User(user_data['email'], user_data.get('is_admin'), user_data.get('theme'))
    return None


class User(UserMixin):
    def __init__(self, email, admin=False):
        self.id = email
        self.admin = admin is True
        self.theme = theme

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
{% raw %}<p>is_admin: {{is_admin}}</p>
<p>is_author: {{is_author}}</p>{% endraw %}
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

## Upravljanje korisnicima

Sad ćemo napraviti novu rutu za upravljanje korisnicima, te ju zaštiti tako da joj samo "admin" može pristupiti.

```python
@app.route('/users', methods=['GET', 'POST'])
@login_required
@admin_permission.require(http_exception=403)
def users():
    users = users_collection.find().sort("email")
    return render_template('users.html', users = users)
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
<table class="table table-striped">
    <thead>
        <tr>
            <th>Email</th>
            <th>Ime</th>
            <th>Prezime</th>
            <th>Potvrđen</th>
            <th>Admin</th>
        </tr>
    </thead>
    <tbody>
        {% for user in users %}
        <tr>
            <td><a href="#" class="text-dark text-decoration-none">{{ user.email }}</a></td>
            <td>{{ user.first_name }}</td>
            <td>{{ user.last_name }}</td>
            <td>{{ 'Da' if user.is_confirmed else 'Ne' }}</td>
            <td>{{ 'Da' if user.is_admin else 'Ne' }}</td>
            <td>
                <a href="#" class="btn btn-primary btn-sm">Uredi</a>
            </td>
        </tr>
        {% endfor %}
    </tbody>
</table>

{% endblock %}
{% endraw %}
```

Dodajmo rutu i predložak za grešku **403**:
```python
@app.errorhandler(403)
def access_denied(e):
    return render_template('403.html', description=e.description), 403
```

403.html:
```html
{% raw %}{% extends "base.html" %}
{% block title %}Zabranjen pristup{% endblock %}

{% block body %}
<h2>Zabranjen pristup</h2>
<p>{{description}}</p>
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

Sljedeći korak će biti stranica za ažuriranje podataka korisnika. Pošto već imamo stranicu profile za ažuriranje profila prijavljenog korisnika, iskoristit ćemo postojeću logiku tako da postojeća i nova ruta dijele zajednički kod, koji ćemo dodati u novu funkciju. Pa promijenimo profile rutu i dodajmo:

```python
def update_user_data(user_data, form):
    if form.validate_on_submit():
        db.users.update_one(
        {"_id": user_data['_id']},
        {"$set": {
            "first_name": form.first_name.data,
            "last_name": form.last_name.data,
            "bio": form.bio.data,
            "theme": form.bio.theme
        }}
        )
        if form.image.data:
            # Pobrišimo postojeću ako postoji
            if hasattr(user_data, 'image_id') and user_data.image_id:
                fs.delete(user_data.image_id)
            
            image_id = save_image_to_gridfs(request, fs)
            if image_id != None:
                users_collection.update_one(
                {"_id": user_data['_id']},
                {"$set": {
                    'image_id': image_id,
                }}
            )
        flash("Podaci uspješno ažurirani!", "success")
        return True
    return False

@app.route('/profile', methods=['GET', 'POST'])
@login_required
def profile():
    user_data = users_collection.find_one({"email": current_user.get_id()})
    form = ProfileForm(data=user_data)
    title = "Vaš profil"
    if update_user_data(user_data, form):
        return redirect(url_for('profile'))
    return render_template('profile.html', form=form, image_id=user_data.get("image_id"), title=title)

@app.route('/user/<user_id>', methods=['GET', 'POST'])
@login_required
@admin_permission.require(http_exception=403)
def user_edit(user_id):
    user_data = users_collection.find_one({"_id": ObjectId(user_id)})
    form = UserForm(data=user_data)
    title = "Korisnički profil"
    if update_user_data(user_data, form):
        return redirect(url_for('users'))
    return render_template('profile.html', form=form, image_id=user_data.get("image_id"), title=title)

```

Dodajmo i novu formu:
```python
class UserForm(FlaskForm):
    email = StringField("Email", validators=[DataRequired(), Length(3, 64), Email()])
    first_name = StringField("Ime", validators=[DataRequired(), Length(max=50)])
    last_name = StringField("Prezime", validators=[DataRequired(), Length(max=50)])
    is_confirmed = BooleanField('Potvrđen')
    bio = TextAreaField("Biografija", validators=[Length(max=1000)], render_kw={"id": "markdown-editor"})
    image = FileField('Slika', validators=[FileAllowed(['jpg', 'png', 'jpeg'], 'Samo slike!')])
    submit = SubmitField("Spremi")
```

Izmijenimo **profile.html** predložak tako da za naslov stavimo ```{% raw %}{{ title }}{% endraw %}```.

U users predlošku dodajmo link za Uredi gumb:
```html
{% raw %}<a href="{{ url_for('user_edit', user_id=user['_id']) }}" class="btn btn-primary btn-sm">Uredi</a>{% endraw %}
```

## Uređivanje vlastitih članaka
Trenutno bilo koji prijavljeni korisnik može uređivati ili izbrisati svaki članak, što ne želimo. Želimo da samo korisnik koji je kreirao članak može uređivati vlastite članke.
Flask-Principal omogućuje stvaranje prilagođenih potreba (*Needs*). U ovom slučaju definirat ćemo *potrebu* za *vlasništvom* nad člankom:

```python
# Klasa za definiranje potrebe za uređivanjem članka
class EditPostNeed(Need):
    def __init__(self, post_id):
        super().__init__('edit_post', post_id)

# Pomoćna metoda za provjeru prava uređivanja
def edit_post_permission(post_id):
    return Permission(EditPostNeed(str(ObjectId(post_id))))
```

U Flask-Principalu moramo povezati prijavljenog korisnika s njihovim potrebama (npr. mogućnošću uređivanja vlastitih postova). To se postiže dodavanjem tih potreba pri prijavi korisnika. U funkciju **on_identity_loaded** dodajmo:
```python
        # Dodajemo EditPostNeed za svaki članak koji je korisnik kreirao
        user_posts = posts_collection.find({"author": current_user.get_id()})
        for post in user_posts:
            identity.provides.add(EditPostNeed(str(post["_id"])))
```

Sad imamo sve spremno da zaštitimo uređivanje čalanak od strane drugih korisnika. U rute **post_edit()** i **delete_post()** dodjmo provjeru prava:
```python
from flask import abort

def post_edit(post_id):
    #  Provjera dozvole
    permission = edit_post_permission(post_id)
    if not permission.can():
        abort(403, "Nemate dozvolu za uređivanje ovog članka.")


def delete_post(post_id):
     # Provjera dozvole
    permission = edit_post_permission(post_id)
    if not permission.can():
        abort(403, "Nemate dozvolu za brisanje ovog posta.")
```

U rutu **post_view()** dodajmo **edit_post_permission** metodu kao parametar predlošku:
```python
    return render_template('blog_view.html', post=post, edit_post_permission=edit_post_permission)
```

U **blog_view.html** predložak sakrijmo gumbe za izmjenu i brisanje članka ako korisnik nema prava:
```html
        {% raw %}{% if edit_post_permission(post['_id']).can() %}
        <div>
            <a href="{{ url_for('post_edit', post_id=post['_id']) }}" type="button" class="btn btn-primary btn-sm">Uredi</a>
            <button type="button" class="btn btn-danger btn-sm" data-bs-toggle="modal" data-bs-target="#deleteModal"
                data-postid="{{ post['_id'] }}">
                Briši
            </button>
        </div>
        {% endif %}{% endraw %}
```


[Naslovna stranica](README.md) | [Prethodno poglavlje: Autentikacija](chapter3.md) | [Slijedeće poglavlje: Objava aplikacije](chapter5.md)


