# Architecture Overview

WaveIQ Radio is built as a local-first Android internet radio app with optional
network-assisted discovery, AI, stream-health, and backup layers. Ordinary
playback and the user's saved station library are kept separate from backend
intelligence so that the app remains understandable, recoverable, and privacy
aware.

## Layers

### UI Layer

- Jetpack Compose
- Material 3 components
- station cards, discovery lists, recommendation surfaces, dialogs, settings,
  backup controls, and expanded playback views
- keyboard, inset, scroll, small-screen, and older-device compatibility work
- localized UI strings and in-app language selection

### Playback Layer

- Media3-based live stream playback
- foreground service lifecycle
- notification and media-session control
- audio focus handling for calls and interruptions
- buffering watchdogs and reconnect status
- sleep timer and fade-out behavior
- separation between preview playback and main playback

### State & Application Logic

- Android ViewModels
- Kotlin Flow / StateFlow
- repository boundaries around playback, stations, history, backup, settings,
  discovery, and recommendations
- deterministic active-station identity based on stream URL / station key
- explicit UI state models for loading, previewing, reconnecting, empty, error,
  and success states

### Local Data Layer

- DataStore for saved stations, favorites, settings, and lightweight app state
- Room for listening history and local listening statistics
- local station logo storage
- backup models for exported local state
- schema files and migration discipline for history persistence

### Discovery & Recommendation Layer

- station search by name, genre, country, and language
- explicit smart search mode for AI-assisted queries
- browse surfaces for countries, languages, and genres
- preview playback before saving stations
- local station catalogue fallback and stream playability signals
- recommendation and similar-station views driven by station metadata, listening
  signals, and backend-ranked candidates

### Optional AI / Backend Layer

- FastAPI backend as a protected gateway
- AI search planning and localized explanations
- station DNA enrichment
- recommendation and similar-station ranking
- explain-station responses where enough data exists
- stream-health workers and station-catalog enrichment
- server-side validation, quota controls, and App Check verification

### Backup Layer

- Google Drive backup and restore through user-authorized Google sign-in
- manual backup / restore flows
- automatic backup controls such as Wi-Fi-only behavior
- backup payload scoped to saved stations, settings, and station logos
- no claim of real-time cross-device sync

## Current System Shape

```text
Android UI              -> Jetpack Compose + Material 3
Playback                -> Media3 + foreground service + audio focus recovery
Local station library   -> DataStore + local logo files
Listening history       -> Room + repository + upload/reporting workers
Discovery               -> station search + browse + preview + local catalogue
AI assistance           -> Android client + FastAPI + App Check / Integrity
Backend data            -> MariaDB-compatible schema + enrichment workers
Backup / restore        -> Google Drive API + user-authorized account
```

The architecture is optimized for reliable playback, clear state transitions,
privacy-aware local ownership, and optional backend intelligence that can evolve
without making everyday radio playback fragile.
