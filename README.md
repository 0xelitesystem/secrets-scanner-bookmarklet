# Secrets Scanner Bookmarklet

A bookmarklet that scans the current web page for exposed API keys, tokens, and credentials. Drag to your bookmarks bar, click on any page, see findings.

**Live demo:** https://0xelitesystem.github.io/secrets-scanner-bookmarklet/

## Use

1. Open [`index.html`](./index.html) in your browser (or visit the GitHub Pages link)
2. Drag the red "Scan for secrets" button to your bookmarks bar
3. Visit any page (your own staging site, a public docs page, anywhere)
4. Click the bookmark
5. An alert box shows pattern matches

## What it scans

The rendered HTML of the current page. This includes:

- Inline scripts and JSON
- Visible text
- Comments in HTML
- Data attributes
- Anything `document.documentElement.outerHTML` returns

It does NOT scan:

- External JS files (only inline)
- Source maps unless inlined
- Service worker storage
- IndexedDB or localStorage values not rendered
- WebAssembly modules

## Patterns matched

| Pattern | Service |
|---|---|
| `sk-ant-*` | Anthropic |
| `sk-*` | OpenAI / generic |
| `ghp_*`, `gho_*`, `ghu_*`, `ghs_*`, `ghr_*` | GitHub PATs |
| `github_pat_*` | GitHub fine-grained PAT |
| `xoxb-`, `xoxp-`, `xoxa-`, `xoxr-`, `xoxs-` | Slack |
| `AKIA*`, `ASIA*` | AWS access key IDs |
| `AIza*` | Google API keys |
| `eyJ*.eyJ*.*` | JWTs |
| `SG.*.*` | SendGrid |
| `key-*` | Mailgun |
| `sk_live_*`, `sk_test_*`, `pk_live_*`, `pk_test_*` | Stripe |
| `Authorization: Bearer ...` | Bearer tokens in headers |
| `-----BEGIN ... PRIVATE KEY-----` | PEM private keys |

False positives are possible. Always inspect findings.

## When this is useful

- **Pre-deploy audit** of your own pages (catch a key you forgot to remove from a debug log)
- **Inspecting third-party widgets or embeds** before pasting them on your site
- **Reviewing copy-pasted error messages or stack traces** that may contain inline credentials
- **Quick check on a docs site** that includes inline examples

## When this is NOT enough

- Don't rely on this for security audits. Use [trufflehog](https://github.com/trufflesecurity/trufflehog), [gitleaks](https://github.com/gitleaks/gitleaks), or commercial scanners for thoroughness.
- This won't catch secrets in private code that isn't rendered.
- This won't catch obfuscated or encoded secrets.

## Privacy

The bookmarklet runs entirely in your browser. No data is sent anywhere. The findings appear in a native `alert()` box. You can verify by reading the source, the entire script is shown on the demo page.

## How to add it to your bookmarks bar

**Most browsers:**
- Drag the red button from the demo page to your bookmarks bar

**If drag-to-bookmark doesn't work:**
- Right-click the bookmarks bar → Add bookmark
- Name it "Scan for secrets"
- URL: paste the `javascript:...` text from the demo page

**On mobile:**
- Bookmarklets are awkward on mobile. Use the in-browser test panel on the demo page instead.

## Build and run locally

```bash
git clone https://github.com/0xelitesystem/secrets-scanner-bookmarklet
cd secrets-scanner-bookmarklet
# Open index.html, or:
python -m http.server 8000
```

There's no build step. The patterns and scanner are all inline in the single HTML file.

## Contribute

PRs welcome:

- New credential pattern (with the regex tested against real-world examples)
- Reduce false positives on existing patterns
- Better UI for the findings alert (without breaking the bookmarklet's no-injection model)

Don't add: telemetry, external dependencies, package.json. Single file by design.

## License

MIT.

## Related

- [jwt-inspector](https://github.com/0xelitesystem/jwt-inspector), decode JWTs found by this scanner
- [byok-security-checklist](https://github.com/0xelitesystem/byok-security-checklist), security checklist for BYOK products
- [csp-builder](https://github.com/0xelitesystem/csp-builder), Content Security Policy builder
