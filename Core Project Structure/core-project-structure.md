# React Native CLI — Core Project Structure

_Last updated: 2026-07-11_

## Overview

This covers what a freshly-scaffolded React Native CLI project (`npx @react-native-community/cli init MyApp`) contains, why each file/folder exists, and the mental model for how the JS and native sides fit together.

## Top-level layout

```
MyApp/
├── android/
├── ios/
├── node_modules/
├── App.tsx
├── index.js
├── package.json
├── metro.config.js
├── babel.config.js
├── tsconfig.json
├── .watchmanconfig
├── react-native.config.js (sometimes)
└── Gemfile (newer templates, for CocoaPods via Bundler)
```

## The entry point: `index.js`

The actual JS entry point — not `App.tsx`. It calls `AppRegistry.registerComponent`, which hands the root component to the native side:

```js
import { AppRegistry } from 'react-native';
import App from './App';
import { name as appName } from './app.json';

AppRegistry.registerComponent(appName, () => App);
```

The native `MainActivity` (Android) / `AppDelegate` (iOS) looks for this registered name at launch.

**Gotcha:** if you rename your app but forget to update it in both `app.json` and native config, the app crashes with a "component not registered" error at launch. This is a very common early mistake.

## `App.tsx`

The root React component. Everything built lives under here (or you restructure into a `src/` folder yourself — the CLI doesn't enforce a folder convention beyond the entry point).

## `android/` — a full native Android project

Not just config — a real Gradle project you can open directly in Android Studio:

- `android/app/src/main/java/.../MainActivity.java` — hosts the RN root view
- `android/app/src/main/AndroidManifest.xml` — permissions, app name, launch config
- `android/build.gradle` (project-level) vs `android/app/build.gradle` (app-level) — SDK versions, dependencies
- `android/gradle.properties` — flags like `newArchEnabled`, Hermes toggle

## `ios/` — a full Xcode project

- `ios/MyApp.xcodeproj` / `.xcworkspace` — the workspace only exists once Pods are installed
- `ios/MyApp/AppDelegate.mm` — bootstraps the RN bridge/root view
- `ios/Podfile` — CocoaPods dependencies (native side of any JS library with native code)

**Gotcha:** always open the `.xcworkspace`, not the `.xcodeproj`, once CocoaPods is in play — opening the wrong one is a classic source of confusing build errors.

## Build/config files

| File                     | Purpose                                                                                                       |
| ------------------------ | ------------------------------------------------------------------------------------------------------------- |
| `metro.config.js`        | Configures Metro, RN's JS bundler (like Webpack, tailored for RN)                                             |
| `babel.config.js`        | Transpiles JSX/TS/modern JS; usually just `module.exports = {presets: ['module:@react-native/babel-preset']}` |
| `package.json`           | Scripts (`react-native run-android`, etc.), JS deps                                                           |
| `.watchmanconfig`        | Config for Watchman, the file-watcher Metro uses for fast refresh                                             |
| `react-native.config.js` | Tells the CLI about native module linking, custom asset paths, etc.                                           |

## Key mental model

- The **JS side** (everything outside `android/`/`ios/`) is one shared bundle across platforms.
- The **native side** (`android/`, `ios/`) is two separate, real native projects that embed the RN runtime and load that JS bundle.
- This is why adding a library with native code requires a native rebuild (`pod install`, Gradle sync) — not just `npm install`. Pure-JS libraries need no native step at all.

## Key Takeaways

- `index.js` is the true entry point; `App.tsx` is just the root component it registers.
- `android/` and `ios/` are full native projects, not generated artifacts to ignore.
- Config files (`metro`, `babel`, `react-native.config.js`) tune the JS build pipeline; native `build.gradle`/`Podfile` tune the native builds.
- Always open `.xcworkspace`, not `.xcodeproj`, once CocoaPods is in play.
- Renaming an app requires updating both `app.json` and native config, or the app won't launch.

## Open Questions / Follow-ups

- Native module linking in depth
- New architecture (Fabric/TurboModules) folder implications
- How Metro resolves modules
