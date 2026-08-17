# React Native CLI — Debugging & Performance

_Last updated: 2026-08-18_

## Overview

Roadmap for debugging and performance-tuning a React Native CLI app — the JS-side bundler internals, native log tooling, Bridge/JSI performance under the New Architecture, and common memory/list-rendering pitfalls. Each header below is a candidate for its own deep-dive conversation and subtopic doc, same pattern as the Native Toolchains roadmaps.

## Metro bundler internals and cache clearing

- Metro's pipeline: resolve → transform → serialize → serve, run as a long-lived dev server rather than a one-shot build.
- Transform (Babel) output is cached per file by content hash, on disk, outside the project folder.
- `--reset-cache` clears only Metro's own transform cache — not `node_modules`, Gradle, or CocoaPods caches.
- Covered — see `01. Metro bundler internals and cache clearing.md`.

## Flipper / React Native DevTools & native logs

- Flipper or React Native DevTools for JS-side inspection (network, layout, Redux/state).
- Native logs: `adb logcat` (Android) vs Xcode console (iOS) — where native crashes and exceptions actually surface. The one piece of this topic that genuinely forks by platform.
- Covered — see `02. Flipper, React Native DevTools, and native logs.md`.

## Bridge/JSI performance

- Bridge (old architecture, async + serialized) vs JSI (New Architecture, synchronous, direct native calls) — where the overhead actually comes from.
- Where this matters in practice: high-frequency native calls (animations, sensors, frequent native-module round trips).
- Covered — see `03. Bridge vs JSI performance.md`.

## Memory leaks and list virtualization

- Common RN memory leak sources: uncanceled timers/listeners, retained closures in navigation stacks.
- `FlatList` optimization levers: `windowSize`, `removeClippedSubviews`, `getItemLayout`, stable keys.
- Covered — see `04. Memory leaks and list virtualization.md`.

## Key Takeaways

- Metro's transform-cache model is the most common source of "why is this stale" confusion in this topic — already covered.
- Native log tooling is the one piece that forks by platform; Bridge/JSI and memory/list concerns are cross-platform.
- New Architecture (JSI) is directly relevant to the perf half of this topic, not a side note.

## Open Questions / Follow-ups

- All four subtopics above are covered. This roadmap is complete.
