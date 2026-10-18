# OmniScope

[中文](README.md) · [Platform matrix](docs/PLATFORMS.md) · [Architecture](docs/ARCHITECTURE.md) · [Changelog](CHANGELOG.md)

**A local-first desktop search app for videos, posts, articles, and web pages.** Search once across multiple public sources, match video titles exactly, filter by source, save favorites, and export CSV / JSON / Markdown.

![OmniScope search](docs/screenshots/search-en.png)

![OmniScope platform directory](docs/screenshots/platforms-en.png)

![OmniScope setup guide](docs/screenshots/setup-en.png)

## Highlights

- **60-platform directory**: 16 native search connectors (5 optional and network-dependent) and 44 external platform launchers. Native connectors return structured results inside the app; external entries open the platform's own search page with the current query.
- **Two-page welcome guide**: features on page one, concrete setup steps and API requirements on page two. Check “Don’t show again” at the bottom-left, or re-enable it in Settings.
- **Setup badges**: YouTube is marked “API key required”, Bilibili “Cookie optional”, other native sources “No setup”, and external platforms “Platform login”.
- **Optional platform index**: configure Serper / Brave / Google CSE / SearXNG to add the 44 external platforms to in-app search. Without it, “Open search” remains fully available.
- **Bilingual UI**: English and Chinese, following the system language or selectable in Settings.
- **Locale-aware defaults**: Chinese systems default to Chinese sources; English/international systems default to global sources. You can change language and region independently.
- **Title-focused search**: Bilibili, Sogou, Bing, 360, Toutiao, CSDN, Juejin, GitHub, Hacker News, arXiv, Stack Overflow, Wikipedia, Lemmy, and optional YouTube Data API.
- **Source-level isolation**: every connector has its own timeout and failure status; one blocked or rate-limited source does not break the search.
- **Respectful networking**: per-source minimum intervals, Sogou mobile-first fallback, and verification cooldowns. No attempts to bypass login, CAPTCHA, paywalls, or access controls.
- **Local by design**: no account, no cloud backend, no telemetry. Credentials are encrypted with the OS secure storage where available.
- **Portable Windows build**: unpack and run; Node.js is not required.

## Setup requirements

| Item | Required? | How |
| --- | --- | --- |
| YouTube Data API v3 key | Required only for the YouTube source | Enable the API in Google Cloud Console, create a key, paste it in Settings → Credentials |
| Bilibili Cookie | Optional | Settings → Credentials; improves stability under risk control, not required for search |
| GitHub / Hacker News / arXiv / Stack Overflow / Wikipedia / CSDN / Juejin / Lemmy | No setup | Public APIs; GitHub has an unauthenticated rate limit |
| Reddit / Bluesky / Mastodon / Internet Archive / Open Library | Optional | Native adapters, disabled by default; some networks may block them |
| 44 external platforms | No app API key | “Open search” opens the platform site; login is handled there |
| Platform index API (optional) | Optional | Serper / Brave / Google CSE / SearXNG can add the 44 external platforms to in-app index results |

## Native connectors

| Source | Type | Notes |
| --- | --- | --- |
| Bilibili | Video | Public search API; visitor cookie; title/creator/views/danmaku |
| Bing | Web | Chinese/global web index |
| Sogou | Web | Mobile-first, desktop fallback, rate limiting and cooldown |
| 360 Search | Web | Chinese web index supplement |
| Toutiao | News | News, articles, and video pages |
| CSDN | Articles | Chinese developer blogs |
| Juejin | Articles | Chinese developer community |
| GitHub | Repositories | Stars, language, update time; unauthenticated rate limit applies |
| Hacker News | Posts | Public Algolia API |
| arXiv | Papers | Authors, abstracts, publication dates |
| Stack Overflow | Q&A | Votes, answers, tags |
| Wikipedia | Articles | English/Chinese Wikipedia selected by UI language |
| Lemmy | Posts | lemmy.world public API |
| Reddit | Posts | Public search JSON; optional, may return 403 on some networks |
| Bluesky | Posts | Public search API; optional |
| Mastodon | Posts | mastodon.social public search; optional |
| Internet Archive | Items | Books, audio, video, and web archives; optional |
| Open Library | Books | Open book catalog; optional |
| YouTube | Video | Optional; requires your own YouTube Data API v3 key |

The other 44 platforms in the 60-entry directory are **external launchers** by default: they open the official search page with the current query. They do not inject fake results into the aggregator. If you configure a universal search API, they can be added to in-app index search. See [docs/PLATFORMS.md](docs/PLATFORMS.md) for the full matrix.

## Add external platforms to in-app search (optional)

1. Open **Settings → Platform index**.
2. Choose a provider:
   - **Serper.dev** (Google index): API key.
   - **Brave Search API**: API key.
   - **Google Custom Search JSON API**: API key + CX.
   - **SearXNG**: URL of your instance with JSON output enabled.
3. Save.
4. Open **Platforms**; external cards change from “Set up API” to “Add to search”. Click it and run the search again.

Notes:

- Results are search-index snippets, not the platform's native ranking, comments, or login-only content.
- Each query consumes provider credits per selected platform. Enable only what you need.
- Without a key, every external platform still works through “Open search”.

## Quick start

1. Download and extract the complete `OmniScope-win32-x64.zip` folder.
2. Run `OmniScope.exe`.
3. Enter a query and press `Enter`.

`OmniScope.exe` is a smart launcher; `OmniScopeCore.exe` is the Electron application. The launcher keeps the Chromium sandbox when possible and falls back only when the current Windows session blocks the sandbox named pipe.

## Development

Requirements: Windows, Node.js 20+, npm.

```bash
npm install
npm test
npm start
npm run pack
```

`npm run pack` produces `dist/OmniScope-win32-x64/` and the packaged launcher. The release zip is created from that folder.

## Project layout

```text
main.js                    Electron main process, windows, IPC, export
preload.js                 contextBridge allow-list
src/main/http.js           fetch, encoding, cache, normalized errors
src/main/search.js         source orchestration, dedupe, scoring, sorting
src/main/platforms.js      60-platform directory and locale metadata
src/main/platform-index.js optional Serper/Brave/Google/SearXNG adapter
src/main/store.js          settings, history, favorites, encrypted secrets
src/main/sources/          19 native source adapters
src/renderer/              vanilla HTML/CSS/JS UI
src/renderer/i18n.js       English UI dictionary
scripts/                   probes, tests, packaging, launcher source
docs/                      architecture, platform matrix, screenshots
```

## Privacy and limitations

- Queries are sent directly from your machine to public sources; results and settings stay local.
- Some platforms require login, signatures, CAPTCHAs, or regional access. OmniScope does not bypass those controls; such platforms are marked **external** and open in your browser.
- Search indexes are incomplete by nature. "Whole web" is product language, not a literal crawl of the entire internet.
- Search engines may rate-limit by IP. Use the proxy setting, reduce results per source, or wait for the built-in cooldown.
- Follow each platform's terms of service and robots rules. Do not use this project for high-frequency scraping or redistributing infringing content.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). New native adapters are welcome; please add fixtures or a documented public endpoint and keep failures isolated.

## Security

See [SECURITY.md](SECURITY.md). Please report vulnerabilities privately through GitHub Security Advisories.

## License

MIT. Third-party content remains the property of its respective platforms and authors.
