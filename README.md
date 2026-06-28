# WaveIQ Radio

![WaveIQ Radio logo](assets/waveiq_logo.png)

## Android Production Architecture Showcase

This repository documents the architecture, product scope, and production design
decisions behind WaveIQ Radio, an AI-assisted Android internet radio application.

WaveIQ Radio helps listeners find, preview, save, understand, and rediscover
radio stations from a large global catalogue. The app combines reliable live
stream playback, smart discovery, listening-history intelligence, stream health
signals, Google Drive backup/restore, and Android Auto-friendly playback
behavior.

This is a portfolio and architecture showcase. The full production
implementation, backend internals, credentials, and proprietary ranking logic
remain private.

## Project Goals

- Build a modern Android internet radio app that feels fast, useful, and stable
  in daily listening.
- Keep the core station library local-first: saved stations, favorites, settings,
  listening history, and station logos are owned by the device unless the user
  chooses online features.
- Use AI as a discovery and explanation layer, not as a dependency for ordinary
  playback.
- Support a global station catalogue with smart search by station name, genre,
  country, language, and natural-language intent.
- Make playback resilient against weak Wi-Fi, stream stalls, phone calls, audio
  focus changes, screen-off behavior, and cellular fallback situations.
- Provide production-ready Android UX with Jetpack Compose, Material 3, Media3,
  background service playback, localization, sleep timer, backup/restore,
  Play-ready review/update flows, and Android Auto-oriented audio behavior.
- Protect backend-assisted AI and discovery flows with server-side trust
  boundaries, Firebase App Check, Play Integrity, rate limiting, and structured
  validation.

## Architecture Overview

WaveIQ Radio follows a layered production architecture:

- UI layer: Jetpack Compose screens, Material 3 components, a focused playback
  surface, station cards, discovery views, recommendations, similar stations,
  settings, backup controls, and small-screen compatibility work.
- Playback layer: Media3-based live radio playback, foreground service control,
  audio focus handling, reconnect policies, buffering watchdogs, now-playing
  state, sleep timer, and stream-health feedback.
- State layer: ViewModels, Kotlin Flow / StateFlow, explicit UI state models,
  repository boundaries, lifecycle-aware collection, and deterministic station
  selection rules.
- Local data layer: DataStore for saved stations and preferences, Room for
  listening history/statistics, local logo storage, backup models, and migration
  schemas.
- Discovery layer: RadioBrowser-style station lookup, local station catalogue
  fallback, stream playability signals, preview playback, deduplication, and
  explicit search modes.
- AI/backend layer: FastAPI services for search planning, recommendations,
  similar-station discovery, station explanation, station DNA enrichment, stream
  health processing, and usage safeguards.
- Cloud/backup layer: user-authorized Google Drive backup and restore for saved
  stations, settings, and logos, with manual and automatic controls.
- Security layer: Firebase App Check, Google Play Integrity, protected backend
  endpoints, server-side quota/rate controls, and careful separation between
  local state and network-assisted intelligence.

Detailed documentation:

- [Architecture Overview](architecture-overview.md)
- [Data Layer Design](architecture-data-layer.md)
- [UI, Playback & State Management](architecture-ui-layer.md)
- [Privacy & Security](architecture-privacy.md)
- [Pseudocode Samples](architecture-pseudocode.md)

## Key User-Facing Features

- Live internet radio playback with saved stations and favorites.
- Browse and search stations by name, genre, country, or language.
- AI-assisted smart search from natural prompts.
- Preview playback before saving a station.
- Personalized recommendations based on listening taste and station signals.
- Similar stations for finding alternatives to a station the user already likes.
- Similar-station tuning options such as less talk, more energy, and calmer.
- "Why this station?" style explanations where enough station data exists.
- Station cards with logos or consistent placeholders.
- Swipe delete, undo support, edit flows, and deterministic active-station
  handling.
- Sleep timer with fade-out behavior.
- Playback recovery for weak Wi-Fi, long stream cuts, stuck buffering, and
  network transitions.
- Android Auto-friendly playback and audio focus behavior.
- Listening history and local taste signals.
- Google Drive backup/restore for saved stations, settings, and station logos.
- Settings, language selection, app sharing, review entry point, privacy link,
  support contact, and version information.

## AI & Discovery Features

AI functionality is implemented as a protected optional layer around discovery,
recommendation, and explanation. It does not replace ordinary playback.

Client-side flows include:

- smart station search from natural-language prompts
- localized search explanations
- recommendation lists based on listening patterns and station metadata
- similar-station discovery from a selected station
- explain-station dialogs and recommendation reasoning where data coverage is
  sufficient

Backend-side responsibilities include:

- protected FastAPI endpoints
- station search planning
- recommendation and similar-station ranking
- station DNA generation and enrichment
- stream reliability signals
- AI usage event tracking
- server-side validation, quotas, and endpoint protection

This keeps AI useful, cost-aware, and bounded by backend controls instead of
client-side trust assumptions.

## Playback Reliability

