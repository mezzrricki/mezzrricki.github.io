# mezzrricki.github.io

This repository is a GitHub Pages **user site** (published from branch `main`, folder `/`, legacy build). It now holds the small
static website for **Inkcore**, a pen-first music notation app for Android tablets: a home page, a privacy policy and a support
page. Plain HTML and one CSS file. No JavaScript, no trackers, no external fonts, scripts or CDNs. It also works when you open
`index.html` straight from disk (`file://`).

(The original README in this repository was only the one-line title above. That title is kept as the heading of this file.)

## Contents

| File | What it is |
|---|---|
| `index.html` | Home page: what Inkcore is, status ("in testing on Android tablets"), links to privacy and support |
| `privacy.html` | Plain-English privacy policy, effective 2026-11-24, matching `docs/release/data-safety-audit.md` in the app repo |
| `support.html` | How to get help and how to report a bug |
| `style.css` | The only stylesheet (system fonts, light and dark through `prefers-color-scheme`) |
| `assets/images/` | All images go here (empty for now, see "Images") |
| `CNAME` | **Not on this branch's first commits.** One line, `inkcore.app`. Added in its own last commit so it can be held back until DNS is ready (see "Custom domain") |

## Before publishing: fill in the placeholders

`privacy.html` and `support.html` contain two placeholders that the owner must replace. Do **not** publish with them in place:

- `[DEVELOPER NAME]` (privacy.html: "Who we are" and "Contact"; support.html: "Get help")
- `[CONTACT EMAIL]` (privacy.html: "Who we are", "Children", "Your choices", "Contact"; support.html: "Get help", "Privacy questions")

Find them with `grep -n "\[DEVELOPER NAME\]\|\[CONTACT EMAIL\]" *.html`. The command prints nothing once they are all replaced.

Also read these decisions in the privacy text, which are the owner's to make: the retention sentence ("keep your message ... only as long as
needed ... delete on request"), the promise about how changes are announced (Google Play release notes and, where practical, inside the
app), and the "Purchases" section, which describes the one-time unlock that exists only on the app's billing branch. Publish that section
only together with the unlock, or delete it (and the matching rows and FAQ entry on `support.html`).

Google Play also wants the policy linked from inside the app, and there is no such link in the app yet.

## Images (add later; the pages work without them)

Nothing in the pages needs an image now. Three clearly marked slots are written as HTML comments (so there is no broken-image icon).
To switch one on, add the file and delete the comment marks around that tag. Each tag already has `alt` text and `width`/`height`.

| Slot | File to add | Size | Where |
|---|---|---|---|
| App icon | `assets/images/app-icon-512.png` | 512 x 512 px PNG (shown at 128 x 128) | `index.html`, top of the page |
| Feature graphic | `assets/images/feature-graphic-1024x500.png` | 1024 x 500 px PNG | `index.html`, under the introduction |
| Favicon (reuses the icon) | `assets/images/app-icon-512.png` | same file | `<link rel="icon">` in `index.html` `<head>` |

The folder is kept in git with an empty `assets/images/.gitkeep`. The site contains no screenshots.

## Preview locally

Open `index.html` in a browser, or serve the folder:

```sh
python3 -m http.server 8765 --bind 127.0.0.1
# then browse to http://127.0.0.1:8765/ and stop it with Ctrl+C
```

## Publish on mezzrricki.github.io (no domain needed)

Nothing has been pushed. The work is on the local branch `site/inkcore-homepage`.

1. Check the placeholders are gone (above).
2. Push the branch and open a pull request into `main`, or merge locally and push `main`:
   - `git push -u origin site/inkcore-homepage`, open the pull request on GitHub and merge it **without the `CNAME` commit** (see below), or
   - `git checkout main && git merge --ff-only site/inkcore-homepage~1 && git push origin main` (`~1` leaves out the last commit, which is the `CNAME` commit).
3. GitHub: "It can take up to 10 minutes for changes to your site to publish after you push the changes to GitHub."
4. Visit `https://mezzrricki.github.io/`, `/privacy.html` and `/support.html`. That `https://mezzrricki.github.io/privacy.html` address is already a valid, public privacy-policy URL for Google Play.

GitHub's own page says that, for a site published from a branch, "GitHub Pages will use Jekyll to build your site by default", and that a
`.nojekyll` file turns that off. This site has no underscore folders, no front matter and no Markdown pages to convert, so Jekyll just
copies the files and **no `.nojekyll` is needed**; add an empty `.nojekyll` only if a build ever fails or starts altering a page.
`README.md` is served as a plain file too.

## Custom domain: inkcore.app

### Why the CNAME file is its own, separate commit

When a repository named `<user>.github.io` has a `CNAME` file, GitHub Pages treats `inkcore.app` as the site's address and
`mezzrricki.github.io` then redirects to it. (The redirect is the usual GitHub behaviour, and the owner brief says the same. The GitHub
pages I fetched do not state it in so many words, so treat that detail as UNVERIFIED.) If the DNS records below are not in place yet, **the
redirect lands on an address that does not work, and the whole site is down**, including the privacy-policy URL you gave Google Play.
So the `CNAME` file is the last commit on the branch, and should be added to `main` only after DNS answers correctly. GitHub also says
that "if you set a custom domain for a user site or organization site, that same custom domain will be used for all project sites owned by
the same account", so any other Pages project site under `mezzrricki` would move to `inkcore.app/<repo>`.

### DNS records (from GitHub's current official documentation)

Source: https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site
fetched 2026-11-24. Add these at the registrar that manages `inkcore.app`.

