# CaaS — CLI as a Service

> The post-SaaS architecture for the agent era.

**Status**: Living specification, v0.1 draft (May 2026)
**Author**: [@irrenwill](https://github.com/irrenwill)
**License**: [CC BY 4.0](./LICENSE)

---

## What is CaaS?

**CaaS (CLI as a Service)** is an architectural pattern where the
command-line interface *is* the service — the single, sovereign API
consumed by humans, agents, and graphical interfaces alike.

In a CaaS application:

- The **CLI** is the source of truth. Every action is a CLI command.
- The **GUI** is a client of the CLI.
- The **agent** (Claude Code, Cursor, Windsurf, etc.) is a client of the CLI.
- The **MCP server**, if present, is a thin wrapper over the CLI.
- The **cloud**, if present, is an optional value-add — not a dependency.

This inverts the assumption that SaaS made for the past 15 years:
that the web app is the product and everything else is an integration.

In CaaS, **the CLI is the product**. Everything else is an interface.

---

## Why now?

CaaS became viable in 2024–2026 because five building blocks finally arrived together:

1. **Tauri v2** (late 2024) — native cross-platform GUIs with first-class
   sidecar CLI support, in <20 MB binaries.
2. **MCP** (late 2024) — a standard protocol for exposing tools to agents.
3. **Skills** (2025) — agent-portable capability packages, now running on
   40+ agent runtimes.
4. **Claude Code, Cursor, Windsurf** (2024–2025) — agents as primary
   developer interfaces, normalizing the idea that *talking to your tool*
   is the new GUI.
5. **BYOK economics** (2025–2026) — model APIs (Anthropic, OpenAI, fal.ai,
   ByteDance, Replicate) finally cheap and aggregated enough that users
   bringing their own keys is a real distribution path, not a curiosity.

Before all five existed, CaaS was theoretically possible but practically
expensive. After 2026, it is the lowest-friction path for indie developers
who want to build durable, sovereign software.

---

## The five principles

### 1. The CLI is the API

There is no separate "internal API" and "CLI." The CLI binary *is* the
service contract. Every feature is exposed as a CLI command with both a
human-readable output and a `--json` machine-readable output. If something
cannot be done from the CLI, it does not exist in the product.

### 2. Local-first by default

User data lives on the user's disk in open formats (SQLite, JSON,
plaintext). The application functions fully without network access for
all core workflows. Cloud features are additive, never load-bearing.

### 3. BYOK over multi-tenant

Users bring their own API keys to model providers, payment gateways, and
storage. The application stores keys in the OS keychain, never on a
remote server. This eliminates the SaaS unit-economics trap where
per-user inference cost exceeds subscription revenue.

### 4. GUIs and agents are clients, not masters

The Tauri/Electron/native GUI calls the CLI via subprocess or sidecar.
Agents call the CLI via the same interface. New clients (VS Code
extensions, Discord bots, mobile apps) can be added without touching
business logic, because business logic lives in the CLI.

### 5. Open core, optional cloud

The CLI, daemon, and GUI ship under a permissive open-source license
(MIT/Apache). Revenue, if desired, comes from optional cloud add-ons:
encrypted sync, hosted MCP endpoints, team collaboration, managed
backups. Following the Obsidian playbook: never paywall features users
already have locally.

---

## How CaaS differs from SaaS

| Dimension | SaaS | CaaS |
|---|---|---|
| Source of truth | Vendor's database | User's disk |
| Primary interface | Web browser | CLI + GUI + agent (all peers) |
| API access | Often paid tier, rate-limited | Free, native, no auth ceremony |
| Agent integration | After-thought, via REST | First-class, via subprocess or MCP |
| Vendor death risk | Total (user loses everything) | Minimal (CLI keeps working offline) |
| Unit economics | Per-user infra cost grows linearly | Marginal — most compute is user-owned |
| Distribution | Paid acquisition + sales | Open-source community + word of mouth |
| Maintainability for solo devs | Hard (multi-tenant, ops, scaling) | Easy (single-user runtime per machine) |
| Privacy story | "Trust us" | "Your disk, your keys, your files" |

---

## Existing tools that exemplify CaaS principles

These tools predate the term but embody the architecture:

- **1Password** — CLI (`op`) + Desktop app + browser extension, all
  clients of a shared local daemon. Authentication via OS biometrics.
  Cloud sync is optional and end-to-end encrypted.
- **Obsidian** — files on disk, free GUI, optional Sync ($5/mo) and
  Publish ($10/mo). ~$25M ARR with 9 employees. The canonical proof that
  this model funds an indie team.
- **GitHub CLI (`gh`)** — every web UI action available as a CLI command,
  enabling clean agent integration without separate API design.
- **Aider** — agent-native from day one, CLI-first, BYOK to model providers.
- **Stripe CLI**, **Supabase CLI**, **Vercel CLI** — services that exposed
  CLI-as-peer-interface alongside their web dashboards.

CaaS formalizes what these tools converged on independently.

---

## Who should adopt CaaS

CaaS is the right architecture if:

- You are an **indie or small team** without infra/ops budget.
- Your users care about **privacy, ownership, or offline use**.
- Your application benefits from **agent automation**.
- Your unit economics **don't work as flat-rate SaaS** (e.g., AI inference,
  large compute, large storage).
- You want **distribution leverage from open-source community**.

CaaS is the wrong architecture if:

- Your application requires **real-time multi-user collaboration** as a
  core feature (Figma, Google Docs).
- Your users **cannot install software** (enterprise IT, ChromeOS).
- You need **VC-scale growth** ($100M+ ARR targets) — CaaS optimizes
  for sustainability over hypergrowth.

---

## What CaaS is not

- **Not "containerize your CLI."** That is Containers-as-a-Service, an
  unrelated cloud-ops concept from the 2010s.
- **Not "put your CLI in the cloud."** CaaS is local-first. The CLI runs
  on the user's machine.
- **Not "CLI-only."** CaaS applications usually have rich GUIs. The point
  is the GUI doesn't own the logic.
- **Not "anti-cloud."** Optional cloud add-ons are encouraged. They just
  cannot be load-bearing.

---

## Reference implementations

- **Tethea** — AI video production pipeline (in development, 2026)
- _More to come. PRs welcome._

---

## Contributing

This is a living specification. Issues and PRs welcome on:

- Refinements to principles
- Additional reference implementations
- Comparison case studies
- Translations (繁體中文 / 简体中文 / 日本語 / others)

---

## Glossary

- **Sovereign client** — A client (GUI, agent, MCP server) that consumes
  the CLI as its API and adds no business logic of its own.
- **Sidecar** — A CLI binary bundled alongside a GUI app, invoked via
  subprocess. Tauri v2 has first-class support.
- **BYOK** — Bring Your Own Key. User-supplied API credentials stored in
  the OS keychain, never transmitted to the application vendor.
- **Daemon** — A long-running local process that holds shared state for
  multiple clients (GUI + CLI invocations). Optional but common.

---

## Citation

If you cite CaaS in writing, please use:

> CaaS: CLI as a Service (v0.1). irrenwill, 2026.
> https://github.com/irrenwill/caas-spec