WaveIQ Radio treats playback reliability as a first-class architecture concern.
The app includes recovery policies and QA matrices for situations such as:

- weak Wi-Fi and cellular fallback
- network loss and network return
- stream stall and stuck buffering detection
- long stream interruption recovery
- phone call / audio focus interruption
- same-station reconnect behavior
- preview playback versus main playback separation
- foreground service lifecycle and notification control
- screen-off and background playback behavior

The goal is not just to start a stream, but to keep live radio understandable and
recoverable when real-world mobile networks behave badly.

## Current Feature Set

- Android app built with Kotlin, Jetpack Compose, Material 3, Media3, Room,
  DataStore, WorkManager-style background scheduling, Firebase integrations, and
  Google Play services.
- Python/FastAPI backend with SQLAlchemy, MariaDB-compatible persistence,
  Firebase Admin verification, OpenAI integration, async HTTP clients, and
  stream-health workers.
- Large station catalogue workflow with local catalogue fallback and more than
  10,000 working station entries documented in the production architecture.
- AI search, recommendations, similar stations, station explanations, station
  DNA, and now-playing enrichment work.
- Google Drive backup/restore through user-authorized Google sign-in.
- Multi-language UI currently documented for English, Finnish, Dutch, Polish,
  Portuguese, and Turkish.
- Google Play readiness work including privacy/support links, in-app review,
  flexible updates, signed release workflow, and production OAuth configuration.

## Privacy & Trust Boundaries

- Core listening features store saved stations, favorites, settings, history,
  and station logos locally.
- Network-assisted features are separated from the local listening core.
- AI search, recommendations, similar stations, stream health, analytics, and
  backup require network services.
- Google Drive backup is user-authorized and should be described as backup and
  restore, not real-time cross-device sync.
- Backend endpoints are protected by App Check / Play Integrity style controls
  and server-side validation.
- Public marketing copy should avoid exposing internal endpoints, table names,
  scoring fields, provider internals, quotas, security implementation details,
  or unfinished experiments.

## Production Status

- Android production app architecture is implemented in the private source
  project.
- Google Drive OAuth is configured for production use.
- Google Drive backup is documented as working in closed testing and ready for
  production release.
- Playback, discovery, recommendations, similar stations, backup/restore,
  localization, privacy/support links, and Google Play readiness work are
  documented.

## Source Code Notice

This repository is maintained as a portfolio and architecture showcase. It does
not publish the full private production source code, proprietary backend logic,
release credentials, private API keys, production secrets, ranking weights, or
security-sensitive endpoint implementation details.

Architecture descriptions and selective pseudocode snippets are provided for
technical evaluation purposes.

© 2026 Janstech

---

# WaveIQ Radio

![WaveIQ Radio -logo](assets/waveiq_logo.png)

## Android-tuotantosovelluksen arkkitehtuuriesittely

Tämä repositorio dokumentoi WaveIQ Radio -Android-sovelluksen arkkitehtuurin,
tuotetason ja tuotantotason suunnitteluratkaisut.

WaveIQ Radio auttaa käyttäjää löytämään, esikuuntelemaan, tallentamaan,
ymmärtämään ja löytämään uudelleen internet-radioasemia laajasta globaalista
asemakatalogista. Sovellus yhdistää luotettavan live-toiston, älykkään haun,
kuunteluhistoriaan perustuvat suositukset, streamien toimintakuntosignaalit,
Google Drive -varmuuskopioinnin ja Android Auto -henkisen toistokäytöksen.

Tämä on portfolio- ja arkkitehtuuriesittely. Koko tuotantototeutus,
backendin sisäiset yksityiskohdat, avaimet ja omistettu ranking-logiikka
pidetään yksityisinä.

## Projektin tavoitteet

- Rakentaa moderni Android-internet-radio, joka tuntuu nopealta, hyödylliseltä
  ja vakaalta arjen kuuntelussa.
- Pitää käyttäjän oma asemakirjasto local-first-periaatteella: tallennetut
  asemat, suosikit, asetukset, kuunteluhistoria ja asemalogot ovat laitteen
  omistamaa dataa, ellei käyttäjä valitse verkkotoimintoja.
- Käyttää AI:ta haku-, suositus- ja selityskerroksena, ei tavallisen toiston
  pakollisena riippuvuutena.
- Tukea globaalia asemakatalogia asemannimen, genren, maan, kielen ja
  luonnollisen hakuintention perusteella.
- Tehdä toistosta palautuvaa heikossa Wi-Fi:ssä, streamien katkeillessa,
  puheluiden ja audio focus -muutosten aikana sekä mobiilidataan vaihdettaessa.
- Tarjota tuotantovalmis Android-kokemus Jetpack Composella, Material 3:lla,
  Media3:lla, taustatoistolla, lokalisaatiolla, uniajastimella,
  varmuuskopioinnilla, Play-valmiilla review/update-polulla ja Android Auto
  -yhteensopivalla toistologiikalla.
- Suojata backend-avusteiset AI- ja discovery-polut palvelinpuolen
  luottamusrajoilla, Firebase App Checkillä, Play Integrityllä,
  rate limitingillä ja validoiduilla vastauksilla.

