<p align="right">
  <a href="./README.md">简体中文</a> · <a href="./README_EN.md">English</a>
</p>

<p align="center">
  <img src="./assets/banner.svg" alt="ObsiPastePic Banner" width="100%" />
</p>

<p align="center">
  <a href="https://github.com/zengyincen/obsidian-image-bed-uploader/releases/latest"><img alt="Latest release" src="https://img.shields.io/github/v/release/zengyincen/obsidian-image-bed-uploader?display_name=tag&sort=semver&style=flat-square&color=0A84FF" /></a>
  <a href="https://github.com/zengyincen/obsidian-image-bed-uploader/releases"><img alt="Downloads" src="https://img.shields.io/github/downloads/zengyincen/obsidian-image-bed-uploader/total?style=flat-square&color=5E5CE6" /></a>
  <a href="https://github.com/zengyincen/obsidian-image-bed-uploader/stargazers"><img alt="GitHub Stars" src="https://img.shields.io/github/stars/zengyincen/obsidian-image-bed-uploader?style=flat-square&color=FFB340" /></a>
  <a href="https://github.com/zengyincen/obsidian-image-bed-uploader/commits/main"><img alt="Last commit" src="https://img.shields.io/github/last-commit/zengyincen/obsidian-image-bed-uploader?style=flat-square&color=30D158" /></a>
  <a href="https://github.com/zengyincen/obsidian-image-bed-uploader/issues"><img alt="Issues" src="https://img.shields.io/github/issues/zengyincen/obsidian-image-bed-uploader?style=flat-square&color=FF9F0A" /></a>
  <a href="./LICENSE"><img alt="License" src="https://img.shields.io/github/license/zengyincen/obsidian-image-bed-uploader?style=flat-square&color=8E8E93" /></a>
</p>

<p align="center">
  <strong>Paste once. Upload automatically. Link instantly.</strong><br />
  ObsiPastePic uploads images pasted into Obsidian to GitHub or your image host, then inserts a customizable CDN or proxy URL.
</p>

<p align="center">
  <a href="https://github.com/zengyincen/obsidian-image-bed-uploader/releases/latest"><strong>Download</strong></a>
  ·
  <a href="#github-setup-in-30-seconds"><strong>Quick start</strong></a>
  ·
  <a href="#cdn--proxy-url-templates"><strong>CDN templates</strong></a>
</p>

<p align="center">
  <img src="./assets/hero.svg" alt="ObsiPastePic — Paste. Upload. Done." width="100%" />
</p>

## Why ObsiPastePic?

Screenshots, web images, and design exports can make an Obsidian vault grow quickly. Manually opening an image host, uploading, copying a URL, and returning to the note also breaks the writing flow.

ObsiPastePic does one thing well: **it catches a pasted or dropped image, uploads it, and puts the remote Markdown image link back in the same place.** Every upload gets a unique placeholder. Failures remain visible instead of silently removing content.

## Features

| Capability | Details |
| --- | --- |
| Automatic paste upload | Paste multiple images and replace each independent placeholder asynchronously |
| Drag-and-drop upload | Optionally upload images dropped into the Markdown editor |
| GitHub image hosting | GitHub Contents API with branches, date paths, and commit-message templates |
| Generic image-host API | `POST multipart/form-data` with custom headers, file field, and extra form fields |
| CDN / proxy acceleration | A full URL template instead of a limited list of CDN presets |
| Native fallback | Leaves Obsidian untouched when there is no image, mixed file types, or missing configuration |
| Desktop and mobile | No local executable dependency and no desktop-only manifest restriction |

## Installation

### Install from a Release

