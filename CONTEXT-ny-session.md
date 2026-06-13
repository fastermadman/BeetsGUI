# BeetsGUI → næste build: context til ny session

## Hvad er bygget (beetsGUIv2)

En single-file HTML GUI til beets (`beetsgui.html`) med:
- 7 tabs: Import, Bibliotek, Ryd op, Konverter, Find, Settings, Tjenester
- Dansk + engelsk (toggle knap)
- Drag-and-drop stier
- Live kommando-bygger → kopier-knap til Terminal
- Config.yaml-generator (kombinerer Settings + Auth)
- Auth: Discogs token, MusicBrainz bruger/kode, Beatport4 bruger/kode
- fd i stedet for find (hurtigere, ignorerer .Spotlight-V100 osv.)
- 3-trins dublet-workflow: Preview → Trash (~/.Trash/) → Permanent (kræver "SLET"-bekræftelse)
- Format-prioritet i config: flac > aiff > alac > wav > mp3
- iCloud-advarsel på sti-feltet
- importfeeds + dirfields plugins
- autotagger: strong_rec_thresh: 0.04

## Problemet med nuværende løsning

Copy-paste fra browser til Terminal er friktion. Brugeren mistede ~300GB musik pga. forkerte terminal-kommandoer (beet duplicates -d uden format-prioritet → mp3 vandt over flac/aiff).

## Hvad brugeren egentlig vil

**Klik på ikon i Dock → grafisk brugerflade åbner → klikker rundt → beets kører i baggrunden.**

Terminal må gerne køre i baggrunden, men må ikke være det brugeren interagerer med.

## Muligheder undersøgt

| Løsning | Vurdering |
|---------|-----------|
| TUI-plugin (`beet gui`) | Kræver stadig at åbne Terminal manuelt. Ikke godt nok. |
| AppleScript wrapper | Halvløsning — stadig browser + copy-paste |
| Electron | Virker, men 200MB overhead |
| **beets web-plugin + browser-app** | Beets har indbygget webserver. Kan pakkes som .app med AppleScript-launcher. |
| **Tauri-app** | Rust + WebView, rigtig .app-fil, bruger BeetsGUI HTML som UI, taler med beets web API |

## Anbefalet næste skridt til ny session

1. **Søg GitHub** efter:
   - eksisterende beets GUI-projekter (ikke TUI)
   - beets web-plugin eksempler
   - Tauri + beets
   - andre music library GUI-apps til inspiration

2. **Afklar arkitektur:**
   - beets `web`-plugin som backend API
   - BeetsGUI HTML (eller ny version) som frontend
   - Launcher-app der starter serveren og åbner vindue

3. **Byg prototype**

## Relevante filer

- `beetsgui.html` — nuværende HTML GUI
- `beetsgui-spec.md` — original spec
- `beetsgui-auth-spec.md` — auth spec
- `beets-guide.md` — beets-guide
- `beets-tui-plugin-ide.md` — tidligere TUI-ide (nu overhalet)
- `README.md` — dokumentation

## Beets web-plugin

Aktiveres med:
```yaml
plugins:
  - web
web:
  host: 127.0.0.1
  port: 8337
  readonly: no
```

Start: `beet web`
API: `http://localhost:8337/item/` osv.

Dokumentation: https://beets.readthedocs.io/en/stable/plugins/web.html
