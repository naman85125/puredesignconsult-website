# PureDesign Consult Website

Marketing site for PureDesign Consult LLC — hardware sourcing and product design for the door and furniture industry.

## Stack

Pure HTML/CSS — no build step. Three pages plus shared stylesheet and SVG illustrations.

## Local preview

From this folder:

```
python -m http.server 8000
```

Then open <http://localhost:8000>.

## Structure

```
.
├── index.html                    Home
├── services.html                 Services
├── contact.html                  Contact
├── staticwebapp.config.json      Azure SWA routing & headers
├── css/
│   └── styles.css
└── images/
    ├── logo.svg                  Wordmark used in nav/footer
    ├── logo-mark.svg             Square mark (favicon)
    ├── illustration-hardware.svg Home intro panel
    ├── illustration-factory.svg  Services / Vendor Sourcing
    ├── illustration-cad.svg      Services / Product Design
    └── illustration-partnership.svg Services / Partner Matching
```

## Deployment

Hosted on Azure Static Web Apps (Free tier), connected to this GitHub repo.
Pushes to `main` trigger automatic deploy via GitHub Actions.

Custom domains: `puredesignconsult.com`, `www.puredesignconsult.com` (DNS at BigRock).

See `DEPLOYMENT.md` for full setup steps.

## License

© PureDesign Consult LLC. All rights reserved.
