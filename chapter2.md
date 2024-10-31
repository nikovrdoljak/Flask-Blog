<link rel="stylesheet" href="assets/css/custom.css">

# Uvod u baze podataka i njihovu primjenu u aplikacijama
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













[Naslovna stranica](README.md) | [Prethodno poglavlje: Flask i web obrasci](chapter1.md)| [Slijedeće poglavlje: Autentikacija](chapter3.md)
