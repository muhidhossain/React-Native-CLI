# React Native CLI — Testing

_Last updated: 2026-08-24_

## Progress

- Unit testing with Jest — covered, see `01. Unit testing with Jest.md`.
- E2E testing with Detox/Maestro — covered, see `02. E2E testing with Detox and Maestro.md`.

Both subtopics above are covered. This roadmap is complete.

## Overview

Roadmap for the two testing tiers in a React Native CLI project: JS-level unit testing with Jest, and end-to-end testing against real builds with Detox or Maestro. These are genuinely different tooling stacks — Jest runs against JS/mocked native modules with no build step, while Detox/Maestro drive an actual compiled app on a simulator/emulator or device — so each is a candidate for its own deep-dive conversation and subtopic doc, same pattern as the Debugging & Performance, State/Navigation/Networking, and Platform-Specific Quirks roadmaps.

## Unit testing with Jest

- Covered — see `01. Unit testing with Jest.md`.

## E2E testing with Detox/Maestro

- Covered — see `02. E2E testing with Detox and Maestro.md`.

## Key Takeaways

- Jest unit tests mock everything native and run in plain Node — fast and cheap to run exhaustively, but they can't prove the actual compiled app works.
- E2E tests (Detox/Maestro) build and run the real app on a simulator/emulator/device, catching failures unit tests structurally can't — a broken native build, a blocking permission dialog, a hung transition — at the cost of needing real device infrastructure in CI.
- Detox and Maestro represent opposite tradeoffs on the same synchronization problem: Detox hooks into the app's own event loop for precise waits at the cost of native build-variant setup; Maestro needs no native setup but relies on polling/retries instead of true event-loop awareness.
- Because E2E is expensive relative to unit tests (simulator/emulator boot time, native runner requirements), it's typically scoped to critical flows rather than run exhaustively like Jest tests.

## Open Questions / Follow-ups

- Mocking `NativeModules` manually for a module with no existing Jest mock.
- How `jest.config.js`'s `moduleNameMapper` handles path aliases defined in `babel.config.js`.
- Detox's `waitFor`/`whileElement` APIs for scrolling to find elements in lists.
- Maestro Cloud for device-farm runs.
