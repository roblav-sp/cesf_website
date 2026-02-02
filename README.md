# CESF Website - Centre d'Études Spirites Fraternité

A modern, multi-language static website for the CESF Spiritist center in Montreal, built with Hugo and hosted on AWS.

## Overview

This website provides information about Spiritism, CESF's activities, teachings, and community services. It supports three languages (Portuguese, French, English) and includes:

- **Multi-language Support**: Portuguese (primary), French, and English
- **Content Management**: Netlify CMS for easy content editing without coding
- **Responsive Design**: Ananke theme with mobile-friendly layout
- **Contact Form**: Lambda-based serverless form handling with SES email
- **Social Media Integration**: Links to YouTube, Facebook, and Instagram

## Project Structure

```
site/
├── content/
│   ├── pt/          # Portuguese content (primary)
│   ├── fr/          # French content (translations)
│   └── en/          # English content (translations)
├── layouts/         # Custom HTML templates
├── static/
│   └── admin/       # Netlify CMS configuration
├── themes/
│   └── ananke/      # Hugo theme
├── hugo.toml        # Hugo site configuration
└── package.json     # Node.js dependencies (optional)
```

## Prerequisites

- **Hugo** (v0.152+): `sudo snap install hugo`
- **Git**: For version control and CMS git-gateway
- **GitHub Account**: For deploying the site and using Netlify CMS
- **AWS Account**: For S3, CloudFront, Lambda, and SES (optional for advanced features)

## Local Development

### 1. Clone and Install

```bash
cd /home/texugo/projects/cesf_website/site
git submodule update --init --recursive  # Install theme
```

### 2. Run Locally

```bash
hugo server --buildDrafts
```

Visit `http://localhost:1313/` to preview the site.

- **Portuguese**: http://localhost:1313/pt/
- **French**: http://localhost:1313/fr/
- **English**: http://localhost:1313/en/

### 3. Create New Content

Add a new page in Portuguese:

```bash
hugo new content/pt/my-page.md
```

Edit the file in `content/pt/my-page.md` and run `hugo server` to see changes live.

## Netlify CMS Setup

### 1. Enable Git Gateway

Netlify CMS uses Git-Gateway to manage content via Git commits. To set it up:

1. Push the site to GitHub
2. Connect to Netlify Hosting or AWS Amplify
3. Enable Identity and Git Gateway in Netlify settings
4. Log in at `https://yoursite.com/admin/`

### 2. Edit Content via CMS

- Visit `/admin/` to access the CMS dashboard
- Edit or create pages without touching code
- Changes are automatically committed to Git

### Configuration

CMS configuration is in `static/admin/config.yml`:

```yaml
backend:
  name: git-gateway
  branch: main

collections:
  - name: "pages"
    label: "Pages"
    folder: "content/pt"
    create: true
    fields:
      - {label: "Title", name: "title", widget: "string"}
      - {label: "Body", name: "body", widget: "markdown"}
```

## Deployment

### Option 1: AWS Amplify (Recommended)

1. Push to GitHub
2. Open AWS Amplify Console
3. Connect your GitHub repo
4. Amplify auto-builds and deploys to S3/CloudFront
5. Set custom domain `cesf.ca` via Route 53

Benefits:
- Automatic builds on Git push
- Free SSL/HTTPS
- Global CDN via CloudFront
- Low costs for <100 users/month

### Option 2: Manual AWS S3 + CloudFront

```bash
# Build the static site
hugo

# Upload to S3
aws s3 sync public/ s3://cesf-website-bucket/ --delete

# Invalidate CloudFront cache
aws cloudfront create-invalidation --distribution-id YOUR_DIST_ID --paths "/*"
```

### Option 3: Netlify Hosting

1. Connect GitHub repo to Netlify
2. Build command: `hugo`
3. Publish directory: `public`
4. Netlify handles builds, hosting, and SSL automatically

## Contact Form with AWS Lambda

### Frontend (Already Added)

The contact form in `content/pt/contact.md` submits to an API Gateway endpoint.

### Backend Setup (To Do)

Create a Lambda function to:

1. Receive form data from API Gateway
2. Validate input (check for spam)
3. Send email via AWS SES to `atendimento@cesf.ca`

**Example Lambda function (Node.js)**:

