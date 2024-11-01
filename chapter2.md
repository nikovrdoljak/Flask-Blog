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
* **Kreiranje postova**: Aplikacija će omogućiti autorima da kreiraju nove postove putem forme. Ova forma će uključivati potrebna polja kao što su naslov, sadržaj, autor, datum, oznake i slika.
* **Uređivanje postova**: Autori će moći uređivati postojeće postove. To će uključivati ponovno prikazivanje forme s prethodno unesenim podacima, omogućujući autorima da izvrše izmjene.
* **Brisanje postova**: Korisnici će moći brisati postove, čime će se trajno ukloniti podaci iz baze.

### Struktura modela podataka
Svaki blog post će imati sljedeće atribute:
* Naslov (title): Kratak opis postа koji će biti prikazan na listi postova.
* Sadržaj (content): Glavni tekst postа.
* Autor (author): Ime osobe koja je napisala post.
* Datum (date): Datum kada je post kreiran.
* Oznake (tags): Kategorije ili ključne riječi povezane s postom, omogućujući korisnicima lakše pretraživanje.
* Slika (image): veza prema slici koja će biti prikazana u postu.




[Naslovna stranica](README.md) | [Prethodno poglavlje: Flask i web obrasci](chapter1.md)| [Slijedeće poglavlje: Autentikacija](chapter3.md)
