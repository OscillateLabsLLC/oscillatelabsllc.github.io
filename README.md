# oscillatelabsllc.github.io

[![Status: Active](https://img.shields.io/badge/status-active-brightgreen)](https://github.com/OscillateLabsLLC/.github/blob/main/SUPPORT_STATUS.md)

Source for the [Oscillate Labs website](https://oscillatelabs.net). Built with [Hugo](https://gohugo.io/) and the [Congo](https://jpanther.github.io/congo/) theme (installed as a Hugo Module).

## Deployment

The site deploys to **GitHub Pages** via `.github/workflows/hugo.yml` on push to `main`. The custom domain (`oscillatelabs.net`) is bound via `static/CNAME`.

## Local development

The Hugo version is pinned to **0.161.1** (see `.hugo-version` and `HUGO_VERSION` in the workflow), which matches what the current Congo release targets. The theme breaks on mismatched versions, so install this exact version rather than `brew install hugo` (which tracks latest):

```sh
# Extended build required for Dart Sass
CGO_ENABLED=1 go install -tags extended github.com/gohugoio/hugo@v0.161.1

# Serve locally
hugo server
```

Congo is pinned in `go.mod`. To upgrade the theme later, bump both Congo (`hugo mod get github.com/jpanther/congo/v2@vX.Y.Z`) and `HUGO_VERSION` together to the version pair the theme's `netlify.toml` specifies.

## Content

- `content/_index.md` — homepage
- `content/posts/` — blog
- `content/booktrove/` — BookTrove support & privacy pages (referenced by app store listings)