```javascript
const AWS = require('aws-sdk');
const ses = new AWS.SES();

exports.handler = async (event) => {
    const { name, email, message } = JSON.parse(event.body);

    // Validate input
    if (!name || !email || !message) {
        return {
            statusCode: 400,
            body: JSON.stringify({ error: 'Missing required fields' })
        };
    }

    // Send email via SES
    const params = {
        Source: 'noreply@cesf.ca',
        Destination: { ToAddresses: ['atendimento@cesf.ca'] },
        Message: {
            Subject: { Data: `New Contact Form Submission from ${name}` },
            Body: { Text: { Data: `Email: ${email}\n\nMessage:\n${message}` } }
        }
    };

    try {
        await ses.sendEmail(params).promise();
        return {
            statusCode: 200,
            body: JSON.stringify({ message: 'Email sent successfully' })
        };
    } catch (error) {
        return {
            statusCode: 500,
            body: JSON.stringify({ error: error.message })
        };
    }
};
```

## Social Media Integration

### YouTube Embeds

Add to a page:

```markdown
<iframe width="560" height="315" src="https://www.youtube.com/embed/VIDEO_ID" 
        title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; 
        clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
        allowfullscreen></iframe>
```

### Facebook and Instagram

Update `hugo.toml` with social media URLs, and add links to pages:

```markdown
[Follow us on Facebook](https://facebook.com/cesf)
[Follow us on Instagram](https://instagram.com/cesf)
```

## Multi-Language Configuration

### Language Structure

- **Default**: Portuguese (`defaultContentLanguage = 'pt'`)
- **Secondary**: French and English (stubs - to be translated)

Hugo automatically:
- Routes `/pt/*` to Portuguese content
- Routes `/fr/*` to French content
- Routes `/en/*` to English content

### Adding Translations

1. Create corresponding files in each language folder:
   ```
   content/pt/historia.md
   content/fr/histoire.md
   content/en/history.md
   ```

2. Hugo will match them and provide language switchers in the navigation.

## Configuration Reference

### `hugo.toml`

```toml
baseURL = 'https://cesf.ca/'
theme = 'ananke'
defaultContentLanguage = 'pt'

[languages]
[languages.pt]
title = "CESF - Centro Espírita São Francisco"
weight = 1

[languages.fr]
title = "CESF - Centre d'Études Spirites Fraternité"
weight = 2

[languages.en]
title = "CESF - Spiritist Study Center Fraternity"
weight = 3
```

## Performance & Scalability

- **Static Site**: Fast load times, SEO-friendly
- **Global CDN**: CloudFront caches content worldwide
- **Low Costs**: Free tier eligible for <100 users/month
- **Auto-scaling**: Lambda handles traffic spikes

## Security Best Practices

1. Enable HTTPS (free via AWS ACM or Netlify)
2. Restrict S3 bucket access (CloudFront only)
3. Use IAM roles for Lambda and SES
4. Add reCAPTCHA to contact form to prevent spam
5. Validate and sanitize form inputs on Lambda

## Troubleshooting

### Hugo server won't start

```bash
hugo server --buildDrafts --verbose
```

### Changes not appearing

- Clear browser cache (Ctrl+Shift+Del)
- Check `content/pt/` for the file
- Rebuild: `hugo`

### Netlify CMS not loading

- Ensure Git Gateway is enabled in Netlify
- Check `static/admin/config.yml` is present
- Browser console (F12) shows detailed errors

### Contact form not sending

- Check Lambda function logs in CloudWatch
- Verify SES has been tested in AWS (not sandbox mode)
- Check IAM permissions for Lambda role

## Resources

- [Hugo Documentation](https://gohugo.io/)
- [Netlify CMS Guide](https://www.netlifycms.org/)
- [Ananke Theme](https://themes.gohugo.io/themes/ananke/)
- [AWS Amplify Hosting](https://aws.amazon.com/amplify/hosting/)
- [AWS SES Documentation](https://docs.aws.amazon.com/ses/)

## Team

- **CESF**: Centre d'Études Spirites Fraternité
- **Location**: Montreal, Quebec, Canada
- **Website**: https://cesf.ca

## License

Content is proprietary to CESF. Theme (Ananke) is MIT licensed.