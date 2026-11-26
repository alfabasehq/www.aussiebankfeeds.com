# AussieBankFeeds website

The public pages of [AussieBankFeeds](https://www.aussiebankfeeds.com), automatic bank feeds for
Australian businesses using Manager.io accounting software. A static site built with
[Eleventy](https://www.11ty.dev/) and deployed to GitHub Pages.

## Commands

```
npm install      # once
npm start        # local preview at http://localhost:8080, rebuilds on change
npm run build    # writes the site to _site/
```

Pushing to `main` builds and deploys the site (`.github/workflows/pages.yml`).

## Where things are

| Path | What it holds |
|---|---|
| `src/index.njk` | Home page: hero, how it works, features and FAQ |
| `src/consent.njk` | CDR consent information ("How we handle your data") |
| `src/404.njk` | Not-found page, which also forwards the addresses that moved to the app (see below) |
| `src/_includes/layout.njk` | Page shell: head, header and footer |
| `src/_data/site.json` | Address of this site (`url`) and of the app (`app`), and the support email |
| `public/` | Files copied to the site as they are: stylesheet, icons, `robots.txt`, `ads.txt` |
| `eleventy.config.js` | Build configuration |

## The app

Everything that needs a signed-in user — accounts, bank connections, syncing into Manager,
subscriptions — is the `app.aussiebankfeeds.com` project, an ASP.NET app with the API, at
<https://app.aussiebankfeeds.com>. That address is `app` in `src/_data/site.json`, and every link to it
is built from that one value.

- This repository used to hold that app, served from this address; its history has the code. The home
  page here is the old app's home page, and `public/styles.css` uses the app's colours, so the two look
  alike.
- Nothing here calls the app's API. The two only link to each other.
- `/consent/` has the same text as the app's own `/consent` page (`wwwroot/js/pages/consent.js` there).
  It is a real page here, not a forward, because that address may be the one given to Basiq as the
  consent knowledge base. Change both together.
- `/profile`, `/connections`, `/connect`, `/accounts`, `/sync` and `/install` were served from this
  address before. They do not exist here, so GitHub Pages serves `404.html`, which sends them on to the
  app with the path, query and hash kept.
- `/sync` matters most: Manager businesses that installed the extension before the move have
  `https://aussiebankfeeds.com/sync` saved as its endpoint, so Manager still opens that address in its
  extension frame. It reaches the app only if the bare domain `aussiebankfeeds.com` also ends up at this
  site (see Custom domain). Nothing is installed any more: the app is opened from Manager's Extensions
  screen, and the app's own `/install` just redirects to `/sync`.
- That forwarding is a script in a page, so it works for people and not for programs. Nothing answers
  at `/api/...` here.

## Custom domain

The site is deployed by a GitHub Actions workflow, so the domain is set in the repository's
**Settings → Pages → Custom domain** (`www.aussiebankfeeds.com`), not by a `CNAME` file. DNS needs a
`CNAME` record for `www` pointing at `alfabasehq.github.io`, and the bare domain `aussiebankfeeds.com`
pointing at GitHub Pages' `A` records, which makes GitHub redirect it to `www`. Until that is done the
site is served from `alfabasehq.github.io/www.aussiebankfeeds.com/`, which the build allows for (see
`--pathprefix` in the workflow).
