# PureDesign Consult — Azure Deployment Guide

This site is a static three-page website. The cleanest hosting option is **Azure Static Web Apps (Free tier)**.

---

## Folder structure

```
PureDesign Consult Website/
├── index.html         # Home
├── services.html      # Services
├── contact.html       # Contact
├── css/
│   └── styles.css
└── images/
    ├── logo.svg
    └── logo-mark.svg  # used as favicon
```

---

## Step 1 — Preview locally

Before deploying, open `index.html` in your browser. The cleanest way:

**Option A: Just double-click `index.html`** — works for most browsing.

**Option B: Run a local server** (recommended, especially if anything looks broken):

```bash
# from inside the project folder
python -m http.server 8000
# then visit http://localhost:8000
```

---

## Step 2 — Push to GitHub

Azure Static Web Apps deploys from a GitHub repo.

1. Create a new GitHub repo: `puredesignconsult-website`
2. From the project folder:
   ```bash
   git init
   git add .
   git commit -m "Initial site"
   git branch -M main
   git remote add origin https://github.com/<your-username>/puredesignconsult-website.git
   git push -u origin main
   ```

---

## Step 3 — Create Azure Static Web App

1. Sign in to [portal.azure.com](https://portal.azure.com).
2. Click **Create a resource** → search **Static Web App** → **Create**.
3. Fill in:
   - **Subscription**: your Azure subscription
   - **Resource Group**: create new, e.g., `puredesign-rg`
   - **Name**: `puredesignconsult`
   - **Plan type**: **Free**
   - **Region**: pick the closest one (e.g., East US 2 or Central US for FL traffic)
   - **Source**: **GitHub** → authorize → pick the repo and `main` branch
   - **Build presets**: **Custom**
   - **App location**: `/`
   - **Output location**: leave blank (no build step needed)
4. Click **Review + create** → **Create**.

Azure will provision the resource and add a GitHub Actions workflow to your repo. Within ~3 minutes, you'll get a default URL like `https://<random-name>.azurestaticapps.net` — visit it to confirm the site is live.

---

## Step 4 — Add custom domain

In your new Static Web App resource:

1. Open **Custom domains** (left menu).
2. Click **+ Add** → choose **Custom domain on other DNS**.
3. Enter `www.puredesignconsult.com` first → Azure will show you a **CNAME** record value (something like `<random-name>.azurestaticapps.net`).
4. Repeat for the apex: `puredesignconsult.com` → Azure will give you a **TXT validation record**, then an **ALIAS/ANAME** (or A record) target.

**In your BigRock DNS panel**, add these records exactly as Azure shows them:

| Type | Host | Value |
|---|---|---|
| CNAME | `www` | `<random-name>.azurestaticapps.net` (from Azure) |
| TXT | `_dnsauth` (or whatever Azure says) | the validation token Azure provides |
| ALIAS / ANAME | `@` | `<random-name>.azurestaticapps.net` |

> If BigRock doesn't support ALIAS/ANAME for the apex, use the **A records** Azure provides as a fallback.

5. Back in Azure, click **Validate** for each domain. DNS propagation usually completes in 15 minutes to a few hours.
6. Once validated, Azure auto-issues a free SSL certificate. The site will be live at `https://puredesignconsult.com` and `https://www.puredesignconsult.com`.

---

## Step 5 — (Recommended) Apex → www redirect

Most sites canonicalize to one domain. To redirect `puredesignconsult.com` → `www.puredesignconsult.com`, create a `staticwebapp.config.json` in the project root:

```json
{
  "globalHeaders": {
    "Strict-Transport-Security": "max-age=63072000; includeSubDomains; preload"
  },
  "responseOverrides": {
    "404": { "rewrite": "/index.html", "statusCode": 200 }
  }
}
```

Note: SWA doesn't natively redirect apex→www; the simplest approach is to set both domains as live and pick one canonical version in your sitemap and external links. If strict redirect is required, route DNS through Cloudflare (free) and configure a Page Rule.

---

## Step 6 — Future updates

Any push to `main` triggers an automatic redeploy via the GitHub Action Azure created. Edit files locally, commit, push — site updates within a couple minutes.

---

## Cost summary

- **Azure Static Web Apps Free tier**: $0/month
  - 100 GB bandwidth/month included
  - Free managed SSL on custom domains
  - Global CDN included
- **BigRock domain renewal**: per your existing plan

Total ongoing cost: **₹0** for hosting.

---

## Replacing stock imagery

The hero and service images currently load from Unsplash CDN URLs. To swap to your own product photos:

1. Save your photos into the `images/` folder (e.g., `images/hero.jpg`, `images/sourcing.jpg`).
2. In `index.html` — find `https://images.unsplash.com/...` and replace the URL with `images/hero.jpg`.
3. Same in `services.html` for the service detail blocks.
4. Recommended sizes: hero 1920×1080, service detail 1200×900, all under 300 KB each (use [Squoosh.app](https://squoosh.app) to optimize).

---

## Adding a contact form later (optional)

If you decide later to add a contact form (no server needed):

- **Formspree** (free tier: 50 submissions/month): [formspree.io](https://formspree.io)
- **Web3Forms** (free, no signup): [web3forms.com](https://web3forms.com)
- **Getform**: [getform.io](https://getform.io)

All three give you an HTML form action URL — paste their snippet in and submissions get emailed to you.
