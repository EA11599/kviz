# Predigra kviz — upute za postavljanje

Sve je već napravljeno u `index.html`. Ostaju ti samo dva koraka: **(A)** spojiti besplatnu bazu (Firebase) i **(B)** objaviti stranicu (Netlify). Traje otprilike 15-20 minuta.

---

## A) Firebase — besplatna baza za sinkronizaciju uživo

1. Idi na **console.firebase.google.com** i prijavi se Google računom.
2. Klikni **"Add project"** (Dodaj projekt). Upiši naziv, npr. `predigra-kviz`. Google Analytics nije potreban — možeš isključiti.
3. Kad se projekt otvori, u lijevom meniju idi na **Build > Realtime Database**.
4. Klikni **"Create Database"**. Odaberi lokaciju (bilo koja europska je ok), pa odaberi **"Start in test mode"** (to je važno — test mode dopušta čitanje/pisanje bez prijave, što nam treba za ovu igru). Klikni Enable.
5. Kad se baza otvori, gore desno klikni na **Rules** (Pravila) i zamijeni sadržaj s ovim:

   ```json
   {
     "rules": {
       ".read": true,
       ".write": true
     }
   }
   ```

   Klikni **Publish**. *(Napomena: ovo znači da je baza otvorena svima s linkom — sasvim ok za privatnu predigru s prijateljima, ali nemoj ovamo stavljati osjetljive podatke.)*

6. Sad idi na **project settings** (zupčanik gore lijevo pored "Project Overview") → **General**. Skroluj dolje do **"Your apps"** i klikni na ikonu **`</>`** (Web app). Daj mu ime (npr. "predigra web"), **ne treba** čekirati Firebase Hosting. Klikni **Register app**.
7. Firebase će ti prikazati kod s objektom `firebaseConfig` — nešto poput:

   ```js
   const firebaseConfig = {
     apiKey: "AIzaSy...",
     authDomain: "predigra-kviz.firebaseapp.com",
     databaseURL: "https://predigra-kviz-default-rtdb.europe-west1.firebasedatabase.app",
     projectId: "predigra-kviz",
     storageBucket: "predigra-kviz.appspot.com",
     messagingSenderId: "123456789",
     appId: "1:123456789:web:abcdef"
   };
   ```

8. Otvori `config.js` (mali zaseban fajl) u bilo kojem uređivaču teksta. Zamijeni placeholder vrijednosti svojim iz koraka 7:

   ```js
   const firebaseConfig = {
     apiKey: "TU_UPISI_apiKey",
     authDomain: "TU_UPISI_authDomain",
     databaseURL: "TU_UPISI_databaseURL",
     ...
   };
   ```

   Ovo je **jedino mjesto** gdje trebaš zalijepiti config — i `index.html` i `admin.html` ga automatski čitaju odavde.

9. Spremi datoteku.

---

## B) Netlify — objava stranice (besplatan link)

1. Idi na **app.netlify.com** i napravi besplatan račun (može preko Googlea, ne treba GitHub).
2. Kad uđeš u Netlify, vidjet ćeš područje na koje piše **"Drag and drop your site output folder here"**.
3. Uzmi folder `predigra-web` (u kojem je `index.html`) i odvuci ga direktno u taj prozor u browseru.
4. Za par sekundi Netlify će ti dati link, nešto poput `https://random-name-12345.netlify.app`. To je tvoja stranica — radi odmah, svugdje.
5. *(Opcionalno)* Klikni **"Site settings" → "Change site name"** da dobiješ ljepši link, npr. `https://predigra-kviz.netlify.app`.

To je to — pošalji taj link ekipi i igra može početi.

---

## C) Kasnije: vlastita domena

Kad budeš spreman/na:
1. Kupi domenu (npr. na Namecheap, GoDaddy, ili preko samog Netlifyja) — oko 10-15€/god.
2. U Netlifyju idi na **Site settings → Domain management → Add a domain**.
3. Netlify će ti dati DNS zapise koje upišeš kod svog registratora domene (obično traje do par sati da profunkcionira).

Javi mi kad dođeš do ovog koraka, provest ću te kroz to.

---

## D) Baza pitanja — admin.html

Sad kad postoji `admin.html`, pitanja više nisu zakucana u kodu — žive u Firebase bazi, pa možeš dodavati nove kvizove (teme) kad god poželiš, bez ikakvog editiranja koda.

**Prvi koraci:**

