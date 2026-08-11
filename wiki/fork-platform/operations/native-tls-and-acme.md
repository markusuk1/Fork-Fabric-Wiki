# Native TLS, ACME, and public domain routing

> **Updated:** 2026-07-24 · completed TLS line through fork build 24
> (`v2.7.0-gpu-default`), carried by build 27
>
> **Do not use build 21 to serve a domain to browsers.** Its guard read only the
> `Host` header, which HTTP/2 does not send, so it answered 421 to every browser
> request for a served domain over HTTPS. Fixed in build 23. Build 24 then
> fixed real Let's Encrypt challenge parsing after the local Pebble test had
> failed to expose Boulder's tokenless `dns-persist-01` response.

The fork terminates TLS itself — no external Caddy/nginx edge. It selects
certificates by SNI, obtains and renews them via ACME (HTTP-01), and routes
`domain → app` with fail-closed unknown-host handling. **Off by default**: with
no `[tls]` section (or `enabled = false`) the host is plain-HTTP only and
behaves exactly as a build without TLS support.

## Enable it

Minimal `config.toml`:

```toml
[tls]
enabled = true
https-port = 443            # default 443 when enabled

# For public (ACME) certificates — both required:
acme-directory-url = "https://acme-staging-v02.api.letsencrypt.org/directory"
acme-contact-email = "ops@example.com"
renew-before-days = 30      # renew this many days before expiry (per-domain jitter)

# Peers whose X-Forwarded-Proto/-Host you trust (empty = trust none):
trusted-proxies = ["10.0.0.1"]

[host-management]
admin-identities = ["<hex-identity>"]   # gates the domain-management API
```

## Changing `[tls]` settings

Every `[tls]` key is settable in `config.toml`, and every one is also settable at
runtime through the host-config facade (`spacetime host config set <key> <value>`,
then `apply`) — see [Host configuration management](host-configuration-management.md).

Most of them take effect **immediately on `apply`, with no restart**. `apply` tells
you which case you are in: it reports `restart_required` only when a changed key
genuinely cannot take effect live.

| Key | On `apply` |
| --- | --- |
| `tls.trusted-proxies` | **live** — next request. Add or remove a load balancer without a restart. |
| `tls.http-redirect` | **live** — next request |
| `tls.hsts-enabled` | **live** — next response |
| `tls.hsts-include-subdomains` | **live** — next response |
| `tls.renew-before-days` | **live** — next renewal scan |
| `tls.acme-directory-url` | **live** — next issuance (one in flight finishes on the old directory) |
| `tls.acme-contact-email` | **live** — next issuance |
| `tls.acme-ca-cert-path` | **live** — next issuance |
| `tls.enabled` | restart — binds/unbinds the HTTPS listener |
| `tls.https-port` | restart — binds a socket |
| `tls.cert-store-dir` | restart — constructs the store; moving it live would orphan every issued certificate |

Configuring ACME on a running host is therefore enough to start issuance: the
renewal daemon idles when ACME is unset and picks it up on its next scan.

`tls.trusted-proxies` takes a list of **IP literals** — the allowlist is matched
against the canonical peer IP, so a hostname is rejected at stage time rather than
silently never matching. Entries are canonicalized and deduplicated the same way
the guard compares them, so `::ffff:127.0.0.1` and `127.0.0.1` are one entry.

`rollback` reverts live settings in the running host too, not just on disk.

## HSTS

Off by default, and **opt-in per domain** — a domain's own `hsts-enabled` is
authoritative in both directions:

| domain's `hsts-enabled` | result |
| --- | --- |
| `true` | HSTS sent |
| `false` | **not** sent, even if the host-wide `[tls] hsts-enabled` is on |
| omitted | inherits the host-wide `[tls] hsts-enabled` |

A host that is not in the registry (a loopback/LAN dev host) never receives HSTS:
nobody opted it in, and HSTS is keyed to the origin, so pinning `localhost` would
force *every* local service in that browser to HTTPS for a year.

`includeSubDomains` is a **separate** opt-in (`tls.hsts-include-subdomains`, off by
default). It commits every subdomain of a served domain to HTTPS for the full
max-age in any browser that saw the header — including subdomains served elsewhere,
over plain HTTP, possibly by someone else. Reversing it means serving `max-age=0`
for as long as clients cached the policy, so it is never implied by enabling HSTS.
`preload` is not offered at all.

Sent only over TLS (RFC 6797 §7.2 has a UA ignore it on a non-secure transport, and
sending it over plain HTTP just invites stripping), and never over an app's own
`Strict-Transport-Security` header if it set one.

## HTTP → HTTPS redirect

`tls.http-redirect` sends a registered domain's plain-HTTP traffic to HTTPS, but
**only once that domain actually has a usable certificate**. A `public_http01`
domain waiting on ACME keeps serving over plain HTTP rather than being redirected
into a handshake that cannot complete — which would take it down over both schemes.

The redirect is **307**, not 308: both preserve the method and body, but 308 is
cacheable, and browsers persist it. Since `http-redirect` applies live and can be
turned back off without a restart, a permanent redirect would outlive the setting.

`/v1/*` and the ACME challenge are never redirected — the control plane must stay
reachable on a host with only a self-signed certificate, and the CA fetches the
challenge over port 80.

## Domain modes

