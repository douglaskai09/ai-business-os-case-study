# Sanitized Architecture Overview

## System shape

```text
Founder / User
    |
    v
Next.js Application
    |
    +--> Business Modules
    |      - discovery
    |      - validation
    |      - CRM
    |      - marketing
    |      - finance
    |      - website workflows
    |      - first-customer workflows
    |
    +--> Shared Business Context
    |      - founder state
    |      - business state
    |      - workflow state
    |
    +--> AI Service Layer
    |      - OpenAI / Anthropic abstraction
    |      - structured outputs
    |      - task-aware routing
    |      - usage + quota controls
    |
    +--> Data / Auth Layer
    |      - Supabase
    |      - PostgreSQL
    |      - server-side privileged operations
    |
    +--> Observability / Guardrails
           - usage records
           - cost accounting
           - quota checks
           - telemetry
           - kill switch
           - production configuration validation
```

## Design principles

### 1. Business context is a first-class system
The AI layer is not treated as a collection of unrelated chat prompts. Business and founder context are assembled into structured shared context so multiple workflows can reason from the same state.

### 2. AI providers are replaceable infrastructure
Provider-specific SDK calls are isolated behind a service abstraction so business logic is not tightly coupled to a single model vendor.

### 3. Paid AI calls pass through guardrails
Usage recording, quota checks, task routing, and cost controls are centralized around the paid-call boundary rather than left to individual UI components.

### 4. Sensitive credentials remain server-side
Privileged database and AI credentials are not exposed to browser-reachable components. Production configuration is validated before sensitive operations proceed.

### 5. Development convenience does not weaken production behavior
Mock mode supports local development without credentials, while production environments are designed to fail clearly when required real credentials are missing.

### 6. Verification is part of architecture
Build, lint, tests, live-server checks, real-provider checks, multi-user isolation, and deployment validation are treated as engineering work—not an afterthought.

## Example workflow: AI validation

```text
Founder submits business concept
        |
        v
Normalize / understand business model
        |
        v
Research / evidence step
        |
        v
Opportunity analysis
        |
        +--> structured result
        +--> confidence / provenance
        +--> cost + token telemetry
        +--> quota accounting
        |
        v
Founder-facing report
```

The internal AI calls can be multiple steps while founder-facing quota accounting can still represent one initiated validation run.

## Example workflow: safe AI request

```text
Request
  -> resolve active provider
  -> validate provider configuration
  -> check quota / kill switch
  -> route task to appropriate model tier
  -> execute provider call
  -> capture usage / latency / cost
  -> return structured result
  -> surface safe errors if configuration fails
```

## Deployment concerns addressed

- workspace / monorepo routing
- Linux native optional dependencies on Vercel
- environment-variable validation
- mock vs production execution behavior
- server-only credential boundaries
- cache behavior for repeat AI insights

This file intentionally omits proprietary prompts, private schemas, secrets, business scoring logic, and source code.