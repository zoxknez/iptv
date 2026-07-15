# Changelog

## 1.4.1 — 2026-07-15

### Auto-update
- Tiha provera nove verzije pri pokretanju aplikacije (pored postojeće ručne provere u Podešavanja → Ažuriranja) — ako je dostupna novija verzija, prikaže se tray obaveštenje. Podrazumevani manifest sad pokazuje na `update.json` u GitHub repou (ažurira se uz svaki Release).

## 1.4.0 — 2026-07-15

### Duboka analiza i popravke, stranica po stranicu
- Televizija, Omiljeni, EPG, Filmovi, Serije, Pretraga, Preuzimanja/Zakazano, Terminal, Podešavanja — dosledna pretraga (jedinstven stil, dugme za brisanje), volume slider vidljiv po difoltu, prazna stanja, ispravke default kategorije (VOD/Serije više ne otvaraju praznu Listu želja), watched-status ispravka po izvoru, race-condition ispravka u pretrazi, batch dodavanje sezone u red preuzimanja.

### Čišćenje mrtvog koda i refaktor
- Uklonjeni neiskorišćeni event-ovi, komande, konverter i Loc ključevi (potvrđeno nula referenci pre brisanja).
- Konsolidovana duplirana render/mini-player logika iz 5 view fajlova (Televizija/Favoriti/Filmovi/Serije/EPG) u deljene helper klase (`MiniPlayerRenderSink`, `FullPlayerRenderSink`, `TracksPopupController`, `CategoryExpanderController`).
- Ispravljen mojibake bag i poslednje `nullable` upozorenje u build-u.

### Vizuelni "premium" prolaz
- Animirani hover/press prelazi kroz celu aplikaciju (dugmad, navigacija, checkbox).
- Doslednija primena senki/dubine na modalima.
- Nova tipografska skala (naslovi stranica, sekcijske etikete).
- Vektorske ikone u navigaciji umesto emoji-ja.

## 1.3 — 2026-07-15

### Performanse (KRITIČNO — 100% CPU na nekim kanalima)
- **Novi GPU render put preko D3DImage**: mpv OpenGL render API → deljena D3D9Ex tekstura (WGL_NV_DX_interop2) → WPF `D3DImage`, sa pravim zero-copy hardverskim dekodiranjem (`hwdec=auto`, npr. `d3d11va`/`nvdec` bez `-copy` readback-a). Uklanja CPU YUV→RGB konverziju i memcpy po frejmu koji je pravio softverski put. Izmereno na realnom kanalu: niži CPU nego u softverskom modu; dobitak raste sa rezolucijom striminga (najveći na HD/4K/HEVC kanalima — upravo tim koji su prijavljivali 100% CPU).
- Novo podešavanje **Reprodukcija → Render videa: Softverski / GPU**. Podrazumevano ostaje **Softverski** (uvek radi, nula rizika) — GPU je opcioni, a promena se primenjuje posle ponovnog pokretanja aplikacije. Ako GPU inicijalizacija ikad zakaže na nekom sistemu, aplikacija automatski pada nazad na softverski render (nikad ne ostaje bez slike).
- GPU put pokriva sve prikaze videa (Televizija, Omiljeni, Filmovi, Serije, EPG vodič mini-plejer) uključujući OSD slojeve (kao WPF overlay iznad D3DImage-a).

### Podešavanja — reorganizacija
- Podešavanja sada imaju **levu navigaciju kategorija** umesto jednog dugog spiska: Liste i izvori · Reprodukcija · Interfejs · Roditeljska kontrola i privatnost · Preuzimanja i mediji · Sistem · Ažuriranja. Sva postojeća polja i dugmad rade identično kao pre — samo su preglednije grupisana.

### MAC portal (Stalker/MAG) login
- Preimenovano iz "Stalker Portal (eksperimentalno)" u **"MAC portal (Stalker/MAG)"**, sa blažom (informativnom, ne alarmantnom) napomenom o ograničenjima.
- MAC adresa se sada **automatski formatira dok kucaš** (velika slova, dvotačke), uz dugme **"🎲 Generiši MAC"** (nasumična adresa sa MAG prefiksom 00:1A:79) i upozorenje ako format nije validan.

