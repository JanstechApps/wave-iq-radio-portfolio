# Data Layer Design

The data layer is designed around local ownership, predictable station identity,
explicit backup boundaries, and backend-assisted intelligence that does not
replace the user's saved station library.

## Storage Technologies

- **Jetpack DataStore** for saved stations, favorites, settings, sleep timer
  state, and lightweight app preferences.
- **Room** for structured listening history and local listening statistics.
- **Local file storage** for station logos and backup assets.
- **Google Drive** for user-authorized backup and restore.
- **MariaDB-compatible backend schema** for AI usage events, station catalogues,
  station DNA, stream health, and discovery support.
- **SQLAlchemy / async database access** in the FastAPI backend.

## Local Station Model

Saved stations are treated as user-owned local data. The central identity rule is
that a station key maps to the stream URL so that playback, deletion, undo,
history, and metadata updates do not accidentally target the wrong station.

Local station state includes:

- station name
- stream URL / station key
- optional homepage or metadata URL
- genre, country, language, and tag hints when available
- favorite state
- locally cached logo reference
- last active / selected station state

Key design decisions:

- station identity is deterministic
- metadata changes must not silently switch the active stream
- delete and undo flows preserve predictable active-station behavior
- logo cleanup is tied to local station removal
- preview playback does not mutate the saved station list unless the user
  explicitly saves the station

## Listening History

Listening history and statistics are stored separately from the saved-station
list. This lets the app use taste signals without making recommendations a
requirement for local playback.

History-related data areas include:

- listening sessions
- station-level listening statistics
- now-playing events where available
- upload/reporting scheduling state
- local taste signals used to inform recommendation flows

The history layer is designed to be useful for product intelligence while still
keeping the user's station library understandable and editable.

## Backup Model

Google Drive backup is scoped to user-controlled app state. It is backup and
restore, not real-time cross-device sync.

Backup includes:

- saved stations
- favorites
- relevant settings
- station logo assets / references

Backup should not be described as exporting internal recommendation scores,
backend station DNA records, stream-health tables, AI quotas, analytics events,
or security metadata.

Restore behavior is designed around reconciliation:

- read remote backup metadata
- compare local and remote baseline
- restore user-owned station data
- restore settings and logos where available
- avoid overwriting active local state silently when the UI needs confirmation
  or feedback

## Backend Data Boundary

Backend data supports discovery, AI, reliability, and protection. It is not the
source of truth for the user's local station list.

Backend-side data areas include:

- station catalogue entries
- stream health status and disabled-until style reliability signals
- station DNA / enrichment records
- AI usage events
- recommendation and smart-search support data
- RadioBrowser enrichment and catalogue backfill workflows

The backend can improve candidate quality and explanation coverage, but Android
must continue to handle empty, partial, stale, or unavailable backend responses.

## Key Design Decisions

- Local saved stations stay usable without Google Drive or AI.
- AI and recommendation metadata are treated as optional enrichment.
- Stream health can influence discovery and feedback without deleting user-saved
  stations.
- Backup payloads are scoped to user-owned app state.
- Room schema evolution is explicit and versioned.
- Backend quotas and endpoint protection are enforced server-side.
- Preview playback, main playback, saved-station edits, and backup restore are
  separated to avoid accidental state corruption.
