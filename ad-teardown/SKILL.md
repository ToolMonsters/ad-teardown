---
name: ad-teardown
description: Give it one brand and get back the ads they run on Facebook and Instagram, ranked by how long each has survived. Reveals their proven messages, their real priority funnel, and the angle nobody in the category is taking. Built entirely on Bright Data. Triggers on "what ads is X running", "ad teardown", "tear down their ads", "swipe file for", "how does X advertise", or /adteardown.
---

# Ad Teardown

One brand in. A ranked map of everything they pay to say, out.

**Never describe a brand's advertising from memory.** Ad creative rotates weekly and training data is stale by definition. Read the library, then analyze.

## You need Bright Data (free to start)

This skill reads the Meta Ad Library through the Bright Data MCP.

**Bright Data gives every account 5,000 free requests a month, no credit card required, renewed on the 1st.**

👉 **[Create your free Bright Data account](https://brdta.com/toolmonsters)**

Then connect the Bright Data MCP to Claude (Settings → Connectors) with your API token.

## The two moves that make this sharp

**Run duration is the only honest performance signal in public.** Nobody publishes spend or CTR. But losing ads get switched off, and winning ads get left on. An ad running eight months has beaten every other creative in that account, repeatedly, against real money. Sort by duration and the top of the list is the brand's most-tested message. Everything else is a test that failed.

**Landing page concentration reveals the real priority.** A brand's homepage says what they want to be known for. The URL that 40 of their 60 ads point at says what they are actually trying to sell this quarter. When those two disagree, the ads are telling the truth.

## What it reads (Bright Data only)

Everything comes from the Meta Ad Library through `scrape_as_markdown`. No other tool, no other MCP, no API key beyond the Bright Data token.

Exact-phrase search (the default, re-tested 2026-10-04):
```
https://www.facebook.com/ads/library/?active_status=all&ad_type=all&country=US&q=<brand>&search_type=keyword_exact_phrase&media_type=all
```

**Use `keyword_exact_phrase`, not `keyword_unordered`.** On 2026-10-04 an unordered search for "ClickUp" returned 44 ads and not one of them was from ClickUp or mentioned it (Temu, tea, cleaning products). The exact-phrase search returned the real ads.

**Page ID gives the cleanest result. Try it second:**
```
https://www.facebook.com/ads/library/?active_status=all&ad_type=all&country=US&view_all_page_id=<PAGE_ID>&media_type=all
```
To get the numeric page ID, search the web for `"<brand>" facebook page id` or open the brand's Facebook page "About / Page transparency" section. In the 2026-10-04 run, ClickUp's page ID (304981139881438) came from a web search and the page-ID scrape worked. It returned HTTP 502 in an earlier test (2026-08-13): if it fails, fall back to exact-phrase and keep going.

**One scrape = one page of results, about 25 to 30 ads.** The library may say "~800 results" but you only see the first page. To widen the sample, scrape both `active_status=active` and `active_status=all`, by page ID and by exact phrase, then dedupe by Library ID. Always tell the user the sample size ("57 of their ~800 ads"), never "every ad".

Per ad the library returns: advertiser and page URL, Library ID, start and end date, active status, full ad copy, landing page (wrapped in an `l.facebook.com/l.php?u=` redirect), platforms, and a variant count reading "N ads use this creative and text".

## Source limits, stated up front

| Source | Status |
|---|---|
| Meta Ad Library, Facebook and Instagram | Works |
| Google Ads Transparency | Not reachable. Bright Data returns "this endpoint is not supported" |
| LinkedIn and TikTok ad libraries | Untested. Attempt the scrape, report the real result, never assume |

**The library carries no spend, no impressions, no reach and no targeting.** Any number in those categories is fabricated. Duration and variant count are the signals, and they are enough.

## How to run it

**1. Resolve the brand.** Run the exact-phrase search, then try the page-ID view if you can find the numeric page ID. Merge both, dedupe by Library ID.

**2. Scrape inside a subagent.** Not optional. A single library page returns tens of thousands of characters, mostly navigation chrome and repeated redirect tokens. Have the subagent return only: advertiser, library_id, start_date, end_date, status, ad_copy, unwrapped landing_url, platforms, variant_count.

Unwrap landing pages by taking the `u=` parameter of the redirect and URL-decoding it.

**2b. Set `active_status` deliberately.** With `all`, every returned ad reads Inactive because the live ones are a small minority of the archive. If the user asks what a brand is running *now*, use `active_status=active`. Use `all` only for the historical arc.

**3. Filter contamination and say how much.** Meta matches keywords loosely, and at scale this is brutal: a search for `kajabi` reported ~7,100 results, and the first page was dominated by course sellers (Dan Lok, Tyler Tometich, Richmond Dinh) who merely mention the platform. Their ads are not Kajabi's ads. A real run for "notion" in France returned a Thai marketing course, a movie promo and several coaching funnels alongside the genuine ads. Drop anything whose advertiser does not plausibly match, drop ads reading "This ad was run by an account or Page we later disabled", and **report the drop count**. If more than half is noise, say so, switch to exact phrase if you were not already on it, then to page ID.

**4. Rank by duration, then by variant count.** Many variants of one message means scaling, not testing. Variant count carries more signal than it looks: in a real run, one Kajabi lead-magnet ad showed "48 ads use this creative and text" while most sat at 2 to 5. That gap is the brand telling you which bet is the big one.

**5. Run each country separately.** Results differ by market. A US sweep says nothing about France.

## Recency windows

- **Active ads**: current, no caveat
- **Ended within 90 days**: recent, treat as live intelligence
- **Ended over 90 days ago**: labeled "retired, as of <date>", never presented as current strategy
- The output is always stamped with the date it was checked

## The output

Five sections. The card is the deliverable, nothing gets pushed anywhere.

```
Ad Teardown — Acme Data
Meta Ad Library · US · checked 2026-08-13
58 ads found, 41 analyzed, 17 dropped as unrelated

1. CONTROL ADS — their proven messages
| Ran | Days | Variants | Hook | Landing |
| Nov 2025 → active | 287 | 12 | "Stop rebuilding pipelines every quarter" | /platform |
| Feb 2026 → active | 190 | 8  | "Your data team is doing plumbing"        | /platform |
Two ads past 6 months, both pointed at /platform, both leading with pain.
That is the message that survived.

2. THE OFFER, AND HOW IT MOVED
Through Q4 2025 they led with speed ("5-minute setup"). From Feb 2026
every long-running ad leads with pain instead. They stopped selling ease
and started selling the cost of the status quo.

3. LANDING PAGE CONCENTRATION
/platform      31 ads   ← the real priority
/pricing        6 ads
/templates      4 ads
The homepage receives zero ads. They are selling one product, not a suite.

4. HOOK INVENTORY
Pain-first openers: 9 ads, avg 140 days survival
Feature-first openers: 14 ads, avg 21 days survival
Social proof openers: 3 ads, avg 9 days
Pain-first wins by 7x on survival. Feature-first is where their tests die.

5. ANGLE GAPS — what nobody here says
No ad mentions migration cost. No ad names a competitor. No ad targets
the analyst persona, which they courted in 2025 and have now abandoned
in paid. That persona is unclaimed.
```

End every teardown on the angle gaps. A list of ads is not intelligence.

## Good to know

**Track it over time.** Save a brand and ask to re-run. The diff matters more than the snapshot: new creatives, killed controls, a landing page that changed, a message that flipped.

**Honest about thin data.** A brand with three ads gets a three-ad readout that says the sample is thin. It never gets padded into a strategy narrative.

**No ads is a finding.** An empty library does not mean low budget. It means Meta is not their channel, and that is worth saying plainly.

**Creator ads are visible.** Ads tagged with a partner hashtag and a creator page co-listed as advertiser mean a running influencer budget and a process to spend it.

## What this skill does NOT do

- Use any MCP other than Bright Data. It is fully self-contained
- Report spend, impressions, reach or targeting. The source does not carry them
- Cover Google Ads Transparency. That endpoint is blocked, and the skill says so instead of substituting a guess
- Hide contamination. The drop count is always reported
- Present retired ads as current strategy
