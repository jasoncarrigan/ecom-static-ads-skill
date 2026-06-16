# 🎨 ecom-static-ads

> Turn one store link — a product page or a collection page — into 4 scroll-stopping static (single-image) DTC ad creatives. Built as a Claude skill.

This is a PDP/PLP-driven take on AI ad creative: instead of uploading a product photo, you paste a link and the skill reads the page, pulls the real product photos *and the store's own customer reviews*, and grounds every ad in them.

---

## ⚡ Quick Setup

**Copy `SKILL.md`, paste it into Claude, and say:**

> *"Help me set this skill up. Walk me through the keys I need and store them safely on my machine."*

Claude will tell you the keys, help you store them in your shell config, and have you restart once.

**⚠️ Security note — treat keys like passwords.** Add them yourself in your own terminal, never paste keys into a chat. Rotate them after setup, and never commit any env file to Git.

---

## 🔑 The 3 Keys You'll Need

| # | Provider | What it does | Env var | Get key |
| --- | --- | --- | --- | --- |
| 1 | **Google AI Studio** | Image generation (Nano Banana 2 / Gemini) | `GEMINI_API_KEY` | [aistudio.google.com/apikey](https://aistudio.google.com/apikey) |
| 2 | **Tavily** | Customer research + reference imagery | `TAVILY_API_KEY` | [tavily.com](https://tavily.com) |
| 3 | **Scrape Creators** | Competitor ads from the Facebook Ads Library | `SCRAPE_CREATORS_API_KEY` | [scrapecreators.com](https://scrapecreators.com) |

Gemini is the only **required** key. Tavily and Scrape Creators sharpen the research but **fall back to web search** if you skip them.

---

## 🧑‍🍳 What this actually does

Give it **one link** — a product page (PDP) or a collection page (PLP). It runs a mini DTC creative team:

- **Reader** scrapes the page for product names, prices, promos, benefits, the real product photos — and the store's own on-site customer reviews.
- **Strategist** starts with those first-party reviews, then mines Reddit, Amazon, and TikTok for the real customer language and the angle.
- **Competitive analyst** scrapes the top-impression competitor ads running right now.
- **Creative director** writes 4 distinct ad concepts — punchy headline, ICP angle, full visual direction.
- **Art director + photographer** generates the finished ad images with Nano Banana 2, using your real product photo as a locked reference.
- **Strategist** hands back a campaign brief with funnel-stage recommendations, an A/B plan, and platform-specific copy.

**PDP** → 4 concepts for that product. **PLP** → 4 concepts across the collection, each able to hero a different product.

---

## 🚀 Running it

```
Use the ecom-static-ads skill on this product page:
https://yourstore.com/products/your-product
```

Or a collection:

```
/ecom-static-ads https://yourstore.com/collections/bestsellers
```

Outputs land in `./ad-workspace/<timestamp>/`:

- `ad-1-*.png` through `ad-4-*.png` (the four creatives)
- `references/` (the source imagery the model used, incl. the scraped product photos)
- `campaign-brief.md` (the strategic wrap-up)

---

## 🔧 Manual install

```
mkdir -p ~/.claude/skills/ecom-static-ads
curl -sL https://raw.githubusercontent.com/jasoncarrigan/ecom-static-ads-skill/main/SKILL.md \
  -o ~/.claude/skills/ecom-static-ads/SKILL.md
```

Then add to `~/.zshrc` (or `~/.bashrc`):

```
export GEMINI_API_KEY="..."
export TAVILY_API_KEY="..."            # optional
export SCRAPE_CREATORS_API_KEY="..."   # optional
```

> **Run it in Claude Code** (the Code tab of the desktop app, or the CLI) — it needs network access to reach Gemini, Tavily, and Scrape Creators. It won't run in Cowork's sandbox.

If your keys aren't picked up in the Code tab, add them to `~/.claude/settings.json` under an `"env"` block — the Code tab reads that even when it can't see `~/.zshrc`.

---

## 💸 Cost to run

- Claude Pro/Max: required to run skills.
- Gemini image generation: free tier covers casual use, then ~$0.03 / image.
- Tavily: free tier (1,000 searches/mo) is plenty.
- Scrape Creators: ~$29/mo (optional).

---

## 📁 Works for any store

Apparel, supplements, cookware, RV parts, pet food, beauty — the skill reads your page and infers the rest. Drop in a link, generate, iterate.
