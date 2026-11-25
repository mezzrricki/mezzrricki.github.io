# Inkcore site (`/inkcore/`)

Static website for **Inkcore**, a pen-first music notation app for Android tablets: a home page, a privacy policy and a support page.
Plain HTML and one CSS file. No JavaScript, no trackers, no external fonts, scripts or CDNs. All links are relative, so the folder works
at `https://mezzrricki.github.io/inkcore/`, from another path, or straight from disk (`file://`).

## Contents

| File | What it is |
|---|---|
| `index.html` | Home page: what Inkcore is, status ("in testing on Android tablets"), links to privacy and support |
| `privacy.html` | Plain-English privacy policy, effective 2026-11-24, matching `docs/release/data-safety-audit.md` in the app repo |
| `support.html` | How to get help and how to report a bug |
| `style.css` | The only stylesheet (system fonts, light and dark through `prefers-color-scheme`) |
| `assets/images/` | All Inkcore images go here (see "Images") |

## Before publishing: fill in the placeholders

`privacy.html` and `support.html` contain two placeholders that the owner must replace. Do **not** publish with them in place:

- `[DEVELOPER NAME]` (privacy.html: "Who we are" and "Contact"; support.html: "Get help")
- `[CONTACT EMAIL]` (privacy.html: "Who we are", "Children", "Your choices", "Contact"; support.html: "Get help", "Privacy questions")

Find them with `grep -n "\[DEVELOPER NAME\]\|\[CONTACT EMAIL\]" inkcore/*.html`. The command prints nothing once they are all replaced.

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

Serve the **repository root** so the real address layout is reproduced:

```sh
cd ..   # the repository root
python3 -m http.server 8765 --bind 127.0.0.1
# then browse to http://127.0.0.1:8765/inkcore/ and stop it with Ctrl+C
```

## Publish (no domain needed)

Nothing has been pushed. The work is on the local branch `site/inkcore-homepage`.

1. Check the placeholders are gone (above).
2. Push the branch and open a pull request into `main`, or merge locally and push `main`:
   - `git push -u origin site/inkcore-homepage`, then merge the pull request on GitHub, or
   - `git checkout main && git merge --ff-only site/inkcore-homepage && git push origin main`.
3. GitHub: "It can take up to 10 minutes for changes to your site to publish after you push the changes to GitHub."
4. Visit `https://mezzrricki.github.io/inkcore/`, `/inkcore/privacy.html` and `/inkcore/support.html`.
   `https://mezzrricki.github.io/inkcore/privacy.html` is already a valid, public privacy-policy URL for Google Play.

No `.nojekyll` is needed (no underscore folders, no front matter); add an empty one only if a build ever fails or alters a page.

## `inkcore.app` redirects to `https://mezzrricki.github.io/inkcore/`

**Why a redirect and not a custom domain.** GitHub Pages can attach a custom domain only to a whole site, never to a folder. A `CNAME`
file in this repository would make `mezzrricki.github.io` (and every other project folder here) redirect to `inkcore.app`. The owner
wants this repository to keep hosting other apps, so there is **no `CNAME` file** here and `inkcore.app` simply forwards to the
`/inkcore/` folder. (The earlier draft's `CNAME` commit has been removed on this branch.)

**The catch: `.app` domains are HTTPS-only.** `.app` is on the browser HSTS preload list, so `http://inkcore.app` never loads, and the
forwarder must answer **over HTTPS with a valid certificate for `inkcore.app`**. Many registrars' basic "URL forwarding" serves HTTP
only, which would show a certificate or connection error. Check this before relying on it.

Ways to do it, best first:

1. **Cloudflare (free): redirect rule.** Move the domain's nameservers to Cloudflare, add a proxied DNS record for `@` (any placeholder
   address such as `192.0.2.1` with the proxy switched on), then add a Redirect Rule from `inkcore.app` (and `www.inkcore.app`) to
   `https://mezzrricki.github.io/inkcore/`, status 301. Cloudflare terminates HTTPS with its own certificate. (From memory; the exact
   menu names change. UNVERIFIED: check Cloudflare's "Redirect Rules" documentation.)
2. **Your registrar's forwarding, only if it offers HTTPS.** Set the forward to `https://mezzrricki.github.io/inkcore/`, enable
   "forward with SSL"/HTTPS if the option exists, and test `https://inkcore.app` from a phone on mobile data.
3. **A tiny forwarding site on GitHub Pages.** A second repository containing only a redirect page, with the `inkcore.app` custom domain
   on it. GitHub provides the certificate. This is a meta-refresh redirect, not a real 301, and each deep link
   (such as `inkcore.app/privacy.html`) needs its own tiny redirect file.

**What does not redirect.** The Play Console privacy URL and the link inside the app should use the **real address**,
`https://mezzrricki.github.io/inkcore/privacy.html`, so they never depend on the forwarder working.

**An alternative worth considering.** Put the Inkcore site in its **own repository** (for example `mezzrricki/inkcore`) and attach
`inkcore.app` to that repository. GitHub then serves `https://inkcore.app/` directly with its own certificate (no forwarder, real deep
links), and `https://mezzrricki.github.io/inkcore` redirects to it automatically (standard GitHub behaviour for project sites with a
custom domain; UNVERIFIED here). This repository stays free for other apps, each in its own repository. The records for that setup are
the apex `A`/`AAAA` records and the `www` CNAME from GitHub's documentation
(https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site),
but with the `www` CNAME pointing at `mezzrricki.github.io` (no repository name).

## Quick fix if you need a privacy URL today (Google Sites)

Create a Google Site at https://sites.google.com, paste the text of `privacy.html` into a page, click **Publish** and set the published site to **Public**, then use the published address in Play Console. Google's page says to click Publish, enter a web address, and choose "Restricted or Public" (https://support.google.com/sites/answer/6372880, fetched 2026-11-24); the exact address format was not shown, so use whatever Publish shows you. The policy must stay on a normal web page that works for everyone worldwide without login, not a PDF, and Google Sites meets that only with Public selected. Replace the placeholders first.
