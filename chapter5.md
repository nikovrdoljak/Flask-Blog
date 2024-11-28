<link rel="stylesheet" href="assets/css/custom.css">

[Naslovna stranica](README.md) | [Prethodno poglavlje: Autorizacija](chapter4.md)| 

# Objava aplikacije
Naša aplikacija pomalo poprima ozbiljan izgled, stoga sad želimo da je objavimo na neki javni poslužitelj, kako bi i drugi mogli vidjeti kako izgleda i radi. U ovom poglavlju ćemo opisati postupak objeve (*deployment*) naše aplikacije na dva servisa koji su nam dostupni i bespalni:
* [https://cloud.mongodb.com/](MongoDB Atlas) - na koji ćemo staviti našu bazu podataka
* [https://www.render.com/]Render - na koji ćemo objaviti Flask aplikaciju

## MongoDB Atlas

MongoDB Atlas je cloud verzija MongoDB-a, koja omogućava host baza podataka na različitim pružateljima usluga kao što su AWS, Azure i Google Cloud. Atlas olakšava postavljanje, upravljanje i skaliranje MongoDB baza podataka. 

MongoDB Atlas nudi besplatni sloj koji uključuje sljedeće:
* 512 MB prostora za pohranu.
* Shared Cluster (M0), što znači da resurse dijelite s drugim korisnicima.
* Hosting na bilo kojem od podržanih pružatelja cloud usluga.
* Podrška za do 100 kolekcija po bazi.
* 3 replike za visoku dostupnost.
* Podrška za jednu bazu podataka po projektu.
Ovaj sloj je savršen za edukacijske svrhe i male projekte.

### Kako koristiti MongoDB Atlas u Flask aplikaciji?
* Kreirajte MongoDB Atlas račun ili se prijavite koristeći postojeći račun. 
* Kreirajte novi Project i unutar njega postavite Free Cluster (M0).
* Odaberite pružatelja usluga (AWS, Azure ili Google Cloud) i regiju.
* Dodajte ime *clustera* (npr., Cluster0).
* Kada je cluster kreiran, idite na opciju Database Access i dodajte korisnika za autentifikaciju.
* Idite na Network Access i dodajte IP adrese koje imaju pristup bazi. Provjerite jeste li dodali svoju trenutnu IP adresu..
* Preuzmite konekcijski string koji će nam trebati u aplikaciji, te ga dodajte u .env datoteku:
    ```
    MONGODB_CONNECTION_STRING = 'mongodb+srv://korisnik:zaporka@cluster0.mongodb....'
    ```
* U app.py promijenite:
    ```python
    client = MongoClient(os.getenv('MONGODB_CONNECTION_STRING'))
    ```
* U MongoDB Compass aplikaciji dodjte novu konekciju i upišite isti konekcijski string.

Pokrenite aplikaciju, registrirajte se, upišite neki novi članak i potvrdite u Compassu da su podaci upisani u novu bazu podataka na Atlasu.

## Render

Render je moderna platforma za hosting koja omogućava lako postavljanje web aplikacija, API-ja, statičnih stranica i više. Idealna je za obrazovne projekte jer nudi besplatnu razinu za jednostavne aplikacije.
Prođimo sljedeće korake kako bismo našu Flask aplikaciju objavili:

* Posjetite [https://www.render.com/]Render i kliknite na Sign Up. Odaberite opciju za prijavu s GitHub računom. Dajte Render-u pristup vašim GitHub repozitorijima (možete odabrati specifične repozitorije ili sve repozitorije).
* Nakon prijave idite na Dashboard. Kliknite na New > Web Service. Render će prikazati popis vaših GitHub repozitorija. Odaberite repozitorij koji sadrži Flask aplikaciju.
* Konfiguracija projekta
  * Naziv servisa: Postavite naziv za svoju uslugu (npr. flask-blog-app).
  * Environment: Odaberite Web Service
  * Branch: Odaberite Git granu (obično main ili master).
  * Build command: pip install -r requirements.txt
  * Start Command: Flask aplikacije najčešće u vanjskoj okolini koriste **gunicorn**. Postavite ```gunicorn app:app```
    * **Gunicorn** (Green Unicorn) je **WSGI** (Web Server Gateway Interface) HTTP server za Python aplikacije. Dizajniran je za pokretanje u produkcijskom okruženju i omogućuje da Python web aplikacije, poput Flask aplikacija, budu dostupne krajnjim korisnicima putem HTTP-a.
    * S druge strane Werkzeug je savršen za lokalni razvoj jer je jednostavan i lak za upotrebu, ali nije dovoljno jak za produkciju.
    * Napomena: prije objave dodajte gunicorn u vaš projekt:
      *  ```pip install gunicorn```
      *  ```pip freeze > requirements.txt```
  * Auto-Deploy: Yes
    * Svaki put kad objavite kod na GitHubu, nova verzija aplikacije će se objaviti i na Renderu.
  * Odaberite Free Instance tijekom konfiguracije. Render nudi Free Instance Type, koja uključuje:
    * 512 MB memorije
    * 0.5 vCPU
    * 750 sati mjesečno (za besplatne račune, dovoljno za konstantan rad jedne aplikacije).
  * Postavljanje varijabli okruženja (Environment Variables)
    * Kliknite na Environment, te kopirajte sadržaj vaše **.env** datoteke.
* Objava Flask aplikacije
  * Kliknite Create Web Service.
  * Render će pokrenuti inicijalni deploy. Ovo može potrajati nekoliko minuta.
  * Nakon uspješne objave, dobit ćete URL poput https://flask-blog-app.onrender.com.

### MongoDB Atlas i IP addrese
Da bi aplikacija radila moramo dodati [Render-ovu IP adresu](https://render.com/docs/static-outbound-ip-addresses) u Atlas-ov Network Access. U Render Dasboard-u gore lijevo se nalazi izbornik "**Connect**" koji će prikazati IP adrese Render clustera na kojem je vaša aplikacija. 
U MongoDB Atlas Dashboardu kliknite **Network Access**, kliknite "Add IP address", te dodajte jednu po jedno IP adresu s Rendera.

Provjerite da vaša aplikacija radi na toj adresi.

[Naslovna stranica](README.md) | [Prethodno poglavlje: Autorizacija](chapter4.md)| 