1. Open the [latest Release](https://github.com/zengyincen/obsidian-image-bed-uploader/releases/latest).
2. Download `image-bed-uploader-*.zip`.
3. Extract it to:

   ```text
   <your-vault>/.obsidian/plugins/image-bed-uploader/
   ```

4. Make sure the folder contains `main.js`, `manifest.json`, and `styles.css`.
5. Restart Obsidian and enable **ObsiPastePic** under Settings → Community plugins.

> ObsiPastePic has not yet been submitted to the official Obsidian community-plugin directory. GitHub Releases are currently the canonical distribution channel.

### Build from source

```bash
git clone https://github.com/zengyincen/obsidian-image-bed-uploader.git
cd obsidian-image-bed-uploader
npm install
npm test
npm run build
```

## GitHub setup in 30 seconds

1. Create or select a GitHub repository for your images. A public repository works best with public CDNs.
2. Create a **fine-grained personal access token**:
   - Limit repository access to the image repository;
   - Set Repository permissions → Contents to **Read and write**;
   - Grant no unrelated permissions.
3. Open ObsiPastePic settings and choose “GitHub repository.”
4. Enter the owner, repository, branch, and token.
5. Run “Test GitHub configuration,” then paste an image.

### Paths and filenames

The default repository path is:

```text
images/{year}/{month}
```

Available date variables: `{year}`, `{month}`, `{day}`, `{hour}`, `{minute}`, `{second}`, and `{timestamp}`.

Filename strategies:

- Timestamp + original filename — default and least likely to collide;
- Timestamp only;
- Original filename.

Commit messages can also use `{filename}` and `{path}`.

## CDN / proxy URL templates

GitHub mode supports `{owner}`, `{repo}`, `{branch}`, `{path}`, `{encodedPath}`, `{rawUrl}`, and `{encodedRawUrl}`.

| Use case | Template |
| --- | --- |
| GitHub Raw — default | `https://raw.githubusercontent.com/{owner}/{repo}/{branch}/{path}` |
| jsDelivr | `https://cdn.jsdelivr.net/gh/{owner}/{repo}@{branch}/{path}` |
| Statically | `https://cdn.statically.io/gh/{owner}/{repo}/{branch}/{path}` |
| Raw URL prefix proxy | `https://image-proxy.example.com/{rawUrl}` |
| Query-parameter proxy | `https://image-proxy.example.com/fetch?url={encodedRawUrl}` |

The settings screen shows a live preview, so the template can match any self-hosted proxy route.

> Raw and public CDN links generally cannot read private GitHub repositories anonymously. Uploads may succeed while images remain unavailable. Use an authenticated proxy or a public image repository.

## Custom image-host API

Custom mode sends images as `POST multipart/form-data`:

```http
POST /upload
Content-Type: multipart/form-data

file=<image bytes>
album=obsidian
```

| Setting | Example |
| --- | --- |
| Endpoint | `https://img.example.com/upload` |
| File field | `file`, `image`, or `source` |
| Header JSON | `{"Authorization":"Bearer token"}` |
| Extra field JSON | `{"album":"obsidian"}` |
| Response URL path | `data.url` or `data.images[0].url` |
| CDN template | `{url}` or `https://proxy.example.com/?url={encodedUrl}` |

Services that require custom signatures, chunked uploads, or an interactive browser login need a dedicated uploader implementation.

## Paste behavior and failures

- The plugin does nothing when the clipboard contains no image.
- It does not intercept a mixed list of image and non-image files.
- Missing configuration preserves Obsidian's native paste behavior.
- Uploads start with a unique placeholder and finish as `![filename](<remote-url>)`.
- Failed uploads become HTML comments and trigger a notice, making retry and diagnosis straightforward.
- A setting controls whether images are uploaded when clipboard text is present too.

## Security

- The GitHub token is stored as plain text in the plugin's `data.json`. Use a **single-repository, least-privilege, revocable** token.
- Never expose tokens or custom authorization headers in screenshots, Issues, logs, or public notes.
- GitHub and public CDNs have terms and traffic limits. Normal personal-note images are usually fine; they are not unlimited object storage.

## Development and verification

```bash
npm run typecheck   # TypeScript type checking
npm test            # Vitest unit tests
npm run build       # Build main.js
```

Tests currently cover template substitution, path encoding, file naming, JSON response paths, and settings migration.

## Brand assets

| Asset | File | Intended use |
| --- | --- | --- |
| App Icon | [`assets/icon.svg`](./assets/icon.svg) | Avatar, app icon, compact identity |
| Banner | [`assets/banner.svg`](./assets/banner.svg) | Repository header and social banner |
| Hero | [`assets/hero.svg`](./assets/hero.svg) | README and product overview |

The visual system uses restrained gradients, layered glass, and generous spacing. It contains no Apple trademark or official Apple artwork.

## Acknowledgements

ObsiPastePic is an independently implemented MIT-licensed project. The following projects provided valuable references for events, uploader abstractions, GitHub image hosting, and UX:

### Direct references

- [gavvvr/obsidian-imgur-plugin](https://github.com/gavvvr/obsidian-imgur-plugin) — image-paste events, uploader abstraction, and failure fallback.
- [yaleiyale/obsidian-emo-uploader](https://github.com/yaleiyale/obsidian-emo-uploader) — GitHub Contents API, multi-host settings, and CDN URL organization.
- [renmu123/obsidian-image-auto-upload-plugin](https://github.com/renmu123/obsidian-image-auto-upload-plugin) — paste placeholders, drag-and-drop uploads, and image-host workflows.

`obsidian-image-auto-upload-plugin` is a fork of `obsidian-imgur-plugin`; thanks to both maintainers for advancing this plugin family.

### Upstream projects and ecosystem credited by those references

- [jordanhandy/obsidian-cloudinary-uploader](https://github.com/jordanhandy/obsidian-cloudinary-uploader) — explicitly credited by Emo Uploader as a Cloudinary reference.
- [PicGo](https://github.com/Molunerfinn/PicGo) and [PicGo-Core](https://github.com/PicGo/PicGo-Core) — the image-host ecosystem and documentation used by Image Auto Upload Plugin.
- [GitHub REST API](https://docs.github.com/rest) and [jsDelivr](https://www.jsdelivr.com/) — upload and CDN infrastructure credited by Emo Uploader.
- [AList](https://github.com/AlistGo/alist), [EasyImages2.0](https://github.com/icret/EasyImages2.0), and [Chevereto](https://github.com/chevereto/chevereto) — self-hosted image ecosystems supported and credited by Emo Uploader.
- [Cloudinary](https://cloudinary.com/), [SM.MS](https://smms.app/), [ImgURL](https://www.imgurl.org/), [Imgur](https://imgur.com/), [imgbb](https://imgbb.com/), and [Catbox](https://catbox.moe/) — hosting services documented by the reference projects.
- Thanks also to [anxinJ](https://github.com/anxinJ) and [Linnnkkk](https://github.com/Linnnkkk), contributors explicitly named by Emo Uploader.

Thank you to [Obsidian](https://obsidian.md/) for the plugin API, and to every maintainer who publishes code and documentation openly.

## License

[MIT](./LICENSE) © 2026 曾胤岑
