# Regole del progetto Stream Grid

## ⚠️ DEPLOY OBBLIGATORIO (regola n.1)

**Dopo OGNI modifica ai file del sito fare SEMPRE il deploy.** Non considerare il lavoro
finito finché le modifiche non sono su GitHub e quindi pubblicate da Vercel.

I file del sito sono: `index.html`, `player.html`, `multiview.html`, `tutorial.html`,
`index.css`, `sw.js`, `manifest.json`, `vercel.json`.

Sequenza da eseguire sempre, in questo ordine:

```bash
git add .
git commit -m "Descrizione breve delle modifiche"
git push origin main
```

- **Non** serve `npx vercel`: il progetto è collegato a Vercel tramite GitHub e fa il deploy
  da solo non appena arriva la push sulla branch `main`.
- Se la push fallisce per conflitti: `git pull --rebase origin main` e poi riprovare la push.
- Dopo la push il sito si aggiorna in pochi secondi.
- Verificare sempre che il comando `git push` termini senza errori prima di dichiarare
  il lavoro completato.

## Struttura del progetto

| File | Ruolo |
|---|---|
| `index.html` | Home con la griglia dei canali (`normalChannels`, `fastChannels`, `digitaleChannels`) |
| `player.html` | Player: stesso elenco canali di index.html + funzioni `updateNormalStream`, `updateStream`, `updateDigitaleStream` |
| `multiview.html` | Multiview: 999 stream (`gomstream.xyz/live/stream-N.php`), 10 pagine da 100, max 10 preview live |
| `index.css` | Tutti gli stili. Quando cambia, aggiornare il versione nei link: `index.css?v=NN` |
| `sw.js` | Service worker. Quando cambia qualcosa, bumpare `CACHE_NAME` (`stream-grid-vN`) |
| `it.m3u` | Playlist IPTV |

**Importante:** `index.html` e `player.html` contengono copie uguali degli elenchi canali:
quando si aggiunge o modifica un canale bisogna aggiornarli **entrambi**.

## Note tecniche

- **Sandbox iframe**: alcuni host rifiutano l'iframe se ha l'attributo `sandbox`
  (Gomstream, SportyLive, dembed, OK.ru, MadPlay, nexa.st, kora.st). Per questi
  `updateNormalStream` rimuove il sandbox — non riaggiungerlo.
- Le fonti "raw" (`edge.cowedd4855ws.sbs/.../index.m3u8`, `vsd209.okcdn.ru/...`) rispondono
  403 se caricate fuori dal player di origine: usare sempre la pagina player in iframe.
- I link che passano da `https://href.li/?...` sono solo un mascheramento del referrer:
  l'URL originale è quello dopo `href.li/?`.
- Le API di madplay77 restituiscono JSON **codificato in ROT47** e richiedono un prefisso
  `sfver.../` prima di `/api/...` per aggirare Cloudflare (qualsiasi stringa `sfverXXX` funziona).
