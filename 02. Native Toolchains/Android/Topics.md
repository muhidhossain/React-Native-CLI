# React Native CLI — Native Toolchains: Android

_Last updated: 2026-08-07_

## Overview

Notes on the Android-specific native toolchain underneath React Native — the pieces that Gradle, the RN CLI, and autolinking abstract away, but that surface the moment a build fails or a native module needs custom setup. Kept separate from iOS since the two toolchains share almost no surface area.

## Core pieces and how they fit together

- **JDK** — compiles the Java/Kotlin layer. Version mismatches with Gradle/RN are a common source of build breakage.
- **Android SDK** — platform tools, build tools, platform APIs (`compileSdkVersion`, `targetSdkVersion`, `minSdkVersion`).
- **Android NDK** — needed because RN native modules (and Hermes/JSI) compile C/C++ for each CPU architecture (`arm64-v8a`, `armeabi-v7a`, `x86`, `x86_64`).
- **Gradle** — orchestrates the whole build. Source of most Android build pain.
- **ADB** — device/emulator comms: installing APKs, `logcat`, port forwarding.

## Gradle specifics

- `android/build.gradle` (project-level) vs `android/app/build.gradle` (module-level) — knowing which config lives where.
- `gradle.properties` — flags like `newArchEnabled`, `hermesEnabled`, JVM args, `android.useAndroidX`.
- Build variants/flavors: `debug` vs `release`, plus custom flavors for multiple environments or white-label builds.
- Dependency resolution — how RN autolinking wires native modules into `settings.gradle` and `app/build.gradle` automatically.
- **ABI splits vs universal APK** — `splits { abi { ... } }`. Ship smaller per-architecture APKs instead of one bloated universal one — relevant for low-connectivity/rural users where download size matters.

## Signing and release builds

- Debug vs release keystores; `signingConfigs` block in `build.gradle`.
- Generate a release keystore, keep it (and passwords) out of version control.
- **ProGuard/R8** — code shrinking/obfuscation for release builds. Can silently break native modules if the right `-keep` rules aren't added.

## Native module / JSI build path

- How a native module's `.cpp`/`.h` files get picked up by Gradle's CMake/NDK build (`CMakeLists.txt`, or `Android.mk` in older-style setups).
- ABI targeting — confirming native code (e.g. model-loading logic) is actually built for the ABIs target devices use. `arm64-v8a` covers most modern Android phones.
- APK size tradeoffs from bundling native `.so` libs per-ABI — adds real weight to the APK separate from any bundled model/data files, worth factoring into download-size planning for low-connectivity targets.

## Debugging tools

- `adb logcat`, filtered by tag/package — where native crashes and JNI errors actually surface.
- `./gradlew assembleRelease --stacktrace` / `-i` / `-d` for build failures.
- Android Studio's Build tab / raw Gradle output — RN CLI errors are often a truncated version of a deeper Gradle error.

## Where RN CLI abstracts this away

- `npx react-native run-android` is a wrapper around Gradle — worth knowing so you can drop to raw `./gradlew` commands when the wrapper's error messages aren't enough.
- Autolinking failures, duplicate class errors, and "manifest merger failed" issues are the most common points where the Gradle layer can't be avoided.

## Key Takeaways

- Android and iOS toolchains share almost no surface area — kept as separate subtopic files for that reason.
- Gradle is the center of gravity for nearly all Android build issues in RN.
- ABI splits and native `.so` size directly affect download size — a real constraint for low-connectivity/rural users.
- ProGuard/R8 rules are a common silent-breakage point for native modules in release builds.

## Open Questions / Follow-ups

- Deep dive on Gradle build variants/flavors in more detail.
- Deep dive on the NDK/CMake native-module build path (ties into llama.rn work).
- Deep dive on signing/release build process end-to-end.
- iOS native toolchain as its own future subtopic file.
