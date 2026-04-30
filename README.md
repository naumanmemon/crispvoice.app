# crispvoice.app — marketing site

Static site for CrispVoice. No build step, no JS framework — just HTML / CSS / a tiny inline script for the year stamp.

## Local preview

```sh
cd website
python3 -m http.server 8080
# open http://localhost:8080
```

Or any static server (`npx serve`, `caddy file-server`, etc.).

## Deploy to Azure Static Web Apps

The repo is structured so the `website/` directory is the publishable root.

### One-time

1. In the Azure portal, create a **Static Web App** resource.
2. Source: **Other** (skips the GitHub Action — we'll deploy with the CLI). Or pick **GitHub** and point it at this repo with `app_location: website` and `api_location: ""` and `output_location: ""`.
3. Note the deployment token in **Manage deployment token**.

### Manual deploy via SWA CLI

```sh
npm install -g @azure/static-web-apps-cli
cd /path/to/this/repo
swa deploy ./website --deployment-token <TOKEN> --env production
```

### Custom domain

1. In the Static Web App resource, **Settings → Custom domains → Add**.
2. Enter `crispvoice.app`. Azure issues a managed cert automatically.
3. At your DNS provider, add the `ALIAS` / `ANAME` (apex) and `CNAME` (`www`) records Azure shows you.
4. Wait for DNS to propagate, then validate. HTTPS is enforced by default.

## What's in `staticwebapp.config.json`

- **Security headers** — HSTS, CSP, frame-deny, referrer policy, permissions policy.
- **Cache rules** — long-cache for `/assets/*`, short-cache for HTML.
- **404 fallback** — unmatched routes serve `/404.html` with a `404` status.

CSP allows inline `<style>` and inline `<script>` because the site uses one tiny inline year stamp; tighten to `'self'` only if that script is removed.

## Files

| File | Purpose |
| --- | --- |
| `index.html` | Marketing landing |
| `privacy.html` | Privacy policy (App Store §5.1.1) |
| `terms.html` | Terms of Use / EULA (App Store §3.1.2 disclosures) |
| `support.html` | Support / FAQ (App Store support URL) |
| `404.html` | Custom not-found page |
| `styles.css` | Single stylesheet, design tokens mirror the iOS app |
| `staticwebapp.config.json` | Azure routing + headers |
| `robots.txt`, `sitemap.xml`, `site.webmanifest` | SEO / PWA basics |
| `assets/icon.svg`, `apple-touch-icon.png`, `og-image.png` | Brand assets |

## App Store coverage

- **Privacy Policy URL**: `https://crispvoice.app/privacy.html`
- **Marketing URL**: `https://crispvoice.app/`
- **Support URL**: `https://crispvoice.app/support.html`
- **EULA**: `https://crispvoice.app/terms.html` (or use Apple's standard EULA).

The pricing section on the home page and the §2 of the Terms include the App Store §3.1.2 subscription disclosures (title, length, price, auto-renewal language, links to Privacy and Terms, restore note).

## Future TODO

- App Store badge image (PNG) once the app is live — drop into `/assets/` and swap the `#download` text link for `<img>`.
- Real App Store URL (`apps.apple.com/app/crispvoice/id…`) — placeholder right now.
- Open Graph image: replace `og-image.png` with a 1200×630 marketing render once we have hero shots.
