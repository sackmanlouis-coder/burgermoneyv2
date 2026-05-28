# Agent-compatibility upgrade — what changed

Your `index.html` and the site are now machine-readable by AI agents on Base (and the broader agent web). Visible design and behavior are unchanged.

## Files in this bundle

| File | Where to deploy | Purpose |
| --- | --- | --- |
| `index.html` | site root | Now carries full agent metadata: OG, Twitter, Farcaster Mini App / Frame embed, JSON-LD graph (WebSite + Organization + Product/FinancialProduct + FAQPage), CAIP-19, AI-crawler permissions, inline `token-data` JSON, `data-*` attributes on the contract pill, stat strip, and every action link |
| `llms.txt` | site root → `/llms.txt` | Clean Markdown summary for LLM crawlers (Jeremy Howard convention; read by AI coding tools and increasingly by AI search) |
| `llms-full.txt` | site root → `/llms-full.txt` | Full single-document export for deep LLM ingest |
| `robots.txt` | site root → `/robots.txt` | Explicitly allows GPTBot, ClaudeBot, anthropic-ai, PerplexityBot, OAI-SearchBot, Google-Extended, Applebot-Extended, Bingbot, etc. |
| `sitemap.xml` | site root → `/sitemap.xml` | Lists the page and all agent-discovery files |
| `manifest.webmanifest` | site root → `/manifest.webmanifest` | PWA manifest (Base App / mobile installs) |
| `.well-known/agent.json` | site root → `/.well-known/agent.json` | A2A Agent Card — 7 read-only skills (`get-token-info`, `get-live-market`, `get-burn-percentage`, `get-charity-mission`, `get-takeover-grid`, `get-buy-link`, `verify-contract`). No auth required. |
| `.well-known/token.json` | site root → `/.well-known/token.json` | Canonical token metadata in machine-readable JSON |
| `.well-known/farcaster.json` | site root → `/.well-known/farcaster.json` | Farcaster Mini App manifest. **`accountAssociation` is empty and needs to be signed (see below).** |

## What the upgrade gets you

- **Base App / Farcaster clients** render a rich embed when the URL is shared (button: "🍔 Buy $BURGERS") via `fc:miniapp` and `fc:frame` meta tags.
- **AI search (ChatGPT, Claude, Perplexity, Gemini)** can pull a clean summary from `llms.txt` and the JSON-LD graph instead of guessing from your HTML and JS.
- **AI agents** following the A2A protocol can discover the site via `/.well-known/agent.json`, see the 7 skills it exposes, and call those skills without auth.
- **On-chain agents and wallet apps** can read the CAIP-19 identifier (`eip155:8453/erc20:0x06A0…dDc5`) from `<meta name="caip-19">`, the inline `#token-data` JSON block, and `/.well-known/token.json`.
- **DOM-walking agents** can find the contract address via `data-token-contract` on the contract pill, live stats via `data-metric` on the stat cards, and action targets via `data-action` + `data-platform` on every CTA link.

## Placeholder assets you need to create

The HTML/manifests reference three images that don't exist yet — please add them to your site root:

1. **`/og-burgers.png`** — 1200×630 PNG. Used for Open Graph, Twitter card, and Farcaster embed. Should show the Burger Money logo on the Base-blue gradient with the tagline. (Render once, host at root.)
2. **`/icon-512.png`** — 512×512 PNG. The Burger Money logo on a transparent or solid background. Used by Farcaster splash, PWA install, schema.org logo.
3. **`/icon-192.png`** — 192×192 PNG. Same logo, smaller. PWA install icon.

These three are the only new assets required. Everything else uses URLs you already control.

## One thing that needs signing

`/.well-known/farcaster.json` contains an empty `accountAssociation` block:

```json
"accountAssociation": { "header": "", "payload": "", "signature": "" }
```

To make the site eligible to be **added as a Mini App** in Farcaster / Base App (and to claim domain ownership), sign it once with the project's Farcaster custody wallet at:

<https://farcaster.xyz/~/developers/mini-apps/manifest>

Paste in `burgermoney.org`, sign with the custody wallet, then copy the three resulting values into the file. Until that's signed, rich previews and embeds still work — only the "Add to apps" attribution requires it.

## Verifying the result

After deploying, test with:

- **OG/Twitter card:** <https://www.opengraph.xyz/url/https%3A%2F%2Fwww.burgermoney.org%2F>
- **Farcaster embed:** <https://farcaster.xyz/~/developers/mini-apps/preview?url=https://www.burgermoney.org>
- **Schema.org / JSON-LD:** <https://search.google.com/test/rich-results?url=https://www.burgermoney.org/>
- **robots.txt:** <https://www.google.com/webmasters/tools/robots-testing-tool>
- **Agent card:** `curl https://www.burgermoney.org/.well-known/agent.json | jq`
- **LLM summary:** `curl https://www.burgermoney.org/llms.txt`

No code, design, or behavior on the visible site has changed — purely additive.
