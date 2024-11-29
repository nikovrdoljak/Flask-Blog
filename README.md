<link rel="stylesheet" href="assets/css/custom.css">

# Flask Tutorial

Dobrodošli u Flask Blog vodič

- [Poglavlje 1: Flask i web obrasci](chapter1.md#poglavlje-1-flask-i-web-obrasci)
  - [Hello world aplikacija](chapter1.md#hello-world-aplikacija)
  - [Predlošci](chapter1.md#predlošci)
  - [Korištenje `base.html` kao osnovnog predloška](chapter1.md#korištenje-basehtml-kao-osnovnog-predloška)
  - [Dodavanje "bootstrap-flask" biblioteke](chapter1.md#dodavanje-bootstrap-flask-biblioteke)
  - [Rad s HTML web obrascima](chapter1.md#rad-s-html-web-obrascima)
    - [Struktura Web Obrasca](chapter1.md#struktura-web-obrasca)
    - [Atributi Obrasca](chapter1.md#atributi-obrasca)
      - [Upitni Niz (Query String)](chapter1.md#upitni-niz-query-string)
      - [Dodajmo i Bootstrap CSS klase u formu:](chapter1.md#dodajmo-i-bootstrap-css-klase-u-formu)
      - [Problem: "Confirm Form Resubmission"](chapter1.md#problem-confirm-form-resubmission)
  - [WTForms](chapter1.md#wtforms)
    - [Što je Flask-WTF i kako ga instalirati](chapter1.md#što-je-flask-wtf-i-kako-ga-instalirati)
    - [Kreiranje NameForm klase s validatorima](chapter1.md#kreiranje-nameform-klase-s-validatorima)
  - [Post/Redirect/Get (PRG) obrazac](chapter1.md#postredirectget-prg-obrazac)
    - [Implementacija PRG obrasca u Flask aplikaciji](chapter1.md#implementacija-prg-obrasca-u-flask-aplikaciji)
    - [flash poruke](chapter1.md#flash-poruke)
- [Poglavlje 2: Uvod u baze podataka i njihovu primjenu u aplikacijama](chapter2.md#uvod-u-baze-podataka-i-njihovu-primjenu-u-aplikacijama)
  - [MongoDB – NoSQL baza podataka](chapter2.md#mongodb--nosql-baza-podataka)
  - [Instalacija MongoDB-a na lokalno računalo](chapter2.md#instalacija-mongodb-a-na-lokalno-računalo)
    - [Instalacija:](chapter2.md#instalacija)
    - [Pokretanje MongoDB-a:](chapter2.md#pokretanje-mongodb-a)
  - [Specifikacija Blog aplikacije](chapter2.md#specifikacija-blog-aplikacije)
    - [Glavne funkcionalnosti aplikacije:](chapter2.md#glavne-funkcionalnosti-aplikacije)
    - [Struktura modela podataka](chapter2.md#struktura-modela-podataka)
  - [Implementacija Blog aplikacije](chapter2.md#implementacija-blog-aplikacije)
    - [Instalacija pymongo](chapter2.md#instalacija-pymongo)
    - [Postavljanje koda za spajanje na bazu podataka](chapter2.md#postavljanje-koda-za-spajanje-na-bazu-podataka)
    - [Kreiranje BlogPostForm klase](chapter2.md#kreiranje-blogpostform-klase)
    - [Ruta za kreiranje novog posta](chapter2.md#ruta-za-kreiranje-novog-posta)
    - [Prikaz forme za kreiranje posta](chapter2.md#prikaz-forme-za-kreiranje-posta)
    - [Link za kreiranje posta i navigacijska traka](chapter2.md#link-za-kreiranje-posta-i-navigacijska-traka)
    - [Provjera sadržaja kolekcije članaka u bazi](chapter2.md#provjera-sadržaja-kolekcije-članaka-u-bazi)
    - [Mongo Shell](chapter2.md#mongo-shell)
    - [Prikaz članaka na glavnoj stranici](chapter2.md#prikaz-članaka-na-glavnoj-stranici)
    - [Prikaz pojedinačnog posta](chapter2.md#prikaz-pojedinačnog-posta)
    - [Uređivanje posta](chapter2.md#uređivanje-posta)
    - [Brisanje posta](chapter2.md#brisanje-posta)
    - [Podrška za slike u članku](chapter2.md#podrška-za-slike-u-članku)
    - [Markdown podrška](chapter2.md#markdown-podrška)
    - [Mardown editor (EasyMDE)](chapter2.md#mardown-editor-easymde)
    - [Oznake (tagovi) i Choices.js](chapter2.md#oznake-tagovi-i-choicesjs)
- [Poglavlje 3: Autentikacija](chapter3.md#autentikacija)
  - [Osnovni pojmovi](chapter3.md#osnovni-pojmovi)
  - [Faktori autentikacije](chapter3.md#faktori-autentikacije)
  - [Tipovi autentikacije](chapter3.md#tipovi-autentikacije)
  - [Implementacije](chapter3.md#implementacije)
  - [Prijava](chapter3.md#prijava)
    - [Primjer](chapter3.md#primjer)
    - [Obrazac za prijavu](chapter3.md#obrazac-za-prijavu)
  - [Prijava korisnika](chapter3.md#prijava-korisnika)
  - [Odjava](chapter3.md#odjava)
  - [Registracija](chapter3.md#registracija)
  - [Aktivacija korisnika i slanje emaila](chapter3.md#aktivacija-korisnika-i-slanje-emaila)
    - [Slanje maila s tokenom za verifikaciju](chapter3.md#slanje-maila-s-tokenom-za-verifikaciju)
    - [Sigurnosne mjere i environment varijable](chapter3.md#sigurnosne-mjere-i-environment-varijable)
  - [Korisnički profil](chapter3.md#korisnički-profil)
    - [Spremanje korisnika kao autora članka](chapter3.md#spremanje-korisnika-kao-autora-članka)
  - [Moji članci](chapter3.md#moji-članci)
  - [Bootswatch](chapter3.md#bootswatch)
- [Poglevlje 4: Autorizacija](chapter4.md#autorizacija)
  - [Flask-Principal](chapter4.md#flask-principal)
  - [Upravljanje korisnicima](chapter4.md#upravljanje-korisnicima)
  - [Uređivanje vlastitih članaka](chapter4.md#uređivanje-vlastitih-članaka)
- [Poglavlje 5: Objava aplikacije](chapter5.md#objava-aplikacije)
  - [MongoDB Atlas](chapter5.md#mongodb-atlas)
    - [Kako koristiti MongoDB Atlas u Flask aplikaciji?](chapter5.md#kako-koristiti-mongodb-atlas-u-flask-aplikaciji)
  - [Render](chapter5.md#render)
    - [MongoDB Atlas i IP addrese](chapter5.md#mongodb-atlas-i-ip-addrese)