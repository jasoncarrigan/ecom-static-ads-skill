# 🎨 ecom-static-ads

> Turn one store link — a product page or a collection page — into 4 scroll-stopping static (single-image) DTC ad creatives. Built as a Claude skill.

This is a PDP/PLP-driven take on AI ad creative: instead of uploading a product photo, you paste a link and the skill reads the page, pulls the real product photos *and the store's own customer reviews*, and grounds every ad in them.

---

## ⚡ Quick Setup

**Copy `SKILL.md`, paste it into Claude, and say:**

> *"Help me set this skill up. Walk me through the keys I need and store them safely on my machine."*

Claude will tell you the keys, give you the commands to store them in your **macOS Keychain** (encrypted at rest), add one line to your shell config that reads them back into env vars, and have you restart once.

**⚠️ Security note — treat keys like passwords.** Add them yourself in your own terminal, never paste keys into a chat. Store them in the **macOS Keychain** (see [Manual install](#-manual-install)) rather than as plaintext in `~/.zshrc` — that keeps them encrypted at rest and out of any dotfiles you might commit. Rotate them after setup.

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

Then store your keys. **Don't put them as plaintext in `~/.zshrc`** — that file is easy to accidentally commit and isn't encrypted. On macOS, put them in the **Keychain** and have your shell read them back at startup.

**1. Save each key into the Keychain** (run in your own terminal, swap in your real values). `-U` lets you re-run to update a key later:

```
security add-generic-password -U -a "$USER" -s GEMINI_API_KEY          -w 'AIza...'
security add-generic-password -U -a "$USER" -s TAVILY_API_KEY          -w 'tvly-...'   # optional
security add-generic-password -U -a "$USER" -s SCRAPE_CREATORS_API_KEY -w '...'        # optional
```

**2. Read them into env vars at session start.** Add this to `~/.zshrc` (or `~/.bashrc`) — it loads the keys without storing their values in the file:

```
for k in GEMINI_API_KEY TAVILY_API_KEY SCRAPE_CREATORS_API_KEY; do
  v=$(security find-generic-password -a "$USER" -s "$k" -w 2>/dev/null) && export "$k=$v"
done
unset k v
```

The first time this runs, macOS prompts to allow Keychain access — choose **Always Allow** so future sessions are quiet.

> **Not on macOS?** Keep the keys in a separate file your shell sources — e.g. `~/.config/ecom-static-ads/secrets.env` with `chmod 600` and gitignored — and add `[ -f ~/.config/ecom-static-ads/secrets.env ] && source ~/.config/ecom-static-ads/secrets.env` to `~/.bashrc`. Never commit the secrets file.

> **Run it in Claude Code** (the Code tab of the desktop app, or the CLI) — it needs network access to reach Gemini, Tavily, and Scrape Creators. It won't run in Cowork's sandbox.

If your keys aren't picked up in the Code tab, the Code tab also reads an `"env"` block in `~/.claude/settings.json` even when it can't see `~/.zshrc`. That file stores values in plaintext, so prefer the Keychain approach above; if you must use it, treat `settings.json` like a secret and never commit it.

---

## 💸 Cost to run

- Claude Pro/Max: required to run skills.
- Gemini image generation: free tier covers casual use, then ~$0.03 / image.
- Tavily: free tier (1,000 searches/mo) is plenty.
- Scrape Creators: ~$29/mo (optional).

---

## 📁 Works for any store

Apparel, supplements, cookware, RV parts, pet food, beauty — the skill reads your page and infers the rest. Drop in a link, generate, iterate.
