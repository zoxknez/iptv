# Changelog

## 1.6.0 — 2026-07-16

### Novo — Magnet / Torrent streaming (Preuzimanja tab)
- Nova sekcija u Preuzimanja: nalepi magnet link ili učitaj `.torrent` fajl, izaberi video fajl (ako ih torrent sadrži više), pusti ga **dok se još preuzima** — MonoTorrent-ov `StreamProvider` daje lokalni HTTP stream sa prioritetizacijom delova oko trenutne pozicije čitanja, mpv ga konzumira kao svaki drugi URL. Titlovi i audio trake rade automatski (isti player put kao završena preuzimanja).
- **Pauza / Nastavi** po torrentu, **Pokušaj ponovo** posle greške (npr. nema odgovora peer-ova/DHT-a), **Ukloni**.
- Lista aktivnih torenta **preživljava restart aplikacije** (izvor + odabrani fajl se pamte; MonoTorrent hash-checkuje već preuzete delove umesto da ih skida ponovo).
- **Otvori folder** dugme (deljeni download direktorijum za snimke/VOD/torrente).
- P2P mreža (port/DHT/firewall) je odvojena od ostatka aplikacije — pad torrent motora nikad ne obara aplikaciju, sekcija se samo tiho onesposobi uz poruku.

### Redizajn — Preuzimanja tab
- Red snimaka/VOD preuzimanja dobio karticu (senka/okvir) i brojač stavki, dosledno sa Magnet/Torrent sekcijom iznad njega.
- Lista torenta ograničene visine sa sopstvenim skrolom — ne gura ostatak stranice kad ima više aktivnih torenta.

### Novo — TMDB radi odmah, bez podešavanja
- Aplikacija sada dolazi sa deljenim TMDB API ključem (podešavanja opisa/postera/glumaca su UVEK aktivna) — korisnik više ne mora sam da vadi i unosi sopstveni ključ da bi video obogaćene informacije o filmovima/serijama. Polje za sopstveni ključ u Podešavanjima ostaje (opciono, za sopstveni rate-limit).

### Novo — funkcionalni dodaci iz analize (Filmovi/Serije/Podešavanja)
- **Cast fotografije** sad prikazane i u stalnom info panelu na Filmovi/Serije (ranije se skidalo sa TMDB ali se prikazivalo SAMO u posebnom modalu).
- **Filteri po žanru + sortiranje** (naziv/godina/ocena/dodato) na Filmovi i Serije — podaci su postojali, ranije nigde izloženi kao kontrola.
- **Zaključane kategorije** — nova sekcija u Podešavanja → Roditeljska kontrola za otključavanje bez odlaska na Televizija tab (mirror postojeće "Sakriveno" sekcije).
- **Status izvora** (broj kanala + "pre X min") na svakoj kartici liste u Podešavanja → Liste i izvori.
- Potvrda pre brisanja liste (izvora) — ranije brisalo bez pitanja.

### Redizajn — dosledna elevacija/kartice na svim preostalim stranicama
- **Podešavanja**: ceo desni panel (svih 7 kategorija) sada u jednoj elevated kartici umesto potpuno ravnog izgleda; ikone dodate svakoj kategoriji u levoj navigaciji; potvrda pre brisanja liste (izvora).
- **EPG**: elevacija na toolbar/raspored/info panel; ilustrovano prazno stanje (ikona + tekst) umesto golog teksta.
- **Pretraga**: traka za pretragu i rezultati sada u elevated karticama (ranije jedina stranica bez ijedne kartice); ilustrovano stanje "ukucaj za pretragu".
- **Zakazano**: dobio karticu + brojač stavki (ranije bez ijednog Border/kartice).
- **Terminal**: strukturiran log obojen po ozbiljnosti (greške crveno, upozorenja žuto, ostalo zeleno) umesto monohromatskog teksta; dodat filter/pretraga kroz log i vidljiv prekidač za automatsko skrolovanje; elevacija.
- **Filmovi / Serije**: elevacija na sve panele (toolbar, lista, info); pravo prazno stanje (ikona + poruka) kad kategorija/pretraga ne vrati ništa; slomljena slika postera se sada vidno razlikuje od "još se učitava" (ranije je pulsirala zauvek identično).

