# puzzlelantern.app

The published build of the Puzzle Lantern site, served by GitHub Pages.

**Nothing here is written by hand.** The source lives in the app repository
under `landing/`, and every file except the ones listed below is overwritten
on each deploy. Edit the source, then run the deploy from there:

```sh
pnpm --dir landing pages
```

That builds the static export, replaces the contents of this repository with
it, commits and pushes. See `landing/README.md` in the app repository for what
the site is and how it is built.

## Files this repository owns

The deploy preserves these four; everything else is generated.

| File         | Why it is here                                                                                                                  |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------- |
| `CNAME`      | The custom domain. GitHub Pages reads it on every build and drops the domain if it disappears.                                   |
| `.nojekyll`  | Stops the Jekyll pass. Without it Pages refuses to serve `_next/`, because Jekyll ignores paths beginning with an underscore.    |
| `.gitignore` | Keeps `.DS_Store` out.                                                                                                          |
| `README.md`  | This file.                                                                                                                      |

## Pages settings

**Settings → Pages → Build and deployment → Deploy from a branch**, branch
`main`, folder `/ (root)`. Custom domain `puzzlelantern.app`, with **Enforce
HTTPS** on once the certificate is issued.

DNS for the apex domain has to point at GitHub, four `A` records and four
`AAAA` records:

```
A     185.199.108.153   AAAA  2606:50c0:8000::153
A     185.199.109.153   AAAA  2606:50c0:8001::153
A     185.199.110.153   AAAA  2606:50c0:8002::153
A     185.199.111.153   AAAA  2606:50c0:8003::153
```

`www.puzzlelantern.app` should be a `CNAME` to `tashukbiz.github.io.`.

## What GitHub Pages cannot do

Two deploy requirements in `landing/README.md` need a host that can set
headers or rewrite paths. Pages can do neither, so:

- **`/i/<token>` invite links.** Pages has no rewrite rule, so a path form
  falls through to `404.html`. The site's 404 page recognises an invite path
  and hands off to `/i/?t=<token>`, which renders the invite correctly, but
  the first response still carries a `404` status. The `?t=` form is served
  directly and is unaffected. This does not touch iOS universal links: the
  system matches the URL against the association file before any page is
  fetched, so an installed app never sees the 404.
- **`/.well-known/apple-app-site-association`.** Apple wants
  `Content-Type: application/json`, and Pages types an extensionless file by
  guesswork. Verify it after the domain goes live:

  ```sh
  curl -sSI https://puzzlelantern.app/.well-known/apple-app-site-association
  ```

  If the type is wrong, put a CDN that can set headers in front of the domain,
  or move the site to a host that can.
