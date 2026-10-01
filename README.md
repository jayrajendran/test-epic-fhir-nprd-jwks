# JWKS host — non-production

Static JSON Web Key Set for a SMART Backend Services FHIR integration, sandbox use only.
Published via GitHub Pages so the authorization server can fetch it.

## What is in here

| File | Purpose |
|---|---|
| `jwks-nonprod.json` | The key set. One RSA public key, `alg: RS384`, `use: sig`. |
| `index.html` | A short human-readable landing page. |
| `.nojekyll` | Tells GitHub Pages to serve files verbatim, with no Jekyll processing. |
| `.gitignore` | Blocks private key material from ever being committed here. |

## This repository is public, and that is correct

A JWKS holds **public key material only** — the modulus (`n`) and exponent (`e`) of an RSA public
key. It is designed to be world-readable: that is the mechanism by which a verifying party fetches
the key to check a signature. Publishing it discloses no secret and grants no access.

The matching **private** key is never stored here. It lives in the secrets store and only ever
signs locally.

## Do not

- Commit any `.pem`, `.key`, or private key material. `.gitignore` blocks the obvious names, but it
  is not a substitute for care.
- Move or rename `jwks-nonprod.json`. The authorization server re-fetches this URL; if it 404s,
  authentication stops working.
- Add the production key set here. That is a separate decision with a separate, durable location.

## Rotating a key

Add the new key to the `keys` array **alongside** the existing one, each with its own `kid`. Publish,
wait for the consumer to re-sync, switch the signing service to the new `kid`, then remove the old
entry in a later commit. Both keys are valid during the overlap, so there is no downtime.