1. `admin.html` i `index.html` dijele isti `config.js` fajl — config lijepiš samo jednom (vidi korak A8 gore), obje stranice ga automatski koriste.
2. Odvuci **cijeli folder** (`index.html`, `admin.html`, `config.js` zajedno) u Netlify kao i prije — sve stranice će raditi na istoj domeni, npr:
   - `https://tvoja-domena.com/` → igra
   - `https://tvoja-domena.com/admin.html` → admin panel
3. Otvori `admin.html`. Ako je baza prazna, vidjet ćeš gumb **"Napuni bazu s početnim kvizom"** — klikni ga da dobiješ postojećih 22 pitanja opće kulture kao prvi kviz.
4. Za novi kviz: upiši naziv teme (npr. "Filmovi i glazba") i klikni **Kreiraj**, zatim dodaj pitanja jedno po jedno — biraš tip (višestruki izbor ili procjena broja), upišeš tekst i točan odgovor.
5. U samoj igri (`index.html`), voditelj sad u lobiju bira koji kviz se igra prije nego što klikne "Pokreni kviz".

**Napomena o sigurnosti:** `admin.html` trenutno nema lozinku — svatko s linkom može uređivati pitanja. Za igru s prijateljima to je sasvim ok (link ionako ne dijeliš javno), ali nemoj ga objavljivati na vidljivom mjestu. Ako kasnije poželiš zaštitu lozinkom, javi mi — to je brz dodatak.

---

## E) Predložak za pitanja i nasumično generiranje

**Predložak je proširen — sad podržava 4 tipa pitanja**, ne samo dva. Nova struktura ima 3 kartice:
- **Upute** — objašnjava sve stupce
- **Formati** — referentna tablica sa svim tipovima pitanja i njihovim bodovanjem (samo za čitanje)
- **Pitanja** — tu upisuješ svoja pitanja, jedan red po pitanju

**4 tipa pitanja i bodovanje:**

| Tip (upiši u stupac "tip") | Kako radi | Bodovanje |
|---|---|---|
| `abcd` | Višestruki izbor, 4 ponuđena odgovora | 1 bod točno, 0 netočno |
| `klasicno` | Igrač upisuje odgovor slobodnim tekstom | 1 bod točno / 0 netočno (ili 3 / -1 ako je `posebna_kategorija` = DA) |
| `broj` | Procjena broja, najbliži pobjeđuje | Max bodova = broj sudionika, pada za 1 po rangu |
| `natuknice` | 5 tragova se otvara redom uživo, prvi točan pogodak pobjeđuje | 5 bod. na 1. natuknici, 4 na 2., 3 na 3., 2 na 4., 1 na svih 5 |

Za `klasicno` i `natuknice` (slobodan tekst) — sustav ponudi automatski prijedlog (podudaranje teksta), ali **voditelj uvijek ima zadnju riječ**: na ekranu s rezultatima klikom mijenja bilo čiji odgovor iz točnog u netočan i obrnuto, prije nego krene na sljedeće pitanje.

**Kako popuniti predložak:**

1. Preuzmi `predlozak_pitanja.xlsx` (uz ostale fajlove, ili s tvoje stranice na `/predlozak_pitanja.xlsx`).
2. Kartica "Formati" ti služi kao podsjetnik dok popunjavaš "Pitanja" - ne moraš je mijenjati.
3. U "Pitanja" upiši svoja pitanja. Za `abcd`: popuni opcijeA-D i tocno (1-4). Za `broj`: popuni tocno (broj) i po želji jedinicu. Za `klasicno`: popuni tocno (tekst) i po želji `posebna_kategorija` (DA/NE). Za `natuknice`: popuni svih 5 natuknica (od najteže do najlakše) i tocno (konačan odgovor).
4. Spremi i uvezi u `admin.html` kao i do sad.

**Kako izgleda "natuknice" tip uživo:** voditelj na svom ekranu ima gumb "Otvori sljedeću natuknicu 🔍" — svaki klik otkriva sljedeći trag svima uživo (i na velikom ekranu i na mobitelima). Igrači mogu poslati pogodak u bilo kojem trenutku; broj bodova ovisi o tome koliko je natuknica bilo otvoreno kad su odgovorili.

---

## Ako nešto zapne

- **Igrači se ne vide jedni drugima** → provjeri jesi li stvarno zalijepio/la svoj `firebaseConfig` (a ne ostavio placeholder tekst), i jesi li kliknuo Publish na Rules u koraku A5.
- **Stranica je bijela/prazna** → otvori Developer Console u browseru (F12 → tab "Console") i pošalji mi grešku koju vidiš, brzo ću skužiti o čemu se radi.
- **Želiš izmijeniti pitanja ili dizajn** → sve je u `index.html`, javi mi što želiš promijeniti pa ću ti dati točan kod za zamjenu.
