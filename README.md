# Ad Teardown

**This skill reads the ads your competitor is paying for. So you can copy what works.**

Type `/adteardown` and one brand name. Claude pulls their ads from the Meta Ad Library, ranks them by how long they've been running, and breaks down the hook, the offer and the landing page of each one.

```
/adteardown ClickUp
/adteardown monday.com, US, active ads only
/adteardown Notion vs Airtable vs Asana
```

## What you get back

A real run on ClickUp (US, October 2026), 58 ads:

> ClickUp runs 15 ads built from your own usage. "You've filled all 5 Spaces the free plan allows. That means your org has outgrown it." One ad per limit, 10 different landing pages.

And on 10 project management tools, 182 ads:

- 3 of the 10 run zero ads in the US (Wrike, Coda, Basecamp)
- monday.com runs 15 ads attacking Airtable by name, all sending to a comparison page
- Airtable's oldest live ads, running since March, all offer a report or a guide

## Why it works

**Run duration is the only honest performance signal in public.** Nobody publishes spend or click-through rates. But losing ads get switched off, and winning ads stay on. The longest-running ad in an account has beaten every other creative, again and again, against real money.

**Landing page concentration shows the real priority.** The homepage says what a brand wants to be known for. The page most of its ads point at says what it is actually trying to sell this quarter.

## You need Bright Data (free to start)

Ad Teardown reads the Meta Ad Library through the Bright Data MCP. It is the skill's only dependency.

**Bright Data gives every account 5,000 free requests a month, no credit card required, renewed on the 1st.**

👉 **[Create your free Bright Data account](https://brdta.com/toolmonsters)**

Then copy your API token from your account settings (Settings → Users → API token).

## Setup

### 1. Connect Bright Data

**Claude (web or desktop):** Settings → Connectors → Add custom connector, and paste:

```
https://mcp.brightdata.com/mcp?token=YOUR_API_TOKEN
```

**Claude Code:**

```bash
claude mcp add --transport http brightdata "https://mcp.brightdata.com/mcp?token=YOUR_API_TOKEN"
```

The skill only needs the base tools (`scrape_as_markdown` and `search_engine`), which are on by default.

### 2. Install the skill

**Claude (web or desktop):** download [`ad-teardown.zip`](ad-teardown.zip), then Settings → Capabilities → Skills → upload it.

**Claude Code:**

```bash
git clone https://github.com/ToolMonsters/ad-teardown
cp -R ad-teardown/ad-teardown ~/.claude/skills/
```

Start a new session so the skill loads.

## What it can and can't see

- **Meta only** (Facebook and Instagram). Google Ads Transparency is not reachable through Bright Data, and the skill says so instead of guessing. LinkedIn and TikTok ad libraries are untested.
- **A sample, not everything.** Each library page returns about 25 to 30 ads. The skill widens the sample (active and all, by page ID and by exact phrase) and always tells you the size: "58 of their ~800 ads", never "every ad".
- **No spend, no impressions, no targeting.** The library does not publish them. Run duration and variant count are the signals, and they are enough.
- **One country at a time.** A US sweep says nothing about France.

## License

MIT, by [Tool Monsters](https://toolmonsters.com).