| Host | Type | Value |
|---|---|---|
| `@` (inkcore.app) | A | `185.199.108.153` |
| `@` | A | `185.199.109.153` |
| `@` | A | `185.199.110.153` |
| `@` | A | `185.199.111.153` |
| `@` | AAAA | `2606:50c0:8000::153` |
| `@` | AAAA | `2606:50c0:8001::153` |
| `@` | AAAA | `2606:50c0:8002::153` |
| `@` | AAAA | `2606:50c0:8003::153` |
| `www` | CNAME | `mezzrricki.github.io` |

GitHub: "we highly recommend using an `A` record in addition to your `AAAA` record"; the `CNAME` must point "directly to `<user>.github.io`" (no
repository name in it); "We strongly recommend that you do not use wildcard DNS records, such as `*.example.com`"; "DNS changes can
take up to 24 hours to propagate." Remove any old parking or placeholder `A`, `AAAA` or `CNAME` records on `@` and `www` first; conflicting
records can stop the HTTPS certificate being issued (GitHub's HTTPS page).
When both the apex and `www` are set up, GitHub redirects between them ("if `example.com` is the custom domain, `www.example.com` will redirect to
`example.com`"; https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/about-custom-domains-and-github-pages, fetched 2026-11-24).

**Recommended, optional:** verify the domain so nobody else can claim it on GitHub. Profile picture, Settings, Pages, "Add a domain", then add the
TXT record GitHub shows. The record name is `_github-pages-challenge-mezzrricki.inkcore.app`; the value appears on that GitHub page. "this may be
immediate or take up to 24 hours" and the record should stay in DNS afterwards. Source: https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/verifying-your-custom-domain-for-github-pages (fetched 2026-11-24).

### Exact steps, in order

1. Replace the placeholders, push, and confirm the site works at `https://mezzrricki.github.io/` (above).
2. (Optional but recommended) verify `inkcore.app` in your GitHub profile Pages settings and add the TXT record.
3. At the registrar, add the A, AAAA and `www` CNAME records from the table.
4. Check that DNS answers (this can take up to 24 hours):
   ```sh
   dig +short inkcore.app A
   dig +short inkcore.app AAAA
   dig +short www.inkcore.app CNAME
   ```
   The A lines should be the four `185.199.108-111.153` addresses and the CNAME should be `mezzrricki.github.io.`.
5. Only now add the `CNAME` commit to `main`: either `git cherry-pick <the CNAME commit>` on `main` and push, or in the repository on GitHub go to Settings, Pages, "Custom domain", type `inkcore.app` and Save (GitHub adds the `CNAME` file for you).
6. Wait for the certificate. GitHub gets a Let's Encrypt certificate "once a DNS check passes"; "The process may take some time", and "Enforce HTTPS" "can take up to 24 hours before this option is available". If it has not finished after several minutes you can remove and re-add the domain to restart it. Then make sure "Enforce HTTPS" is ticked (it is already on for the `github.io` address).
7. Check `https://inkcore.app/`, `https://inkcore.app/privacy.html`, and `https://www.inkcore.app/`. Then put `https://inkcore.app/privacy.html` into Play Console and the app.

If the site breaks after step 5, delete the `CNAME` file (or clear "Custom domain" in Settings, Pages) and the `github.io` address works again.

### `.app` domains need HTTPS

`.app` is managed by Google Registry and is HSTS-preloaded: "browsers will only load .app sites over a secure HTTPS connection" (https://www.registry.google/domains/app/, fetched 2026-11-24). `http://inkcore.app` will not load in modern browsers, so the site must always be served over HTTPS. GitHub Pages, Cloudflare Pages and Netlify all do that.

## Alternatives to GitHub Pages (all free tiers; all serve over HTTPS)

**Cloudflare Pages.** Source: https://developers.cloudflare.com/pages/framework-guides/deploy-anything/ and https://developers.cloudflare.com/pages/configuration/custom-domains/ (fetched 2026-11-24). Push the files to a Git repository, then in the Cloudflare dashboard open Workers & Pages, Create application, Pages, Import an existing Git repository. Production branch `main`, build command `exit 0` ("If you are not using a preset and do not need to build your site, use `exit 0`"), build output directory: the folder holding `index.html` (the repository root, `/`). You get a `*.pages.dev` address. For an apex domain, Cloudflare requires the domain to be a zone on your Cloudflare account, which means pointing the registrar's nameservers at Cloudflare; Cloudflare then creates the record for you. A `www` subdomain needs a CNAME to `<project>.pages.dev`. (Direct upload without Git also exists; not checked here, UNVERIFIED.)

**Netlify.** Source: https://docs.netlify.com/domains-https/custom-domains/configure-external-dns/ and https://docs.netlify.com/site-deploys/create-deploys/ (fetched 2026-11-24). Drag this folder onto Netlify Drop (https://app.netlify.com/drop) while logged in. For the custom domain with external DNS: apex `A` record to `75.2.60.5` (or an ALIAS to `apex-loadbalancer.netlify.com` if your DNS provider supports ALIAS), and `www` CNAME to `<site-name>.netlify.app`. Netlify's page lists "Instant SSL certificate provisioning"; "SSL certificates may require additional verification steps" with external DNS.

## Quick fix if you need a privacy URL today (Google Sites)

Create a Google Site at https://sites.google.com, paste the text of `privacy.html` into a page, click **Publish** and set the published site to **Public**, then use the published address in Play Console. Google's page says to click Publish, enter a web address, and choose "Restricted or Public" (https://support.google.com/sites/answer/6372880, fetched 2026-11-24); the exact address format was not shown, so use whatever Publish shows you. The policy must stay on a normal web page that works for everyone worldwide without login, not a PDF, and Google Sites meets that only with Public selected. Replace the placeholders first.