## Arkkitehtuurin yleiskuva

WaveIQ Radio noudattaa kerroksellista tuotantoarkkitehtuuria:

- UI-kerros: Jetpack Compose -näkymät, Material 3 -komponentit, selkeä
  toistopinta, asemakortit, discovery-näkymät, suositukset, samankaltaiset
  asemat, asetukset, backup-kontrollit ja pienien näyttöjen huomiointi.
- Toistokerros: Media3-pohjainen live-radio, foreground service -ohjaus,
  audio focus -käsittely, reconnect-politiikat, buffering watchdogit,
  now-playing-tila, uniajastin ja stream health -palaute.
- Tilanhallinta: ViewModelit, Kotlin Flow / StateFlow, eksplisiittiset UI-tilat,
  repository-rajat, lifecycle-aware-keräys ja deterministiset asemanvaihdot.
- Paikallinen datakerros: DataStore tallennetuille asemille ja asetuksille,
  Room kuunteluhistorialle ja tilastoille, paikallinen logotallennus,
  backup-mallit ja migraatioskeemat.
- Discovery-kerros: RadioBrowser-tyylinen asemien haku, paikallinen
  asemakatalogifallback, streamien toimivuussignaalit, preview-toisto,
  deduplikaatio ja eksplisiittiset hakutilat.
- AI/backend-kerros: FastAPI-palvelut search planningiin, suosituksiin,
  samankaltaisiin asemiin, asemaselityksiin, station DNA -rikastukseen,
  stream health -prosessointiin ja käyttörajoihin.
- Pilvi/backup-kerros: käyttäjän valtuuttama Google Drive -varmuuskopiointi ja
  palautus tallennetuille asemille, asetuksille ja logoille.
- Turvakerros: Firebase App Check, Google Play Integrity, suojatut backend-
  endpointit, palvelinpuolen quota/rate-kontrollit ja selkeä ero paikallisen
  tilan sekä verkkoavusteisen älyn välillä.

Tarkemmat dokumentit:

- [Architecture Overview](architecture-overview.md)
- [Data Layer Design](architecture-data-layer.md)
- [UI, Playback & State Management](architecture-ui-layer.md)
- [Privacy & Security](architecture-privacy.md)
- [Pseudocode Samples](architecture-pseudocode.md)

## Keskeiset käyttäjäominaisuudet

- Live-internet-radion toisto tallennetuilla asemilla ja suosikeilla.
- Asemien selaus ja haku nimellä, genrellä, maalla tai kielellä.
- AI-avusteinen smart search luonnollisista hakupyynnöistä.
- Aseman esikuuntelu ennen tallentamista.
- Henkilökohtaiset suositukset kuuntelumaun ja asemasignaalien perusteella.
- Samankaltaiset asemat käyttäjän pitämän aseman pohjalta.
- Similar-toiminnon säätötilat kuten vähemmän puhetta, enemmän energiaa ja
  rauhallisempi tunnelma.
- "Miksi tämä asema?" -tyyppiset selitykset, kun aseman data riittää siihen.
- Asemakortit logoilla tai johdonmukaisilla placeholder-kuvilla.
- Swipe delete, undo, muokkauspolut ja deterministinen aktiivisen aseman hallinta.
- Uniajastin fade-out-käytöksellä.
- Toiston palautuminen heikossa Wi-Fi:ssä, pitkissä katkoksissa,
  jumittuneessa bufferoinnissa ja verkkosiirtymissä.
- Android Auto -ystävällinen toisto ja audio focus -käytös.
- Kuunteluhistoria ja paikalliset makusignaalit.
- Google Drive -varmuuskopiointi ja palautus asemille, asetuksille ja logoille.
- Asetukset, kielivalinta, jakaminen, arvostelupolku, tietosuojalinkki,
  tukiyhteys ja versiotiedot.

## Tuotantotilanne

- Android-tuotantosovelluksen arkkitehtuuri on toteutettu yksityisessä
  lähdeprojektissa.
- Google Drive OAuth on konfiguroitu tuotantokäyttöön.
- Google Drive -varmuuskopiointi on dokumentoitu toimivaksi closed testingissä
  ja valmiiksi tuotantojulkaisuun.
- Toisto, discovery, suositukset, samankaltaiset asemat, backup/restore,
  lokalisaatio, tietosuoja-/tukilinkit ja Google Play -valmius on dokumentoitu.

## Lähdekoodihuomautus

Tätä repositoriota ylläpidetään portfolio- ja arkkitehtuuriesittelynä. Se ei
julkaise koko yksityistä tuotantolähdekoodia, omistettua backend-logiikkaa,
julkaisuavaimia, yksityisiä API-avaimia, tuotantosalaisuuksia, ranking-painoja
tai turvallisuusherkkiä endpoint-toteutuksia.

Arkkitehtuurikuvauksia ja valikoituja pseudokoodiesimerkkejä tarjotaan teknistä
arviointia varten.

© 2026 Janstech
