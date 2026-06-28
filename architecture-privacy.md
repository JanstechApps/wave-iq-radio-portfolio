# Privacy & Security

WaveIQ Radio is not a purely offline application, but its architecture separates
local listening data from optional online intelligence. The user's saved station
library remains local-first, while AI discovery, stream health, recommendations,
analytics, and backup use network services with explicit boundaries.

## Local-Core Guarantees

- Saved stations and favorites are stored locally.
- Settings are stored locally.
- Station logos are cached locally where available.
- Listening history and local listening statistics are stored on device.
- Ordinary playback does not require an account.
- AI is not required to play a saved station.
- Google Drive backup is user-authorized and user-controlled.

## Optional Network Features

Network-assisted features include:

- AI smart search
- localized search explanations
- recommendations
- similar-station discovery
- explain-station responses
- stream health reporting and reliability feedback
- station catalogue enrichment
- Google Drive backup and restore
- analytics or usage-event reporting where enabled

These features should be described as online features. They are useful
extensions, not the source of truth for the local station list.

## Backup Boundary

Google Drive backup is designed for backup and restore of user-owned app state.
It should not be marketed as live real-time sync.

Backup scope:

- saved stations
- favorites
- settings
- station logos / logo references

Excluded or non-public backup areas:

- internal AI ranking weights
- recommendation scoring fields
- stream-health operational tables
- App Check / Play Integrity tokens
- backend quota records
- production credentials
- internal endpoint implementation details

## Backend Trust Boundary

Backend-assisted features are protected by server-side checks. The client is not
trusted to enforce security, quota, or AI-cost controls by itself.

Security controls include:

- Firebase App Check verification
- Google Play Integrity participation
- protected FastAPI endpoints
- server-side validation and sanitization
- backend quota / rate limiting
- defensive handling of missing or invalid security headers
- separation between public station metadata and private operational data

## Analytics Boundary

Analytics and usage events must be scoped carefully. Event payloads should avoid
including sensitive or unnecessary user content.

Events should not expose:

- private user identifiers where not required
- raw personal notes or messages
- production secrets
- security tokens
- internal ranking weights
- hidden backend endpoint details

For a radio app, listening signals can be useful for recommendations and product
quality, but public documentation should remain clear that online intelligence is
separate from local saved-station ownership.

## Public Portfolio Boundary

This portfolio repository intentionally documents architecture without exposing
security-sensitive implementation details.

Do not publish:

- production API keys
- Google service secrets
- OAuth client secrets
- private backend URLs if not intended for public use
- database credentials
- exact abuse-prevention thresholds
- proprietary ranking formulas
- unreleased experiments as shipped features

The public story should emphasize the engineering shape: local-first station
ownership, resilient playback, optional AI discovery, user-controlled backup,
and protected backend boundaries.
