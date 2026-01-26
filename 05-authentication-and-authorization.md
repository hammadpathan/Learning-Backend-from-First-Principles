# 5. Authentication and Authorization

Authentication answers "Who are you?" while authorization answers "What can you do?". Both must work together to protect data and capabilities.

## Authentication essentials
- Early trust-based methods (seals, pass-phrases) gave way to passwords, hashing, and asymmetric crypto (Diffie-Hellman, PKI).
- Multi-factor authentication (MFA) combines something you know, have, and are to resist brute-force and credential reuse.
- Modern trends: OAuth 2.0, JWT, zero-trust thinking, passwordless (WebAuthn), and post-quantum/behavioral approaches on the horizon.

## Sessions, cookies, and JWTs
### Stateful sessions (cookie + server store)
1) Client submits credentials; server validates.
2) Server creates a session ID and stores user context in a fast store (e.g., Redis) with expiry.
3) Session ID is sent as an HttpOnly cookie; the browser sends it on every request.
4) Server looks up the session ID to authorize the request; can revoke centrally.

### Stateless tokens (JWT)
- Self-contained token: header (alg, typ), payload (claims like `sub`, `role`, `iat`), signature (HMAC/RS/EC) over base64url-encoded header+payload.
- Sent typically in the `Authorization: Bearer <JWT>` header; servers validate signature and claims without a session store.
- Pros: no server-side session storage, cross-region portability.
- Cons: theft enables impersonation until expiry; revocation is hard without maintaining a denylist.
- Hybrid: keep JWTs but also track allow/deny lists in a store to blacklist compromised tokens.
- Example layout: [JWT sample](og_notes/images/5-jwtexample.png).

### Cookies in auth flows
- Server sets a cookie (often HttpOnly/SameSite) carrying a session ID or token.
- Browser sends it automatically on same-site requests; good fit for session-backed auth.

## Choosing stateful vs stateless
- Stateful: central control, easy revocation, good default for most SaaS apps; requires shared store and coordination across regions.
- Stateless: scales easily and suits distributed APIs/mobile; revocation and rotation are harder, so keep TTLs short and use rotation/denylists if needed.

## API key authentication
- One-sided secret for machine-to-machine access; identifies the caller and enables usage tracking/quotas.
- Simple to issue and rotate; lacks built-in user identity or fine-grained scopes unless layered with additional controls.

## OAuth 2.0 and OpenID Connect (OIDC)
- Delegation model so one app can access another app's resources without password sharing.
- Actors: resource owner (user), client (app), authorization server (issues tokens), resource server (protects APIs).
- Common flows:
  - Authorization Code (with PKCE in public clients): standard for web/mobile apps.
  - Client Credentials: machine-to-machine, no end user.
  - Device Code: for devices with limited input (TVs, consoles).
- Access token authorizes API calls; optional refresh token for longevity.
- OIDC adds an ID token (JWT) to convey authenticated user identity, enabling "Sign in with X" experiences.

## Authorization (what you can do)
- Role-Based Access Control (RBAC): assign roles (admin, moderator, user) that map to permissions.
- Typical flow: user authenticates -> server resolves role (from token claims or DB) -> enforcement checks role/permission on each action.
- Apply least privilege; keep role/permission checks close to the resource being protected.

## Operational hardening tips
- Error messages: keep responses generic (e.g., "Invalid credentials") to avoid hinting whether a user exists or which step failed.
- Timing attacks: avoid response-time differences that leak state. Use constant-time comparisons for secrets and consider adding uniform delays before responding.
