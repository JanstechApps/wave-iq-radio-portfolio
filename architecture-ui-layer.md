# UI, Playback & State Management

The UI is implemented with Jetpack Compose and Material 3, with screen state
driven by ViewModels, Kotlin Flow / StateFlow, and repository boundaries. Because
WaveIQ Radio is a live playback app, UI state and playback state are designed
together rather than as separate concerns.

## UI Principles

- Declarative UI with explicit screen states.
- Single source of truth per screen.
- State hoisting out of composables where practical.
- Clear separation between presentation state, playback commands, repository
  calls, persistence, and backend requests.
- Compact, readable layouts for older and smaller Android devices.
- User-facing feedback for loading, buffering, reconnecting, previewing, errors,
  empty states, and completed actions.

## Main User Flows

- home / saved stations
- station edit and delete / undo
- main playback controls
- preview playback from discovery results
- station search by name, genre, country, and language
- AI-assisted smart search
- browse by genre, country, and language
- recommendations
- similar stations and tuned similar-station modes
- explain-station dialog
- listening history
- sleep timer
- settings and language selection
- Google Drive backup / restore
- privacy, support, app sharing, review, and update entry points

## Playback State

Live radio playback is stateful and network-sensitive. The app models playback
states clearly so the user can understand what is happening.

Important playback states include:

- idle
- preparing
- playing
- paused by user
- buffering
- reconnecting
- failed
- temporarily suspended after manual pause
- previewing a discovery result
- restored after interruption

Playback design considerations:

- preview playback must not replace main playback accidentally
- explicit station selection clears manual suspend states
- same-station reconnect is handled differently from station switching
- audio focus loss from calls is recoverable when appropriate
- weak Wi-Fi fallback must avoid endless visible churn
- screen-off playback must remain service-driven
- notification/media-session state should match the actual active station

## Discovery UI State

Find Stations supports several explicit modes:

- smart search
- station name
- tag / genre
- country
- language

The UI avoids ambiguous search behavior by making search mode and search
execution explicit. This prevents accidental backend calls on every keystroke and
keeps the result list understandable.

Discovery state includes:

- current search mode
- user query
- loading state
- paged result list
- preview player state
- stream playability status
- added / already saved status
- AI explanation text when available
- empty and error states

## Recommendation UI State

Recommendations and similar-station screens combine station metadata, listening
signals, stream reliability, and explanation availability. The UI must handle
partial coverage gracefully.

Recommendation surfaces account for:

- station logo availability
- placeholder logo fallback
- explanation available / unavailable states
- tuned similar-station modes
- preview before saving
- add / undo flows
- active-station overlay visibility
- compact card actions on small screens

## Compatibility Considerations

The production work pays attention to:

- Android 8+ device behavior
- small phones and narrow screens
- keyboard / IME stability
- bottom dock and snackbar clearance
- system bar insets
- background playback restrictions
- notification and audio focus differences across Android versions
- layout stability when translated strings are longer

The goal is a radio app that feels calm during normal use and still provides
clear feedback when networks, streams, or permissions are imperfect.
