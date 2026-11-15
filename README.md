# WARP Config Generator — Cloudflare one-click template

[![Deploy to Cloudflare](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/MCFCMARK/warp-1-click/)

A minimal, self-contained **Cloudflare Worker**. No VPS, PHP, database, Cloudflare KV, or secrets are required for deployment.

## Publish your one-click button (once)

1. On GitHub, create a **public** repository (for example, `warp-config-oneclick`).
2. Choose **Add file → Upload files**, upload the *contents* of this package, keeping `src/worker.mjs`, `package.json`, and `wrangler.toml` in the repository root. GitHub's web uploader may not preserve nested folders when uploading them individually; use the **Create new file** flow with the full path (`src/worker.mjs`) if necessary, or import the contents using an existing Git workflow. No PC software installation is required.
3. In this README, replace `REPLACE-OWNER/REPLACE-REPOSITORY` in the button's URL with your actual `GitHubUser/RepositoryName` and commit the edit. For example: `https://deploy.workers.cloudflare.com/?url=https://github.com/GitHubUser/warp-config-oneclick`.
4. Open the README, click **Deploy to Cloudflare**, approve the GitHub and Cloudflare permissions, and confirm the deploy setup. Cloudflare will create the Worker and publish its `*.workers.dev` URL.

**That's it.** After the repository and link are prepared, future installations use the one-click button (followed by Cloudflare's authorisation/confirmation screens). The deployment process also creates a Git-linked deployment for updates.

If you don't need a public one-click button, in Cloudflare go to **Workers & Pages → Create application → GitHub**, select the repository and deploy it. This uses the same source files.

## Use and verify

- Open `https://YOUR-WORKER.YOUR-SUBDOMAIN.workers.dev/` to see the WARP configuration form.
- Open `https://YOUR-WORKER.YOUR-SUBDOMAIN.workers.dev/health` to check that the code is running. This route does **not** test WARP registration.
- Enter a profile name, accept the terms, and click **Create & Download Config** to try a registration and download a WireGuard `.conf` file.

The app does not persist profile private keys or registration tokens in application storage. The endpoint has a lightweight **best-effort, per-Cloudflare-location** 45-second IP throttle; it is **not** strong global anti-abuse protection.

## Compatibility and important limitation

The supplied generator depends on a *separate*, undocumented Cloudflare WARP registration API (`v0a5641`). Its TLS/client-fingerprint checks may return **HTTP 403** to traffic originating from Cloudflare Workers. The source tries both a raw TLS connection and a standard Worker `fetch`, but either can be rejected. **Deployment success and `/health` do not prove registrations will work**; test generation immediately after deployment. A one-click installer cannot fix upstream API rejection.

The Worker uses supported Node.js imports (`node:crypto`, `node:tls`, `node:buffer`) with a compatibility date after 2026-08-04, when Node.js compatibility is enabled by default. The Wrangler flag `no_throw_on_not_implemented_tls_options` is retained from the supplied project.

## Project structure

- `src/worker.mjs` — site, API handler, WireGuard config generator, and WARP registration logic.
- `wrangler.toml` — Cloudflare Worker configuration.
- `package.json` — deployment and test commands; Wrangler is used by Cloudflare.
- `test/worker.test.mjs` — offline logic tests (no WARP registration).

## Verification without installing anything on your PC

The included tests were run with `node --test` in the preparation environment. They are unit tests for local logic only, not a live deployment or upstream API integration test.

## Terms and affiliation

This is an unofficial project and is not affiliated with or endorsed by Cloudflare. Only create WARP accounts/devices in accordance with Cloudflare's current terms and usage limits.

## Official documentation

- [Cloudflare's Deploy to Cloudflare button documentation](https://developers.cloudflare.com/workers/platform/deploy-buttons/)
- [Cloudflare Workers Builds (Git integration)](https://developers.cloudflare.com/workers/ci-cd/builds/)
- [Node.js compatibility in Workers](https://developers.cloudflare.com/workers/configuration/compatibility-flags/)
