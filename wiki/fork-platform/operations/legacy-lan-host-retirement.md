# Legacy LAN Host Retirement

> Sources: [LAN-LEGACY-001 completion](../../docs/completions/COMP-LAN-LEGACY-001.md); [final audit](../../docs/audits/AUD-LAN-LEGACY-001.md); [historical build-17 promotion](../../raw/operations/2026-07-16-pred004-build17-production-promotion.md); [current release state](../../docs/release-state.json)
> Updated: 2026-07-30

## Current status

`192.168.1.211:3000` is the **legacy LAN host**. It is not production, not the
AI-Collab database substrate, and not a current Fork manual endpoint.

- Windows service: `SpacetimeDB`
- State: `Stopped`
- Startup type: `Disabled`
- Port 3000: no listener
- HTTP endpoint: unreachable
- Last executable: immutable build 17, `v2.7.0-pred004-r2`
- Preserved module: `agent-starter`

The independently supervised public/local runtime at `127.0.0.1:3012` and the
public version endpoint both report accepted build 36 after its 2026-07-30
production promotion. This does not change the retired status of port 3000.

## Why it was retired

The listener was originally the LAN production Fork tool host. Later, the
public/local runtime moved forward independently while NSSM remained pinned to
build 17. Current inspection found no connected client and no active runtime
configuration consuming port 3000. The only live surface was the old
`agent-starter` manual/health route.

AI-Collab still contains two seed-goal strings describing port 3000 as active
and assigning it to `FORK_BASE_URL`. Those are stale downstream documentation,
not a runtime dependency, and must not be used to revive the host. The exact
cleanup is captured in the
[outbound AI-Collab request](../../docs/requests/FORK-REQ-ai-collab-retire-legacy-lan-references.md).
Connected delivery was unavailable because this caller is not a `WS-1` member;
the repo-local client also returned `fetch failed`.

## What was preserved

Retirement did not uninstall the service or delete anything. The following
remain available as rollback evidence:

- NSSM service definition, application, working directory and arguments;
- immutable build-17 binaries;
- `D:/Projects/SpacetimeDB_Builds/lan-deploy/data`;
- the existing JWT directory;
- the `agent-starter` database state and logs.

## Reactivation rule

Do not simply change the service back to Automatic. A future consumer must
first establish that it needs a distinct LAN database host, select a current
compatible Fork stage, validate database/module migration and ownership, define
the intended endpoint, and complete a rollback-protected promotion. Until that
explicit decision, port 3000 is reserved legacy state.

## See also

- [Live Deployment](live-deployment.md)
- [Fork Versioning & Drift Detection](fork-versioning.md)
- [Staged Builds & Compatibility](staged-builds-and-compat.md)
- [Generic Managed Service Plane](managed-service-plane.md)
- [AI-Collab cleanup request](../../docs/requests/FORK-REQ-ai-collab-retire-legacy-lan-references.md)
