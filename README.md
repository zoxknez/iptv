# Super IPTV

Windows desktop IPTV plejer (Xtream Codes / Stalker Portal / M3U) — uživo TV, omiljeni, EPG vodič, filmovi/serije, globalna pretraga, red preuzimanja/snimanja, GPU-ubrzano renderovanje videa, više skinova, srpski jezik (latinica + ćirilica).

Ovaj repozitorijum sadrži **samo dokumentaciju izmena i gotove release build-ove** — izvorni kod se razvija u zasebnom privatnom repozitorijumu. Aplikacija koristi ovaj repo za proveru novih verzija (`update.json`).

## Preuzimanje

Najnovija verzija: [Releases](../../releases/latest) — preuzmi `SuperIPTV-x.x.x-win-x64-portable.zip`, raspakuj bilo gde i pokreni `SuperIPTV.exe`. Nema instalacije; native zavisnosti (mpv/ffmpeg/yt-dlp) su uključene u arhivu.

## Šta je novo

Vidi [CHANGELOG.md](CHANGELOG.md) za detaljnu listu izmena po verzijama.

## Automatsko ažuriranje

Aplikacija pri pokretanju tiho proverava `update.json` iz ovog repozitorijuma i obaveštava (tray toast) ako je dostupna novija verzija. Preuzimanje/instalacija ostaje ručno, iz bezbednosnih razloga.
