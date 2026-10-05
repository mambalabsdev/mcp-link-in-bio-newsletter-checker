# Link in Bio Scraper and Newsletter Detector MCP Server

[![Smithery](https://smithery.ai/badge/mambabuilt/mcp-link-in-bio-newsletter-checker)](https://smithery.ai/servers/mambabuilt/mcp-link-in-bio-newsletter-checker) [![Glama score](https://glama.ai/mcp/servers/mambalabsdev/mcp-link-in-bio-newsletter-checker/badges/score.svg)](https://glama.ai/mcp/servers/mambalabsdev/mcp-link-in-bio-newsletter-checker) [![MCP Registry](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fregistry.modelcontextprotocol.io%2Fv0%2Fservers%3Fsearch%3Dcom.mambabuilt%252Fmcp-link-in-bio-newsletter-checker%26limit%3D1&query=%24.servers%5B0%5D._meta%5B%22io.modelcontextprotocol.registry%2Fofficial%22%5D.status&label=mcp%20registry&color=blue)](https://registry.modelcontextprotocol.io/v0/servers?search=com.mambabuilt/mcp-link-in-bio-newsletter-checker&limit=1) [![npm version](https://img.shields.io/npm/v/@mambalabsdev/mcp-link-in-bio-newsletter-checker)](https://www.npmjs.com/package/@mambalabsdev/mcp-link-in-bio-newsletter-checker) [![npm downloads](https://img.shields.io/npm/dm/@mambalabsdev/mcp-link-in-bio-newsletter-checker)](https://www.npmjs.com/package/@mambalabsdev/mcp-link-in-bio-newsletter-checker) [![license](https://img.shields.io/github/license/mambalabsdev/mcp-link-in-bio-newsletter-checker)](https://github.com/mambalabsdev/mcp-link-in-bio-newsletter-checker/blob/main/LICENSE) [![mcpservers.org](https://img.shields.io/badge/mcpservers.org-listed-blue)](https://mcpservers.org/servers/mambalabsdev/mcp-link-in-bio-newsletter-checker)

MCP server for the Mamba Labs [Link in Bio Scraper and Newsletter Detector](https://apify.com/mambalabs/link-in-bio-newsletter-checker) actor on Apify.

Creator handles or link in bio URLs in, a newsletter verdict and contact details out.

## Install

```bash
npx -y @mambalabsdev/mcp-link-in-bio-newsletter-checker
```

### Claude Desktop

```json
{
  "mcpServers": {
    "mamba-link-in-bio-newsletter-checker": {
      "command": "npx",
      "args": ["-y", "@mambalabsdev/mcp-link-in-bio-newsletter-checker"],
      "env": { "APIFY_TOKEN": "your-apify-token" }
    }
  }
}
```

Get an Apify token at [console.apify.com/account/integrations](https://console.apify.com/account/integrations).

## Tool

### `check_link_in_bio_and_newsletter`

Follow a creator bio link and report the newsletter, what they sell, and the contact emails.

| Input | Type | Required | Description |
| --- | --- | --- | --- |
| `handles` | array of string | no | One per line. A profile URL on any supported platform (https://www.tiktok.com/@name, https://www.instagram.com/name/, https://www.youtube.com/@name, a Pinterest, Twitch, or Threads profile, an Apple Podcasts show page, or a Spotify show), or platform:@handle (tiktok:@name). A bare @handle needs `platforms` and is looked up on each listed platform. One entry is a single run; a list is a batch. Duplicates are removed before any fetch. |
| `bio_links` | array of string | no | Skip the profile read and start from these pages directly (a Linktree, Stan Store, linkin.bio page, or the creator's own site). One per line. When one input item carries both a handle and bio links, the links are read as that creator's links and the actor returns one row for that creator, not one row per link; to get one row per link, pass the links in bio_links alone. |
| `platforms` | array of `tiktok`, `instagram`, `youtube`, `pinterest`, `twitch`, `threads`, `podcast` | no | Which platforms a bare @handle is looked up on. A full profile URL carries its own platform and ignores this. This actor does not search; pass the creators you want read. Supported: TikTok, Instagram, YouTube, Pinterest, Twitch, Threads, and podcasts. Not X, not Facebook pages, not LinkedIn. |
| `render_unreadable_pages` | boolean | no | Off by default. Some link-in-bio pages (Stan Store, linkin.bio, Typeform shells) return an empty shell to a plain fetch and are classified unknown_fetch_failed. Turn this on to render them in a headless browser. Charged per page rendered (event browser-render) to cover the browser compute. Default: false. |
| `ai_check` | boolean | no | Off by default. When on, a model reads the rule classifier's evidence and rules on each row. Runs only with your own key in `ai_api_key`; the actor never uses a Mamba Labs key and never logs yours. Default: false. |
| `ai_provider` | one of `anthropic`, `openai` | no | Which API the key belongs to. Default: "anthropic". |
| `ai_api_key` | string | no | Your own model API key. Used only when `ai_check` is on. Never stored, logged, or written to a row. |
| `scan_website_for_email` | boolean | no | Off by default. For creators with their own website (not a link-in-bio page), reads the home, contact, and about pages and the footer for an email and records where it was found. Charged per creator scanned (event website-scan). Default: false. |
| `match_agencies` | boolean | no | Matches the domain of a manager or business email against the bundled talent agency list and fills agency_name, agency_domain, and agency_match_method. Charged per matched row (event agency-match). Default: true. |
| `escalate_on_block` | boolean | no | On by default. A profile fetch that comes back as a bot detection page is retried once over the residential proxy. On Instagram the bio, bio link, and following are read from the profile page over residential when the embed and the datacenter API did not carry them, and a page that comes back readable charges instagram-bio-fetch ($0.010). Uncheck it to never pay that event: a blocked profile then returns a labeled error row, and Instagram rows keep an empty bio and bio link on about half of the reads. Default: true. |
| `max_residential_reads` | integer | no | Caps how many Instagram profile pages one run reads over the residential proxy (each readable one charges instagram-bio-fetch). 0 means no cap. Once the cap is reached, later Instagram rows keep what the embed widget and the datacenter API returned, and error_reason reads escalation_skipped on the rows that would have escalated. Only acts when escalate_on_block is on. Default: 0. |
| `max_download_mb` | integer | no | The largest page or file the actor downloads from a creator's links, in megabytes. A link whose Content-Length is over the limit is not downloaded; a link with no Content-Length is read up to the limit, then stopped and discarded. Either way the row comes back with every other field filled and error_reason reads file_too_large, and the rest of the batch is not affected. 1 to 200. Default: 25. |
| `batch_size` | integer | no | Rows fetched at once. Leave empty for the measured per platform default; the measurement is in the README. Higher is faster and, above the measured point, loses rows. |
| `contribute_to_shared_pool` | boolean | no | On by default. Contributes the public records this run finds to a shared creator and agency pool that all users of this actor read from, so a later run reads what this one found. Only public data already in the returned rows is sent, never your Apify account, your input list, or your API keys, and a contribution is not charged. Set false to read the pool and write nothing. Default: true. |

Nothing is required. Link in Bio Scraper and Newsletter Detector answers a run with no usable input with a row carrying `row_status` and `error_reason` rather than failing, and the tool mirrors that.

## Pricing

Link in Bio Scraper and Newsletter Detector is pay per event on Apify. Every price below is flat across every Apify plan tier, read from the live pricing record on 2026-10-05.

| Event | Charged for | Price | Fires when |
| --- | --- | ---: | --- |
| `actor-start` | Actor start | $0.002 | Once per run, on start. Covers the run overhead. |
| `links-checked` | Links checked | $0.008 | Once per creator whose bio link or link-in-bio page was fetched and classified. A creator with no bio link returns newsletter_status none from the bio alone and does not charge this event. |
| `browser-render` | Browser render | $0.004 | Once per page rendered in the headless browser because the plain fetch did not return readable content (Stan Store, linkin.bio, Typeform shells) and the rendered page came back readable: 120 characters of visible text or 3 links off the page's host, and no block page. A render that returns the same empty shell is not charged, and a charged render always reaches the classifier. Only when the option is on. Priced to cover the browser compute. |
| `website-scan` | Website scan | $0.005 | Once per creator whose own website (not a link-in-bio page) was scanned for an email on the home, contact, about, and footer. Only when the add-on is on. |
| `agency-match` | Agency match | $0.003 | Once per creator row where a manager or agency email domain was matched against the bundled agency list and an agency name came back. |
| `instagram-bio-fetch` | Instagram bio fetch | $0.01 | Once per Instagram profile row when the bio, bio link, and following were not on the embed widget or the datacenter API and the profile page was read over the residential proxy and came back readable. Only when escalate_on_block is on. Never on the embed or datacenter reads, never on another platform, never on a blocked page, and never on an error row. |

The platform's synthetic `apify-actor-start` event is not in this actor's pricing record and is not charged: the actor's own `actor-start` event above covers the run overhead.

## Reading the output

Every row carries `row_status` and `error_reason`. A creator the actor could not read comes back as a row saying why, not as a gap in the list, so an absence is readable rather than inferred. Filter on `row_status` before loading a table.

## What this actor shares

The run contributes the records it finds to a shared creator and agency pool that all users of this actor read from. What one run finds, the next run can read.

The toggle is `contribute_to_shared_pool`. It is on by default. Set it to false and the run still reads the pool and writes nothing to it.

**What this actor contributes.** The manager contacts it finds, the agency it matched them to, and any manager email domain the pool has not seen before, as a candidate agency for a later run to confirm.

**Only public data that is already in your own output.** Every field written to the pool is a field this run returned to you, read from a page the platform or the creator publishes to anyone without a login. Nothing from your Apify account, your input list, your API keys, or your own notes is sent. A contribution never deletes anything from the pool.

**What a contribution is labeled with.** The actor ID, the run ID, the pool key issued to the actor build, and a hash of the calling IP address, used for the rate limit and nothing else. Your Apify account and your user ID are not recorded.

**Contributing is free.** No event is charged for a write to the pool. If the pool is unreachable the run finishes as normal, the rows are dropped, and the run log says so.

## Actor

Actor ID `OorucdheTIgu7RFzK`. The wrapper calls the actor by that immutable ID rather than by its Store slug, so a Store rename never breaks it.

## Suite

| Actor | Actor ID | MCP server |
| --- | --- | --- |
| [Influencer Finder](https://apify.com/mambalabs/creator-finder) | `tpkedmloIIWtXx6sg` | [`@mambalabsdev/mcp-creator-finder`](https://www.npmjs.com/package/@mambalabsdev/mcp-creator-finder) |
| [Influencer Profile Scraper](https://apify.com/mambalabs/creator-profile-reader) | `EqEnklDVMIcB1V499` | [`@mambalabsdev/mcp-creator-profile-reader`](https://www.npmjs.com/package/@mambalabsdev/mcp-creator-profile-reader) |
| [Link in Bio Scraper and Newsletter Detector](https://apify.com/mambalabs/link-in-bio-newsletter-checker) | `OorucdheTIgu7RFzK` | [`@mambalabsdev/mcp-link-in-bio-newsletter-checker`](https://www.npmjs.com/package/@mambalabsdev/mcp-link-in-bio-newsletter-checker) |
| [Influencer Change Monitor](https://apify.com/mambalabs/creator-change-monitor) | `d2VVgahNL6UmcLkhg` | [`@mambalabsdev/mcp-creator-change-monitor`](https://www.npmjs.com/package/@mambalabsdev/mcp-creator-change-monitor) |
| [Influencer Lead List Builder](https://apify.com/mambalabs/creator-lead-list-all-in-one) | `KnmByszcv135yM30G` | [`@mambalabsdev/mcp-creator-lead-list-all-in-one`](https://www.npmjs.com/package/@mambalabsdev/mcp-creator-lead-list-all-in-one) |
| [Influencer Talent Agency Lookup](https://apify.com/mambalabs/talent-agency-lookup) | `zCuX4Mgyg6JvXgGzd` | [`@mambalabsdev/mcp-talent-agency-lookup`](https://www.npmjs.com/package/@mambalabsdev/mcp-talent-agency-lookup) |

Built by [Mamba Labs](https://mambabuilt.com).
