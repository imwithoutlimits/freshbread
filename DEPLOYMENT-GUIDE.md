> **Current setup:** the site is deployed on Netlify and served at **https://apps.lehemnetwork.org** (custom domain), with the source in GitHub. Pushing to the production branch redeploys it. Because the site is now on Netlify itself, the contact form posts to the same domain and Netlify Forms collects submissions under **Site > Forms**. Make sure **Enable form detection** is on, then redeploy once so the form is registered.

# Deployment Guide — FreshBread Publishing Website

This walks through the full path: zip file → live on GitHub Pages → custom domain → keeping Netlify (the contact form backend) in sync.

---

## Part 1 — From zip to a GitHub repo

**1. Download and unzip**
Download `freshbread-publishing-site.zip` and extract it. You should get a folder containing `index.html`, `assets/`, `plugins/`, and the rest of the pages directly inside it (not nested another level deeper).

**2a. If this is your first deploy — create a new repo**
- Go to github.com → click **+** (top right) → **New repository**.
- Name it (e.g. `freshbread-site`), keep it **Public**, don't add a README (you already have one), click **Create repository**.
- On the empty repo's page, click **uploading an existing file**.
- Drag in everything *inside* your unzipped folder (not the folder itself) — `index.html`, `about.html`, `assets/`, `plugins/`, `README.md`, all of it.
- Scroll down, add a commit message like "Initial site upload", click **Commit changes**.

**2b. If you already have the repo — update it**
- Open your existing repo on github.com.
- Click **Add file → Upload files**.
- Drag in the full unzipped folder contents again. GitHub will show which files changed.
- Commit the changes — this replaces the old versions in place. Nothing is deleted unless you specifically remove a file from the upload.

## Part 2 — Turn on GitHub Pages

1. In the repo, go to **Settings → Pages** (left sidebar).
2. Under "Build and deployment," set **Source** to **Deploy from a branch**.
3. Set **Branch** to `main`, folder to `/ (root)`, then click **Save**.
4. Wait 1–2 minutes, then refresh the page. A green banner shows your live URL — something like `https://yourusername.github.io/freshbread-site/`.

## Part 3 — View it live

Open that URL. Click through Home, Products, every plugin page, About, Contact, and the legal pages. Submit a real test message on the Contact page and confirm you land on your own `thank-you.html` (not a `netlify.app` address).

## Part 4 — Add a custom domain

You'll need a domain you own (e.g. from Namecheap, Google Domains, GoDaddy).

**If using the root domain (freshbreadpublishing.com):**
1. In your domain registrar's DNS settings, add four **A records** for the root domain, all pointing to GitHub Pages' IP addresses:
   ```
   185.199.108.153
   185.199.109.153
   185.199.110.153
   185.199.111.153
   ```
2. Add a **CNAME record** for `www` pointing to `yourusername.github.io`.
3. Back in GitHub: **Settings → Pages → Custom domain**, type in `freshbreadpublishing.com`, click **Save**. GitHub creates a `CNAME` file in your repo automatically.
4. Check **Enforce HTTPS** once it becomes available (can take up to 24 hours for the SSL certificate to provision).

**If using just a subdomain (e.g. www.freshbreadpublishing.com):**
1. Add a single **CNAME record**: `www` → `yourusername.github.io`.
2. Same step in GitHub Pages settings, entering `www.freshbreadpublishing.com`.

DNS changes can take anywhere from a few minutes to 24–48 hours to fully propagate. If the custom domain doesn't resolve immediately, that's normal — wait and try again.

## Part 5 — Keep Netlify in sync

Netlify exists here purely to receive Contact form submissions (see the README's "Contact form" section for why). Every time you update the site content, redeploy to Netlify too, so its copy of the form stays in sync with what visitors are actually submitting from your live domain.

1. Go to **app.netlify.com/drop**.
2. Drag in the same unzipped folder you just uploaded to GitHub.
3. This redeploys to your existing Netlify site (same URL as before, e.g. `freshbreadpublishing.netlify.app`) — it does **not** create a new site as long as you're logged into the same account and it recognizes the existing deploy.
4. Check **Site → Forms** in the Netlify dashboard afterward to confirm your test submission from Part 3 shows up there.

If you ever change the contact form's fields (add a phone number field, for example), that change has to go to Netlify too, or Netlify won't know about the new field and will silently drop it from submissions.

## Quick reference — what lives where

| Task | Where |
|---|---|
| Public-facing live site | GitHub Pages |
| Contact form backend / submissions inbox | Netlify (Site → Forms) |
| DNS / custom domain | Your domain registrar + GitHub Pages settings |
| Editing site content | Local files → re-upload to **both** GitHub and Netlify |
