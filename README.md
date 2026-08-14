# AI Systems Portfolio Case Study

## AI Venture & Business Operating System

A production-oriented AI business operating system built to help a founder move from idea discovery through validation, setup, launch, customer acquisition, and growth.

This case study is intentionally sanitized. It documents architecture, product thinking, verification practices, and engineering outcomes without exposing private source code, credentials, proprietary prompts, or internal business logic.

## What the system does

The platform combines multiple business workflows inside one AI-assisted operating environment:

- business discovery and idea generation
- AI validation and market research
- business DNA / structured founder context
- CRM and lead workflows
- marketing and content workflows
- finance workflows
- website-builder workflows
- first-customer / first-dollar workflows
- multi-agent / AI-team orchestration
- usage, quota, cost and telemetry controls

## Architecture

**Frontend / application**
- Next.js
- React
- TypeScript
- monorepo architecture

**Data / auth**
- Supabase
- PostgreSQL
- server-side auth and privileged service-role boundaries

**AI layer**
- OpenAI and Anthropic provider abstraction
- structured JSON generation
- shared business context
- task-aware model routing
- usage tracking and quota enforcement

**Delivery / operations**
- GitHub
- Vercel
- environment-based configuration
- mock/live operating modes

See [ARCHITECTURE.md](ARCHITECTURE.md) for the sanitized system design.

## Engineering decisions demonstrated

- separated AI-provider concerns from business/domain logic
- centralized shared business context instead of isolated prompt-by-prompt state
- kept privileged credentials server-only
- added production configuration validation and safe failure modes
- introduced usage/cost tracking and per-user quota controls
- added a kill switch for paid AI calls
- prevented quota accounting from treating a multi-step validation run as multiple founder runs
- added caching to reduce repeated paid AI calls
- handled platform-specific native dependency failures during Vercel deployment
- preserved mock mode for local development while failing safely in misconfigured production environments

## Verification approach

The build process emphasizes verification rather than assuming generated code is correct.

Examples of verification used during development:

- production builds
- ESLint checks
- automated tests
- live dev-server checks
- real-provider API tests when credentials were available
- two-user isolation checks
- quota accounting checks
- direct inspection of stored AI usage records
- validation of token/cost calculations
- deployment-specific debugging
- explicit disclosure when a scenario could not be fully verified

See [VERIFICATION.md](VERIFICATION.md) for the reliability and QA approach.

## Example production problems solved

### 1. AI quota false positive
A multi-step validation flow wrote three usage records for one founder-initiated run, causing a nominal 5-runs/day limit to behave like roughly 1–2 runs/day. The fix changed quota accounting to count founder runs rather than every internal AI request while still recording all usage for cost telemetry.

### 2. Missing Linux native binaries in Vercel
A workspace dependency chain resolved correctly on Windows but dropped native Linux optional binaries in the deployment environment. The dependency graph and lockfile were corrected so the required platform binaries became explicit install targets without committing binaries or changing application behavior.

### 3. Unsafe production fallback
Local mock auth was useful for development, but a misconfigured production environment must not silently behave like a demo. Production configuration checks were added so missing real auth credentials fail safely and clearly instead of falling back to mock authentication.

### 4. Provider configuration ambiguity
AI-provider selection and credentials were made explicit and validated. Misconfiguration is surfaced safely without exposing secrets, while server-only credentials remain isolated from client components.

## What this case study demonstrates

This project demonstrates the ability to take a broad business problem and turn it into a structured AI-enabled software system, including product architecture, workflow design, AI orchestration, database/auth integration, deployment, testing, cost controls, security boundaries, and iterative production debugging.

## Private implementation

The production source repository remains private. Sanitized demos and smaller public examples are maintained separately so clients can inspect implementation quality without exposing proprietary code.