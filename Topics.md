# React Native CLI — Project Overview & Learning Roadmap

_Last updated: 2026-07-11_

## Overview

A roadmap of what's needed to maintain and manage a React Native project built with the React Native CLI (as opposed to Expo's managed workflow), where native `android/` and `ios/` folders are exposed directly and must be maintained by hand.

## Core Project Structure

- `android/` and `ios/` are real native projects, not abstracted away like in Expo.
- Key files: `index.js` (entry point), `App.tsx`/`App.js`, `metro.config.js`, `babel.config.js`, `package.json` scripts (`android`, `ios`, `start`).
- Understand the difference between a JS bundle reload and a full native rebuild — not every change needs a native rebuild, but some do.

## Native Toolchains

**Android**

- Android Studio, Gradle, `build.gradle` (project-level and app-level), `AndroidManifest.xml`.
- SDK/NDK versions, `gradle.properties`, keystores/signing configs for release builds.
- Managing Gradle version bumps and dependency conflicts.

**iOS**

- Xcode, CocoaPods (`Podfile`, `Podfile.lock`, `pod install`).
- Provisioning profiles, signing certificates, `Info.plist`.
- `.xcworkspace` vs `.xcodeproj` — always open the workspace once Pods are installed.

## Dependency & Version Management

- `react-native upgrade` / upgrade helper — comparing diffs between RN versions is often manual.
- Autolinking (RN 0.60+) still needs troubleshooting for native modules.
- Managing peer dependency conflicts between RN, React, and third-party native libs.
- Know which libraries require `pod install` or a Gradle sync after installing.

## Build & Release Process

- Debug vs. release builds; signing for Play Store / App Store.
- Generating APK/AAB (Android) and IPA (iOS).
- Environment-specific configs (dev/staging/prod) via `.env` files or flavor/scheme setups.
- CI/CD pipelines (Fastlane, Bitrise, GitHub Actions, CircleCI) for automated builds.

## Debugging & Performance

- Metro bundler internals; cache clearing with `--reset-cache`.
- Flipper or React Native DevTools; native logs (`adb logcat`, Xcode console).
- Bridge/JSI performance, especially relevant with the New Architecture.
- Memory leaks and list virtualization (`FlatList` optimization).

## New Architecture Awareness

- Fabric renderer, TurboModules, JSI — increasingly the default going forward.
- Codegen for native modules/components.

## State, Navigation, Networking

- React Navigation (stack/tab/drawer) and its native dependencies (gesture handler, reanimated, screens).
- State management (Redux, Zustand, Context) and async storage options.
- Networking, offline handling, push notifications (native setup required — FCM/APNs).

## Platform-Specific Quirks

- `Platform.OS` and `.ios.js`/`.android.js` file conventions.
- Permissions handling differs significantly between platforms.
- Deep linking / Universal Links setup requires native config on both sides.

## Testing

- Jest for unit tests.
- Detox or Maestro for E2E testing on real builds.

## Key Takeaways

- CLI projects require real native-toolchain fluency (Xcode + Android Studio) — this is the biggest departure from Expo.
- Dependency and native-module management is a recurring maintenance burden, not a one-time setup cost.
- The New Architecture (Fabric/TurboModules) is worth tracking even if not adopted yet, since the ecosystem is moving toward it.

## Open Questions / Follow-ups

- Each area above (native toolchains, build/release, new architecture, etc.) is a candidate for its own deep-dive conversation and subtopic doc.
