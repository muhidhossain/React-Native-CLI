# React Native CLI — Native Toolchains: iOS

_Last updated: 2026-08-12_

## Overview

Roadmap of the iOS-specific native toolchain underneath React Native — the pieces Xcode, CocoaPods, and the RN CLI abstract away, but that surface the moment a build fails, a signing step breaks, or a native module needs custom setup. Mirrors the structure of the Android roadmap; kept as a separate file since the two toolchains share almost no surface area.

## Core pieces and how they fit together

- **Xcode** — the IDE/build system for the whole native layer; also ships the actual compiler toolchain (Clang) and simulators.
- **CocoaPods** — dependency manager for native iOS libraries (`Podfile`, `Podfile.lock`, `pod install`); how most RN native modules get linked in.
- **`.xcworkspace` vs `.xcodeproj`** — once Pods are installed, the workspace (not the bare project) is the thing to open; forgetting this is a classic gotcha.
- **`Info.plist`** — app metadata, permissions usage strings, URL schemes, deep-link config.
- **Provisioning profiles & signing certificates** — tie a build to an Apple Developer account/device/App ID; distinct from Android's self-managed keystore model.

## Xcode build specifics

- Targets, schemes, and build configurations (Debug/Release) — how they relate to each other and to Gradle's build variants/flavors on Android.
- Build Settings vs `Info.plist` vs entitlements — where a given config value actually lives.
- `.xcconfig` files for environment-specific overrides (dev/staging/prod), analogous to Gradle's flavors.
- How CocoaPods wires into the Xcode build via the generated `Pods.xcodeproj` and `[CP]` build phases.

## Signing and release builds

- Automatic vs manual signing in Xcode; Apple Developer account, App IDs, provisioning profiles, distribution certificates.
- Archiving (`.xcarchive`) and exporting an `.ipa` for TestFlight/App Store submission.
- Common signing failures: expired certs, mismatched provisioning profiles, missing entitlements.
- How this compares to Android's keystore-based signing — no local keystore file to manage, but more account/portal-side state to keep in sync.

## Native module / JSI build path

- How a native module's Objective-C/Swift (`.h`/`.m`/`.mm`/`.swift`) sources get built via CocoaPods' generated Xcode project.
- Bridging headers for mixed Swift/Objective-C modules.
- Static vs dynamic frameworks/libraries — `use_frameworks!` and its interaction with some native modules.
- Podspecs (`*.podspec`) — how a library declares itself to CocoaPods, roughly analogous to a Gradle module's `build.gradle`.

## Debugging tools

- Xcode's console/device log output — where native crashes and Objective-C/Swift exceptions surface.
- Console.app for viewing device logs outside Xcode.
- Symbolicating crash reports; `.dSYM` files for release-build crash logs.
- Instruments for performance/memory profiling — the rough iOS counterpart to Android's profiler tooling.

## Where RN CLI abstracts this away

- `npx react-native run-ios` wraps `xcodebuild` (and triggers `pod install` when needed) — worth knowing so you can drop to raw `xcodebuild`/`pod install` commands when the wrapper's error output isn't enough.
- Common failure points the CLI can't paper over: Pods out of sync with `Podfile.lock`, signing/provisioning mismatches, and stale derived-data caches.

## Key Takeaways

- iOS and Android toolchains share almost no surface area — same reasoning as the Android file for keeping these separate.
- CocoaPods is the center of gravity for most iOS build/link issues, similar to Gradle's role on Android.
- Signing is account/portal-driven (Apple Developer, provisioning profiles) rather than a local keystore file — a meaningfully different mental model from Android.
- Nothing in this file is written up yet — each header above is a candidate for its own deep-dive conversation and subtopic doc, same pattern as Android's `01`–`06` files.

## Open Questions / Follow-ups

- Deep dive on CocoaPods internals and common dependency-resolution failures.
- Deep dive on signing/provisioning end-to-end (certs, profiles, App Store Connect).
- Deep dive on the native-module build path (bridging headers, Podspecs, static vs dynamic frameworks).
- Deep dive on Xcode debugging tools and crash symbolication.
