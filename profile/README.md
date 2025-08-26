# CGLabs-dev
**Mission:** Build portable, practical PoCs for L2/L3/L4 observability — shadow sampling, TPROXY, eBPF — with clean OSS facades and gated commercial modules.

> **TL;DR:** OSS packages live under `@cglabs-dev/*`; private/commercial live under `@cglabs/*`.
`@cglabs-dev/core` is a public orchestrator.
Providers: `@cglabs/core-pro` (private) or `@cglabs-dev/core-dev` (public).

---
## 📦 Repo model

- **Public (`@cglabs-dev/*`)**  
  Contracts, facades, reference implementations, safe demos.  
  Published to **npmjs** (mirrored to GitHub Packages).  
  Examples:  
  - `@cglabs-dev/core` — API & orchestrator (public).  
  - `@cglabs-dev/core-dev` — reference/demo provider.  
  - `@cglabs-dev/contracts` — shared schemas, types.  
  - `@cglabs-dev/sdk`, `@cglabs-dev/cli`, `@cglabs-dev/labs`.

- **Private (`@cglabs/*`)**  
  Production-grade dataplane, infra, and agent code.  
  Published to **GitHub Packages** (later Artifactory).  
  Access controlled by tenant/org tokens.  
  Examples:  
  - `@cglabs/core-prod` — production provider(s): A-mirror, B-inline, C-metadata.  
  - `@cglabs/infra` — Helm / Tofu / Pulumi with privileged values.  
  - `@cglabs/agent` — binary runner.

---
## 🧩 Core model

- **`@cglabs-dev/core` (public orchestrator)**  
  - Depends only on `@cglabs-dev/contracts`.  
  - Dynamically loads providers if present.  
  - If no provider is installed → clear error message.  
  - Always clean: no bundled mocks.

- **Providers:**  
  - `@cglabs/core-prod` (private) — production provider.  
    Validates token/tenant internally (OIDC/JWT/license).  
  - `@cglabs-dev/core-dev` (public) — reference/demo provider (safe OSS).  

```ts
// example: @cglabs-dev/core
export function loadProvider() {
  try { return require("@cglabs/core-prod"); } catch {}
  try { return require("@cglabs-dev/core-dev"); } catch {}
  throw new Error("No provider installed. Install prod (private) or dev (public).");
}
````

---
1. Orchestrator is always public.
2. Private provider enforces access at its own level.
3. Dev provider is optional for demos/tests.

---
## 🔐 Registry setup

```ini
@cglabs-dev:registry=https://registry.npmjs.org
@cglabs:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken=${CGLABS_TOKEN}
always-auth=false
```

* **Public packages** → npmjs (`@cglabs-dev/*`).
* **Private packages** → GitHub Packages / Artifactory (`@cglabs/*`).


> ℹ️ Note: `cglabs-dev` is the active OSS organization.
> Private/commercial modules live in the `@cglabs/*` scope with gated registry access.

