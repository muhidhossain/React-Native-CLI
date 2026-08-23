# React Native CLI — Platform-Specific Quirks

_Last updated: 2026-08-24_

## Progress

- File conventions — covered, see `01. Platform.OS and file-suffix conventions.md`.
- Permissions handling — covered, see `02. Permissions handling.md`.
- Deep linking / Universal Links — covered, see `03. Deep linking and Universal Links.md`.

All three subtopics above are covered. This roadmap is complete.

## Overview

Roadmap for the places where React Native's "write once" JS layer still forks by platform: file-level code splitting via `Platform.OS`/`.ios.js`/`.android.js`, permissions models that differ significantly between Android and iOS, and deep linking setups that require separate native configuration on each side. Same pattern as the Debugging & Performance and State/Navigation/Networking roadmaps — each header below is a candidate for its own deep-dive conversation and subtopic doc.

## File conventions — `Platform.OS` and `.ios.js`/`.android.js`

- Covered — see `01. Platform.OS and file-suffix conventions.md`.

## Permissions handling

- Covered — see `02. Permissions handling.md`.

## Deep linking / Universal Links

- Covered — see `03. Deep linking and Universal Links.md`.

## Key Takeaways

- File-level forking (`Platform.OS` vs. `.ios.js`/`.android.js`) is a runtime-vs-bundle-time choice — pick based on how much implementation diverges, not just platform count.
- Android permissions are declarative-plus-runtime-request with a re-promptable denial state; iOS is declarative-and-implicit with no re-prompt ever after the first decision — both eventually funnel into an "open Settings" fallback.
- Deep linking splits into native ownership verification (Android's `assetlinks.json`, iOS's `apple-app-site-association`) and a separate JS-side problem of reconstructing the full nested navigation state from a URL.
- Across all three subtopics, the recurring shape is the same: a simple-looking JS API sits on top of real, and differently-shaped, native machinery per platform.

## Open Questions / Follow-ups

- Notification-permission special case and `react-native-permissions`'s `checkNotifications`/`requestNotifications` API.
- Structuring `linking.config` for very deep nesting, and the `getStateFromPath`/`getPathFromState` override hooks.
