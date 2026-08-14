# Verification & Production Reliability

AI-assisted development is only useful when the output is independently checked. This project uses a repeatable verification loop to reduce the risk of plausible-but-wrong generated code.

## Verification loop

1. Define the expected behavior and failure conditions.
2. Trace the relevant architecture and existing implementation before changing code.
3. Make the smallest scoped change that solves the identified root cause.
4. Build and lint the project.
5. Run automated tests.
6. Exercise the affected workflow against a running application.
7. Use real external services when credentials and safe test conditions are available.
8. Inspect persisted state or telemetry when the feature changes data.
9. Record known limits when a scenario cannot be fully validated.

## Evidence from the private production build

### Build and automated checks
Recent iterations repeatedly completed:
- clean Next.js production builds
- clean ESLint runs
- automated Node test suite runs

### Live-provider verification
AI usage instrumentation was tested against a real configured provider. Verification included:
- provider/model identity
- input/output token recording
- observed latency
- stored usage records
- independent cost calculation against the pricing registry

### Kill-switch verification
A central AI kill switch was tested by proving that:
- enabled: the paid call was blocked before the provider request
- disabled: the same call proceeded normally

### Cross-user isolation
Quota behavior was tested with distinct user identities to prove that one user's usage did not consume another user's allowance.

### Quota semantics
A three-phase AI validation flow originally consumed multiple quota units for one user action. Verification after the fix confirmed:
- one founder-initiated validation counted as one run
- all internal model calls were still recorded for telemetry/cost
- subsequent runs incremented quota correctly

### Production configuration behavior
A production-like Next.js build/start environment was used to confirm that missing real Supabase credentials surface a configuration error rather than silently enabling mock authentication.

### Deployment debugging
A Vercel Linux build failure caused by native optional dependencies was diagnosed from the actual dependency graph and lockfile behavior rather than patched with committed binaries. The fix made the required Linux packages explicit optional dependencies while preserving platform gating.

## Reliability principles

- never claim a test ran when it did not
- distinguish code-complete from visually verified
- prefer root-cause fixes over symptom patches
- preserve local mockability without weakening production boundaries
- record usage and errors at central chokepoints
- keep privileged credentials and service-role operations server-only
- disclose known gaps and deferred work

## Why this matters to clients

A polished demo can hide fragile implementation. The goal of this workflow is a handoff that is understandable, testable, and safer to operate after delivery.