## 1.5.0 — 2026-07-15

### Ispravke prijavljenih bagova
- **GPU render: naopaka slika ("u ogledalu")** — orijentacija deljene GL↔D3D teksture zavisi od drajvera grafičke kartice; novo podešavanje **Reprodukcija → "Okreni sliku po vertikali (GPU render)"** ispravlja sliku uživo, bez restarta.
- **Seckanje / 100% CPU**:
  - Softverski render više ne radi CPU skaliranje iznad rezolucije izvora (fullscreen 4K sa 1080p streamom = ~4× manje CPU posla po frejmu); doskaliranje radi grafička kartica.
  - Render nit se više ne blokira na UI niti (sinhroni `Invoke` → asinhroni uz spajanje notifikacija) — kraj mikro-seckanja kad je UI zauzet.
  - GPU put: uklonjen nepotreban `glFinish` stall po frejmu; ispravljen redosled primene dekodera (GPU mod uvek zero-copy `hwdec=auto`).
  - **HEVC/AV1 na starim grafikama**: aplikacija sada detektuje kad hardversko dekodiranje tiho padne na procesor, prikaže upozorenje (sa savetom da se izabere H.264 verzija kanala ako postoji) i automatski uključi CPU olakšice u dekoderu.
- **Fullscreen**: bez sistemskog okvira na svim sistemima (`WindowStyle=None` pri ulasku), prozor više nikad ne ostane "zaglavljen preko svega" (topmost samo dok je aplikacija aktivna — alt-tab i obaveštenja rade normalno), ispravljen race pri ulasku u fullscreen iz mini-plejera (crn/pogrešno dimenzionisan frejm).

### Novo
- **Plutajući PiP prozor** — dugme ⧉ u kontrolama plejera: video u malom always-on-top prozoru (premeštanje prevlačenjem, promena veličine, pauza/povratak na hover), glavni prozor se sklanja; zatvaranje vraća plejer u aplikaciju.
- **"Sada" u listi kanala** — naziv trenutne emisije + progres traka ispod imena kanala (TV i Omiljeni), iz postojećeg EPG keša, osvežava se na 30s.
- **O aplikaciji** blok u Podešavanja → Ažuriranja (verzija, autor, komponente) + **o0o0o0o** link ka portfoliju autora (title bar i Podešavanja).

### Vizuelni redizajn
- Inter font (pakovan, sa ćirilicom) za podrazumevani i tamni skin; jača tipografska skala (naslovi 24 SemiBold, sekcijske etikete).
- Navigacija: zaobljene "pill" stavke sa animiranim akcentnim indikatorom (BackEase) i hover slojem.
- Fade+slide tranzicija pri promeni taba (~150ms, preskočeno preko video površina).
- Fokus prsten na tastaturnu navigaciju (dugmad, checkbox, combo, nav) — pristupačnost + moderan izgled.
- Skeleton puls placeholderi za postere (Filmovi/Serije) dok se slike učitavaju.
- Status toast na "floating surface" podlozi sa senkom; hardkodovane boje prebačene u tokene teme; naslov prozora i brand u title baru prate stvarnu verziju (umesto zastarelog "1.1").

## 1.4.1 — 2026-07-15

### Auto-update
- Tiha provera nove verzije pri pokretanju aplikacije (pored postojeće ručne provere u Podešavanja → Ažuriranja) — ako je dostupna novija verzija, prikaže se tray obaveštenje. Podrazumevani manifest sad pokazuje na `update.json` u zasebnom javnom repozitorijumu [zoxknez/iptv](https://github.com/zoxknez/iptv) (dokumentacija + release-i su javni; izvorni kod ostaje privatan).

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