### Interna arhitektura (bez promene ponašanja)
- `MainViewModel.cs` (7400+ linija) podeljen u partial fajlove po funkcionalnoj oblasti (`.Tv/.Vod/.Series/.Epg/.Downloads/.Search/.Player/.Osd/.Sources/.Settings.cs`) radi lakšeg održavanja.
- Settings handleri iz `MainWindow.xaml.cs` izdvojeni u `MainWindow.Settings.cs`.

### Stabilnost
- Ispravljen pad cele aplikacije kad Xtream/MAC server ne odgovori na vreme (mrežni timeout/DNS greška pri učitavanju kanala, filmova ili serija). Umesto pada, greška se sad prikazuje korisniku (status poruka) ili tiho loguje (pozadinsko osvežavanje kataloga), a aplikacija nastavlja da radi.

## 1.2.1 — 2026-07-14

### Stabilnost (otkriveno testiranjem sa pravom listom)
- Ispravljen pad cele aplikacije pri BILO KOM pozivu ka mpv-u (ne samo pri pokretanju) kad mpv-2.dll nedostaje ili nije validan — centralna zaštita u MpvInterop umesto pojedinačnih provera po funkciji.
- tools/fetch-deps.ps1: dodat UTF-8 BOM (Windows PowerShell 5.1 bez BOM-a pogrešno dekodira dijakritike u komentarima što je rušilo parsiranje cele skripte).

### Automatsko preuzimanje/ažuriranje komponenti
- **mpv-2.dll se sada automatski preuzima i ažurira kod korisnika**, isto kao ffmpeg i yt-dlp do sada — bez potrebe za ručnim pokretanjem fetch-deps.ps1 ili instaliranim 7-Zip-om (raspakivanje ide preko SharpCompress, čisto u .NET-u). Izvor: isti shinchiro/mpv-winbuild-cmake build koji koristi i dev skripta, radi verzijske konzistencije.
- Ako je mpv nedostajao pri pokretanju aplikacije, a stigne kasnije (auto-preuzimanje u pozadini), player se sada automatski ponovo inicijalizuje BEZ potrebe za restartom aplikacije.
- Prikaz verzije mpv-a dodat u Podešavanja → Ažuriranja (pored ffmpeg i yt-dlp).

## 1.2 — 2026-07-14

### Jezik
- Aplikacija je sada isključivo na srpskom jeziku: latinica i ćirilica (Podešavanja → Pismo). Ćirilica se generiše automatskom transliteracijom latinične tabele (tehnički pojmovi i brendovi ostaju nepromenjeni). Svi ostali jezici i njihove tabele prevoda su uklonjeni.
- Lokalizovane preostale poruke koje su ranije bile hardkodovane (Xtream prijava, FFMPEG terminal, "aplikacija je već pokrenuta", nedostatak mpv-2.dll…).

### Player
- Odnos slike / zum (ciklus Auto → 16:9 → 4:3 → Zum → Rastegni), prečica u mapiranju tastera.
- Normalizacija jačine zvuka (noćni režim, dynaudnorm) — uključivo u Podešavanjima.
- Podesivo kašnjenje zvuka i titlova (±0,1 s), sa OSD prikazom.
- Snimak ekrana (bez OSD-a) u mapu preuzimanja.
- Učitavanje spoljnog titla (.srt/.ass/.sub) iz datoteke — dugme u audio/titl meniju (TV, favoriti, filmovi, serije).
- Statistika strima (rezolucija, FPS, bitrejt, ispušteni frejmovi, keš, dekoder) — OSD panel, prečica u mapiranju tastera.
- Tajmer za spavanje (30/60/90/120 min → zaustavlja reprodukciju).

### Televizija
- Biranje kanala kucanjem broja (kao na STB-u/daljinskom), sa OSD potvrdom.
- „Flip“ — prečica na prethodno gledani kanal (TV ili favorit).
- Upozorenje na istek pretplate (Xtream) kad ističe za ≤ 7 dana.
- Pauza živog kanala sa arhivom automatski prelazi u timeshift od trenutka pauze (umesto da preskoči na uživo).

### Nastavak gledanja
- Filmovi, epizode i lokalne snimke pamte poziciju gledanja i nude nastavak od tog mesta.

### Roditeljska kontrola
- PIN u Podešavanjima; zaključavanje kategorija (TV/filmovi/serije) desnim klikom — sakrivene dok se ne otključaju PIN-om (do izlaska iz aplikacije).

