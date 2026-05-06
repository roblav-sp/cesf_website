# CESF Website — Centre d'Études Spirites Fraternité

Multi-language static website for the CESF Spiritist center in Montreal. Built with Hugo + Congo theme, hosted on AWS (S3 + CloudFront), with a serverless backend (Lambda + API Gateway).

## Stack

- **Hugo** v0.161.1+ with [Congo](https://jpanther.github.io/congo/) theme
- **AWS**: S3 (static hosting), CloudFront (CDN + WAF + HTTPS), Lambda (Python 3.12), API Gateway, SES, Secrets Manager
- **CMS**: Decap CMS at `/admin/` — GitHub OAuth via the `cms_auth` Lambda
- **CI/CD**: GitHub Actions → Hugo build → S3 sync → CloudFront invalidation (OIDC, no long-lived credentials)

## Local Development

```bash
# From this directory (cesf_website/)
hugo server --buildDrafts
```

Visit `http://localhost:1313/`. Language previews:
- Portuguese (primary): `http://localhost:1313/pt/`
- French: `http://localhost:1313/fr/`
- English: `http://localhost:1313/en/`

## Project Structure

```
cesf_website/
├── config/_default/        # Modular Hugo config (TOML, one file per concern)
│   ├── hugo.toml
│   ├── params.toml
│   ├── menus.{pt,fr,en}.toml
│   └── languages.{pt,fr,en}.toml
├── content/
│   ├── pt/                 # Portuguese — source of truth
│   ├── fr/                 # French translations
│   └── en/                 # English translations
├── data/
│   └── activities.yaml     # Activity schedule (used by templates + social_poster Lambda)
├── i18n/
│   └── {pt,fr,en}.toml     # UI strings (buttons, labels, nav)
├── layouts/                # Theme overrides (never edit themes/ directly)
├── static/
│   └── admin/
│       ├── config.yml      # Decap CMS configuration
│       └── image-gen.html  # Admin UI for AI image generation
└── themes/
    └── congo/              # Congo theme (do not edit)
```

## Content Workflow

Always edit Portuguese content first — it is the source of truth.

```bash
# New page (run from cesf_website/)
hugo new content/pt/my-page.md
# Then create matching files in content/fr/ and content/en/
```

Hugo automatically generates language switchers when files share the same slug across language directories.

UI strings (button labels, navigation text, footer): edit `i18n/{pt,fr,en}.toml`.  
Menus: edit `config/_default/menus.{pt,fr,en}.toml`.

## CMS (Decap CMS)

Access at `https://cesf.ca/admin/`. Login uses GitHub OAuth handled by the `cms_auth` Lambda. CMS config is at `static/admin/config.yml`.

## Deployment

Fully automated on push to `main`:

1. GitHub Actions builds the site with `hugo`
2. Syncs `public/` to S3 bucket `cesf-website-bucket-604834691797-us-east-1-an`
3. Invalidates CloudFront distribution `E1PAM3XAWP0JIH`

Manual deploy (requires AWS CLI with the `cesf-github-deploy` role or equivalent):

```bash
hugo
aws s3 sync public/ s3://cesf-website-bucket-604834691797-us-east-1-an/ --delete
aws cloudfront create-invalidation --distribution-id E1PAM3XAWP0JIH --paths "/*"
```

## Validation

Run from the repo root (`cesf_admin/`):

```bash
python scripts/validate_site.py   # Validates all Markdown frontmatter and YAML
python scripts/test_domain.py     # Smoke-tests 28 checks against cesf.ca
```

## License

Content is proprietary to CESF. Congo theme is MIT licensed.
