# React Native CLI — JS Bundle Reload vs. Full Native Rebuild

_Last updated: 2026-07-11_

## Overview

A deeper dive on a distinction only briefly mentioned in the main Core Project Structure doc: when a code change only needs Metro to re-bundle JS, versus when it requires a full native rebuild (Gradle/Xcode).

## What actually gets compiled where

A React Native app has two layers that build independently:

1. **The JS bundle** — `App.tsx`, all components, business logic, third-party JS-only libraries. Metro bundles this into a single JS file the native runtime executes.
2. **The native binary** — compiled Java/Kotlin (Android) or Objective-C/Swift (iOS) code, including the RN runtime itself, any native modules, and native configuration (permissions, entitlements, app icons, etc.).

Fast Refresh / hot reload only re-executes the JS bundle. It does **not** touch the native binary at all.

## When a JS-only reload is enough

If a change only touches things Metro can re-bundle, no native rebuild is needed:

- Editing component logic, styles, JSX
- Adding/removing pure-JS npm packages (no native code, e.g. `lodash`, `date-fns`)
- Changing JS-side navigation logic, state management, API calls

Metro detects the file change, re-bundles, and Fast Refresh injects the new bundle into the running app in under a second.

## When a full native rebuild is required

Anything that changes the **native binary itself** requires a real rebuild (`pod install` + Xcode build, or Gradle sync + build):

| Change                                                                                              | Why it needs a rebuild                                                           |
| --------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| Installing a library with native code (e.g. `react-native-reanimated`, `react-native-vector-icons`) | New native code must be compiled into the binary                                 |
| Editing `AndroidManifest.xml` or `Info.plist`                                                       | Permissions/config are baked into the native binary, not read from JS at runtime |
| Changing `android/app/build.gradle` or `ios/Podfile`                                                | Build configuration changes require Gradle/Xcode to re-run                       |
| Upgrading React Native itself                                                                       | Native runtime code changes                                                      |
| Adding native assets (fonts via native linking, custom native modules)                              | Not part of the JS bundle                                                        |

**Rule of thumb:** if the change lives inside `android/` or `ios/`, or it's a package with a native component, assume `pod install` (iOS) and/or a Gradle sync + rebuild (Android) is needed. If it's pure JS, Fast Refresh handles it.

## A common trap

Installing a native-code library and only running `npm install` — the JS side updates and Metro doesn't complain, but the app crashes or the feature silently doesn't work at runtime because the native side was never rebuilt with the new native module linked in. Always check a library's install instructions for a `pod install` or "rebuild your app" step before assuming `npm install` was sufficient.

## Practical checklist

- Only edited `.js`/`.ts`/`.tsx` files, no new deps? → reload is enough.
- Ran `npm install`/`yarn add` on anything with native code? → `pod install` + Gradle sync + full rebuild.
- Touched anything in `android/` or `ios/` directly? → full rebuild.
- Not sure? → rebuilding is always safe, just slower; skipping a needed rebuild causes confusing runtime bugs.

## Key Takeaways

- Fast Refresh only re-executes the JS bundle — it never touches the compiled native binary.
- Any native-code dependency or change inside `android/`/`ios/` needs `pod install` and/or a Gradle sync plus a full rebuild.
- The most common bug source: installing a native-code library and forgetting the native rebuild step, leading to crashes or silent failures.

## Open Questions / Follow-ups

- What `pod install` and Gradle sync actually do under the hood
