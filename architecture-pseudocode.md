# Pseudocode Samples (No Source Code)

This document provides implementation-level pseudocode to demonstrate how WaveIQ
Radio is structured without publishing the actual production source code,
proprietary backend implementation, ranking weights, credentials, or security
internals.

---

## 1) Saved Station Identity

Goal: keep station operations deterministic even when metadata changes.

```text
Station.stationKey():
    return normalize(streamUrl)

Repository.saveStation(candidate):
    key = candidate.stationKey()

    existing = stationStore.findByKey(key)
    if existing exists:
        return existing.markAsAlreadySaved()

    station = Station(
        key = key,
        name = candidate.displayName,
        streamUrl = candidate.streamUrl,
        country = candidate.country,
        language = candidate.language,
        tags = candidate.tags,
        logoRef = logoStore.cache(candidate.logoUrl)
    )

    stationStore.insert(station)
    return station
```

Notes:

- Stream URL identity prevents accidental station replacement.
- Metadata enrichment can update display details without changing playback
  ownership.
- Add and undo flows can target the same station reliably.

---

## 2) Main Playback Versus Preview Playback

Goal: allow discovery preview without corrupting the active saved station.

```text
DiscoveryScreen.preview(candidate):
    previewPlayer.stopCurrentPreview()
    previewPlayer.play(candidate.streamUrl)
    state.previewingKey = candidate.stationKey()

DiscoveryScreen.savePreviewedStation(candidate):
    station = stationRepository.saveStation(candidate)
    previewPlayer.stopCurrentPreview()
    mainPlayer.play(station)
    state.activeStationKey = station.key
```

Notes:

- Preview playback is isolated from saved-station playback.
- The main active station changes only after an explicit user action.
- This prevents "ghost playback" where UI and audio point to different stations.

---

## 3) Weak Wi-Fi Playback Recovery

Goal: recover from real-world network instability without looping forever.

```text
PlaybackObserver.onBufferingTooLong(activeStation):
    if userManuallyPaused():
        return

    if recoveryPolicy.shouldRetrySameStream(activeStation):
        showStatus("Reconnecting")
        player.retry(activeStation.streamUrl)
        return

    if networkMonitor.cellularAvailable() and settings.allowCellularFallback:
        showStatus("Switching network")
        player.retryUsingAvailableNetwork(activeStation.streamUrl)
        return

    showStatus("Playback problem")
    streamHealthReporter.reportFailure(activeStation, reason = "buffering_timeout")
```

Notes:

- Manual pause is respected.
- Same-station retries and cellular fallback are separate decisions.
- Stream health reporting supports future discovery quality without deleting the
  user's saved station.

---

## 4) AI Smart Search Flow

Goal: call AI search through a protected backend and return a normal discovery
result list.

```text
Android.smartSearch(query, uiLanguage):
    appCheckToken = AppCheckProvider.currentToken()

    plan = backend.post(
        path = "/search-plan",
        headers = {
            "X-Firebase-AppCheck": appCheckToken
        },
        body = {
            "query": query,
            "ui_lang": uiLanguage
        }
    )

    candidates = discoveryRepository.search(plan.searchTerms)
    ranked = discoveryRepository.applyPlanHints(candidates, plan)

    return DiscoveryUiState(
        results = ranked,
        explanation = plan.localizedExplanation
    )
```

Notes:

- AI produces a search plan, not direct uncontrolled UI state.
- Android still handles empty, partial, stale, or failed backend responses.
- UI language is passed so explanations can match the user's locale.

---

## 5) Recommendation Flow

Goal: combine local taste signals with backend station intelligence.

```text
RecommendationsViewModel.refresh():
    historySummary = historyRepository.localTasteSummary()
    savedKeys = stationRepository.savedStationKeys()

    response = backend.recommendations(
        taste = historySummary,
        exclude = savedKeys
    )

    cards = response.candidates.map(candidate ->
        RecommendationCard(
            station = candidate.toStationPreview(),
            reason = candidate.explanationOrNull(),
            logo = logoResolver.resolve(candidate.logoUrl),
            reliability = candidate.streamHealthSummary
        )
    )

    state = RecommendationsState.Ready(cards)
```

Notes:

- The local saved-station list remains authoritative.
- Recommendations exclude already saved stations where possible.
- Explanation text is optional and should not block recommendation display.

---

## 6) Similar Stations With Tuned Modes

Goal: discover alternatives to a station while letting the user steer the result
style.

```text
SimilarStations.load(seedStation, mode):
    seed = stationRepository.resolve(seedStation.key)

    request = {
        "station_key": seed.key,
        "stream_url": seed.streamUrl,
        "mode": mode  // default, less_talk, more_energy, calmer
    }

    response = backend.similarStations(request)

    return response.candidates
        .filter(candidate -> candidate.streamUrl != seed.streamUrl)
        .dedupeByStreamUrl()
        .map(candidate -> candidate.toPreviewCard())
```

Notes:

- The seed station is excluded from results.
- Tuned modes affect candidate ranking, not the local saved station itself.
- Results are shown as previewable station cards.

---

## 7) Google Drive Backup / Restore

Goal: back up user-owned local app state without describing it as realtime sync.

```text
BackupRepository.createBackup():
    requireUserAuthorizedDriveAccount()

    snapshot = localTransaction {
        stations = stationStore.readAll()
        settings = settingsStore.readAll()
        logos = logoStore.collectReferencedLogos(stations)
    }

    archive = ZipBackup.create(
        schemaVersion = CURRENT_BACKUP_SCHEMA,
        payload = snapshot
    )

    driveClient.uploadOrReplace(archive)
    backupState.markSuccess(now())

BackupRepository.restoreBackup():
    requireUserAuthorizedDriveAccount()

    archive = driveClient.downloadLatestBackup()
    snapshot = ZipBackup.readAndMigrate(archive)

    localTransaction {
        stationStore.replaceWith(snapshot.stations)
        settingsStore.mergeRestore(snapshot.settings)
        logoStore.restore(snapshot.logos)
    }

    playbackController.reconcileActiveStationAfterRestore()
```

Notes:

- Backup and restore are user-authorized.
- Restore reconciles playback state after local data changes.
- The flow backs up local app state, not backend ranking internals.

---

## 8) Protected Backend Endpoint

Goal: enforce trust boundaries server-side.

```text
Backend.handleAiRequest(request):
    appCheckResult = verifyAppCheck(request.headers["X-Firebase-AppCheck"])
    if appCheckResult.invalid:
        reject(401)

    clientContext = parseClientContext(request)
    quota.enforce(clientContext, scope = request.scope)

    sanitizedInput = validateAndNormalize(request.body)
    aiResult = aiService.generate(sanitizedInput)
    safeResult = validateAiResponse(aiResult)

    usageEvents.record(clientContext, scope = request.scope)
    return safeResult
```

Notes:

- Security checks happen in the backend.
- Quotas are not trusted to the Android client.
- AI responses are validated before returning to the app.

---

## Repository Notice

These are conceptual outlines designed for technical evaluation. They do not
represent the full production implementation.