### Podsetnici za emisije
- ⏰ u EPG vodiču na budućim emisijama — obaveštenje (i auto-prebacivanje ako se gleda uživo TV) kad emisija počne.

### Uređivanje kanala i kategorija
- „Uredi kanal“ dobija preimenovanje prikaza i sakrivanje pojedinačnog kanala.
- Trajno sakrivanje celih kategorija (desni klik na naslov kategorije u TV listi) — bez PIN-a, čisto radi preglednosti.
- Novi panel „Sakriveno“ u Podešavanjima za pregled i vraćanje sakrivenih kanala/kategorija.

### Lista želja i praćenje serija
- ★ Lista želja za filmove i serije (poseban folder na vrhu liste kategorija).
- Automatsko pokretanje sledeće epizode uz odbrojavanje i mogućnost otkazivanja.
- 🔔 Praćenje serija — obaveštenje kad provajder doda novu epizodu/sezonu.

### Provera kanala
- Ručna, sekvencijalna provera koji kanali rade (🔍 pored liste kanala) — sivi marker i oznaka „NE RADI“ za mrtve kanale.

### Sistem
- Izvoz/uvoz svih podešavanja (liste, favoriti, mapiranja, PIN, watchlist, praćene serije, red preuzimanja) u jednu .zip datoteku.
- Toast/tray obaveštenja za završeno/neuspelo preuzimanje i početak zakazanog snimanja.
- Napredak preuzimanja na traci zadataka (taskbar).
- Pokretanje sa Windows-om (minimizovano u tray).
- Priprema za samostalno ažuriranje aplikacije: opcioni URL manifesta u Podešavanjima za proveru nove verzije (preuzimanje ostaje ručno, iz bezbednosnih razloga).

### Izvori
- Stalker Portal kao treći tip izvora (eksperimentalno — samo živi TV kanali, bez filmova/serija/EPG-a).

### Stabilnost
- Ispravljen pad cele aplikacije kad nedostaje mpv-2.dll (npr. sveže preuzet kod bez pokrenutog fetch-deps.ps1) — sada se prikazuje jasna poruka umesto rušenja.

## 1.1 — 2026-07-13

### Zvuk
- Glasnoća aplikacije (0–100) neovisna o sistemskom mikseru: slider u kontrolama playera (i mini-player), pamti se u konfiguraciji.
- Mute gumb s pamćenjem stanja (glasnoća se čuva neovisno o mute-u).
- Izbor audio izlaza (zvučnici / HDMI / slušalice…) u postavkama — mpv WASAPI uređaji, prebacivanje uživo, bez diranja sistemskih postavki; "Zadano" prati Windows default.

### Globalna pretraga
- "Sve liste" sada vraća rezultate sa SVIH izvora: limit od 300 rezultata po grupi vrijedi po izvoru (prije su prve liste potrošile globalni limit pa se 3.+ lista nije vidjela). Bez deduplikacije preko izvora — isti naslov s različitih izvora su odvojeni rezultati.
- Rezultati su jedna virtualizirana lista (zaglavlja grupa + retci u istom scrollu): nema smrzavanja kod tisuća rezultata, prirodno skrolanje kotačićem kroz sve grupe, puna visina ekrana i kad je otvorena samo jedna grupa.
- Ispravljen pad aplikacije pri skrolanju velikih rezultata (recikliranje WPF kontejnera preko različitih tipova redaka).

### EPG
- Satni EPG refresh se sada stvarno pokreće (timer se prije nikad nije startao) i osvježava EPG SVIH izvora + globalni (fallback) XMLTV, jedan po jedan (bez flooda providera). Puni refresh listi ostaje na 12 h.

### Alati / build
- fetch-deps.ps1: ffmpeg se uvijek osvježava iz gyan release-essentials builda (isti izvor kao in-app updater → nema lažnog updatea na prvom pokretanju).
- Dokumentirane upute za publish edicija u .csproj (publish-editions.ps1, fiksne izlazne mape, bez brisanja).

## 1.0

- Prva verzija: .NET 10 / WPF / libmpv port (TV + catchup, EPG vodič, VOD/serije, favoriti, snimanje/preuzimanje, multi-provider, skinovi, 10 jezika, UI skala).