| `tls-mode` | Meaning |
| --- | --- |
| `local_http` | plain HTTP only (no cert) |
| `local_selfsigned` | a locally generated self-signed cert — a dev secure-context, no ACME; provisioned inline at registration |
| `public_http01` | a publicly-trusted cert via ACME HTTP-01; issuance starts automatically on registration |

## Manage domains (admin-gated)

The management API lives under `/v1/tls`; the domain registry is persisted to
`<data-dir>/tls/domains.json` and holds **no key material**.

```
GET    /v1/tls/domains                    # list every domain + cert status (secret-free)
PUT    /v1/tls/domains/<domain>           # register/update
POST   /v1/tls/domains/<domain>/renew     # force issuance/renewal now
POST   /v1/tls/domains/<domain>/disable   # stop serving (evicts the cert immediately)
DELETE /v1/tls/domains/<domain>           # remove record + on-disk material
```

`PUT` body: `{ "tls-mode", "route-target"?, "workspace-id"?, "app-id"?,
"proxy-adapter"?, "hsts-enabled"? }`. `cert-status` progresses
`pending-dns → issuing → active` (or `→ failed` with `last-error`).

Example — a self-signed dev domain, then a public one:

```
curl -X PUT https://host/v1/tls/domains/dev.example.com \
  -H "Authorization: Bearer <token>" -H 'Content-Type: application/json' \
  -d '{"tls-mode":"local_selfsigned"}'

curl -X PUT https://host/v1/tls/domains/app.example.com \
  -H "Authorization: Bearer <token>" -H 'Content-Type: application/json' \
  -d '{"tls-mode":"public_http01","route-target":"myapp"}'
# ...watch it go pending-dns → issuing → active:
curl https://host/v1/tls/domains -H "Authorization: Bearer <token>"
```

## Security model (what the audit locked down)

- **No key material anywhere observable.** Private keys live only on disk under
  `<data-dir>/tls/certs/<domain>/bundle.pem` (cert+key, one atomic file) and as
  opaque signing keys in memory. They never appear in a module table,
  `/v1/status`, the status API, a log line, or an error. A keyless
  `fullchain.pem` sits alongside for inspection.
- **The key file is restricted to this service, on both platforms.** `0600` file /
  `0700` dir on unix; on Windows a **protected DACL** granting only the account the
  server runs as, `LocalSystem` and `Administrators` — inherited permissions from
  the data directory are discarded, not merged. The restriction is applied while
  the file is still empty, so key bytes never exist in a file anyone else can read,
  and `rename` carries it through the atomic publish. If the key cannot be
  protected, the write **fails** rather than proceeding (so a cert store cannot
  live on FAT32/exFAT).
- **Fail-closed routing.** An unknown, missing, or public-IP-literal `Host` is
  answered **421 Misdirected Request** — never routed to an arbitrary app. Only
  loopback/private/link-local hosts (LAN + dev) are exempt. A disabled domain is
  evicted from the resolver immediately (its TLS handshake then fails).
- **Trusted proxies only.** `X-Forwarded-Proto/-Host` are honored only when the
  direct peer is on `trusted-proxies`; spoofed headers from anyone else are
  ignored and flagged (`tls.route.trusted_proxy_violation`).
- **Expired certs are not served.** The resolver refuses a certificate past its
  `notAfter`, and the renewal daemon backs off failures and gives up after a
  ceiling so a permanently-broken domain can't burn ACME quota.
- **HSTS off by default** — per-domain `hsts-enabled` opt-in only.

## Renewal

A background daemon scans on start and then every 6 hours, renewing certs within
their window (with deterministic per-domain jitter), issuing pending domains, and
retrying failed ones with exponential backoff. Renewal **hot-swaps** the
certificate into the resolver with no restart — established connections (incl.
SSE) survive; only new handshakes see the new cert.

## Operational notes

- **Windows key files need no manual ACL work.** The server locks the key down
  itself (see the security model above). It builds the ACL around **its own
  running account**, so changing the service account needs no action — the next
  write re-applies the restriction for the new identity. A store created by a
  build before 22 is re-hardened on its next renewal. Verify any time with
  `icacls <data-dir>\tls\certs\<domain>\bundle.pem`: expect exactly the service
  account, `SYSTEM` and `Administrators`, and **no `(I)`** (inherited) entries.
  Hardening only fails where the filesystem has no ACLs at all, and then it fails
  loudly rather than leaving the key exposed.
- **Local ACME testing.** Point `acme-directory-url` at a local
  [Pebble](https://github.com/letsencrypt/pebble) and set `acme-ca-cert-path` to
  Pebble's CA so the fork trusts the test directory. Use a DNS mock
  (`pebble-challtestsrv`) to resolve the test domain to the host, and set Pebble's
  VA `httpPort` to the fork's plain listener port so it fetches the HTTP-01
  challenge the fork serves. (Pebble ≥ that ships with instant-acme's protocol
  version; a very new Pebble may diverge — pin a matching release.)
- **Public issuance** needs inbound 80 (HTTP-01 validation) and 443 (serving)
  reachable at the domain's A record.

## See Also

- [Host configuration management](host-configuration-management.md)
- [Staged builds and compatibility](staged-builds-and-compat.md)
- [Fork versioning](fork-versioning.md)
- Web platform: [`../web/`](../web/)
