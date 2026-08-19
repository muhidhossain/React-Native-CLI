# React Native CLI — State, Navigation, Networking

_Last updated: 2026-08-20_

## Progress

- Navigation — covered, see `01. React Navigation and its native dependencies.md`.
- State management — covered, see `02. State management and the persistence boundary.md`.
- Networking, offline handling, and push notifications — covered, see `03. Networking, offline handling, and push notifications.md`.

All three subtopics above are covered. This roadmap is complete.

## Overview

Roadmap for three JS-level concerns that each pull in real native setup underneath — React Navigation's native dependencies, the persistence boundary under state management, and networking/offline handling/push notifications. Unlike the native-toolchain topics, the through-line here isn't a build pipeline — it's that the JS API stays simple while the native setup is where the real work (and the real gotchas) live. Each header below is a candidate for its own deep-dive conversation and subtopic doc, same pattern as the Debugging & Performance roadmap.

## Navigation — React Navigation and its native dependencies

- React Navigation itself (stack/tab/drawer) is pure JS — a state machine plus transition UI.
- As of v5+, it depends on non-pure-JS libraries: `react-native-screens`, `react-native-gesture-handler`, and (often) `react-native-reanimated`.
- **react-native-screens** — properly unmounts off-screen stack screens from the native view hierarchy instead of leaving them mounted; needs a native rebuild after install, not just a JS reload.
- **react-native-gesture-handler** — native gesture recognizers for swipe-to-go-back/drawer gestures; requires a non-autolinked step wrapping the root component/`MainActivity` on Android.
- **react-native-reanimated** — needed for native-driven transition animations; its Babel plugin must be last in `babel.config.js`'s plugins array, or you get cryptic worklet errors instead of a clear config error.
- Practical takeaway: adding React Navigation to a CLI project is never just `npm install` — expect `pod install`, a Gradle sync check, a possible Babel config edit, and (Android) a `MainActivity`/`MainApplication` touch.

## State management — the persistence boundary

- Redux, Zustand, and Context are all pure-JS state containers — no native code in the state management itself.
- Native involvement shows up specifically at **persistence**: `AsyncStorage` (`@react-native-async-storage/async-storage`) is the standard choice, backed by `SharedPreferences` (Android) / a native key-value store (iOS), and is async — every read/write crosses the JS↔native boundary.
- **MMKV** (`react-native-mmkv`) is a faster alternative, backed by mmap'd memory rather than round-tripping — same "JSI enables synchronous native access" story as the New Architecture doc, applied to storage instead of rendering.
- `redux-persist` is the common adapter wiring a Redux store to whichever storage engine (AsyncStorage or MMKV) is chosen — nothing native-specific about `redux-persist` itself.

## Networking, offline handling, and push notifications

- Plain networking (`fetch`, axios) has no native involvement — identical to any JS environment.
- **Offline detection** needs `@react-native-community/netinfo`, a native module wrapping `ConnectivityManager` (Android) / `NWPathMonitor` (iOS). Offline *handling* beyond detection (queue-and-replay) is usually a JS-level pattern (RTK Query, React Query).
- **Push notifications** are the one area with unavoidable, substantial native setup, since push delivery is a platform service:
  - Android: Firebase Cloud Messaging (FCM), `google-services.json` in `android/app/`, Firebase SDK registered in `build.gradle`.
  - iOS: Apple Push Notification service (APNs), an auth key/certificate in App Store Connect, `GoogleService-Info.plist` if Firebase is the cross-platform layer.
  - Foreground vs. background vs. killed-state delivery differs by platform and by library (`setBackgroundMessageHandler` on Android vs. a native delegate method on iOS).
  - iOS also requires enabling the Push Notifications capability and (for background delivery) Background Modes → Remote notifications in Xcode — an Info.plist/entitlements change that silently fails on-device with no JS-side error if missed.
  - CLI vs. Expo gap: no shared Expo push token relay — you register your own Firebase project and your own APNs key/cert.

## Key Takeaways

- All three areas share the same pattern: simple JS API, real native setup at the integration boundary — this mirrors the native-toolchain topics but shows up at the library level instead of the build-pipeline level.
- Navigation's native dependencies (`screens`, `gesture-handler`, `reanimated`) each have their own non-obvious native setup step beyond autolinking.
- State management is native-agnostic except at the persistence boundary, where MMKV's JSI-based sync access is the same mechanism (not just the same idea) as Fabric/TurboModules' synchronous calls.
- Push notifications are the heaviest native lift in this topic — full per-platform registration, not just a library install.

## Open Questions / Follow-ups

- Step-by-step FCM (Android) + APNs (iOS) setup walkthrough for push notifications.
- react-native-gesture-handler's Android root-view wrapping step in detail.
- None of the three subtopics above have a dedicated doc yet — this file is the roadmap they'll be carved from.
