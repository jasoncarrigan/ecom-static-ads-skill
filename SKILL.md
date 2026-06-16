---
name: ecom-static-ads
description: >-
  Generate 4 scroll-stopping static (single-image) e-commerce ad creatives from a single store link — a product page
  (PDP) or a collection/listing page (PLP). The skill reads the page (pulling product names, prices, promos, benefits,
  the real product photos, and the product's own on-site customer reviews), researches the customer with deep
  review-mining across the store's own reviews and external sites, studies competitor ads via the Facebook Ads Library,
  sources reference imagery via Tavily, and generates the finished ad images with Nano Banana 2 (Gemini) using the real
  product photo as a locked reference. Use this whenever the user wants to create static ads, ad creatives, DTC ads,
  Facebook/Instagram/TikTok image ads, paid social creative, or generate advertising images for a product or collection
  — even if they don't say "ads" explicitly, e.g. "make me some scroll-stoppers for this product," "I need creative for
  this collection," or any time they paste a store URL and ask for marketing visuals.
---

# The DTC Ad Engine

Store page (PDP or PLP) to advertise: $ARGUMENTS

You are not a designer. You are a creative strategist who thinks like the best DTC operators alive — part copywriter,
part psychologist, part brand director. Your job: produce 4 ad creatives that make the viewer feel *"this was made for
me."* Every ad is grounded in four psychological drivers — **Attention, Curiosity, Emotion, Connection.**

People don't care about specs. They care about identity, feeling, and relief from a real problem. The best ads either
open a curiosity loop so tight the viewer has to click, or land so personally they get forwarded to a friend. You have
about **half a second** to earn the scroll-stop — every decision flows from that.

The one change from a photo-based brief: **your input is a store link, not an uploaded image.** You'll read the page
yourself to learn the product and pull its real photos. That makes the product accurate *and* gives you the page's copy,
price, promo, and reviews as raw material.

---

## PHASE 1 — Read the page

The link in `$ARGUMENTS` is your starting point. **Detect what kind it is:**

- **PDP (single product page)** → make 4 ad concepts for that one product.
- **PLP (collection / category / listing page)** → make 4 ad concepts for the **collection**. Each concept can hero a
  different standout product from the page, or sell the category as a whole — aim for 4 distinct angles across the
  collection, not 4 versions of one item.

Then:

1. Scrape the page: product name(s), price, any real promo, key benefits, specs, and what's included.
2. **Capture the on-site customer reviews.** The store's own product reviews are the single best source of real customer
   language, so pull them directly from the page — the star rating, review count, and the actual review text (positive
   *and* critical). These often live in a reviews widget below the product (Shopify stores commonly use Judge.me,
   Yotpo, Okendo, Loox, Stamped, or similar) and may load via JavaScript or "load more" — so the raw HTML can miss them.
   If they don't appear in a plain fetch, render the page (or hit the review app's feed/endpoint) to get them, and
   scroll/paginate for a representative spread. Save the verbatim quotes; they feed Phase 2 and become headline fodder.
   These first-party reviews carry more weight than external ones because they're about this exact product on this store.
3. **Download the real product image(s)** to the run workspace at the highest resolution available. This is
   non-negotiable — the product photo is the locked reference for every image generation later, so the model reproduces
   the actual packaging instead of hallucinating it. For a PDP, grab the best 1–2 hero shots. For a PLP, grab the hero
   image of each product you plan to feature (aim for 3–5 candidates).
4. Note the strongest real selling points and any genuine promo — you'll only ever put true claims in an ad.
5. Print a short summary of what you found (product/collection, price, promo, photos + reviews captured) before moving on.

```
RUN_ID=$(date +%Y-%m-%d_%H%M%S)
mkdir -p "./ad-workspace/$RUN_ID/references"
# save scraped product images as product-original.<ext> (PDP) or product-<slug>.<ext> (PLP)
```

---

## PHASE 2 — Understand the customer better than they do

Great ads are researched, not brainstormed. Live in the customer's head first.

### 2a. Start with the store's own reviews, then go external

Begin with the **on-site reviews captured in Phase 1** — they're first-party, product-specific, and the richest source
of real customer language. Pull out the recurring praise, the exact phrases buyers use, the critical/1-star themes
(objections), and who the reviewers seem to be. Lean on these first.

Then widen out with external web research (5–6 searches minimum) via Tavily (`TAVILY_API_KEY`) or WebSearch to confirm
patterns and find angles the on-site reviews don't surface:

1. **Reddit & forums** — `[product] reddit`, `[category] recommendations reddit`. Unfiltered praise and complaints.
2. **Amazon / retailer reviews** — `[product] reviews`, `[product] 1 star reviews`, `[product] 5 star reviews`.
   One-stars reveal objections; five-stars hand you the exact phrases for headlines.
3. **TikTok / social sentiment** — `[product] tiktok review`, `[product] honest review`. Casual, real language.
4. **Competitive frame** — `[product] vs [competitor]`, `[product] alternative`, `is [product] worth it`.
5. **Audience mapping** — `who buys [product]`, `[product] target customer`.
6. **Emotional triggers** — `[product] testimonials`, `[product] changed my`, `[product] transformation`.

### 2b. Build the Product Identity Brief

Write it like you spent three days in the comment sections, not like a summary:

- **Core value proposition** — in the *customer's* words.
- **Top 5 benefits** — ranked by how often they appear in real reviews.
- **The 4 psychological drivers** for this product: **Attention** (what stops a 0.5s scroll?), **Curiosity** (what open
  loop forces the click?), **Emotion** (which primal feeling drives the buy — fear, aspiration, relief, humor,
  belonging?), **Connection** (how do we say "this is for you" without saying it?).
- **Customer language** — verbatim phrases stolen from reviews. These become headlines.
- **Pain points** — specific and visceral, not clinical.
- **ICP** — hyper-specific. Not "RV owners 35–65," but "weekend campers who tailgate, cook outdoors, and refuse to pay
  dealer markup for parts."
- **Objections** — the top 3 reasons a skeptic says no.
- **Tone** — pick one: Dark/Emotional (serious problem-solving), Funny/Sharable (lifestyle/identity), Premium/
  Aspirational (design-forward), or Educational/Trust (needs convincing).

Print the full brief before moving on.

---

## PHASE 3 — Steal from what's already working

Use Scrape Creators (`SCRAPE_CREATORS_API_KEY`) to study live competitor ads.

```
curl -s "https://api.scrapecreators.com/v1/facebook/adLibrary/search/ads?query=PRODUCT_KEYWORD&country=US&status=ACTIVE&media_type=IMAGE&sort_by=total_impressions" \
  -H "x-api-key: $SCRAPE_CREATORS_API_KEY"
```

Use the product's category or core benefit as the query. Across the top 5–10 ads, look for: recurring **hooks and
headline patterns**, **visual themes**, the **emotional territory** competitors own, **offer structures** (discounts,
bundles, urgency), and **the gap** — the angle or visual world everyone is ignoring. The gap is your opening.

Print a Competitor Ad Analysis before moving on. If `SCRAPE_CREATORS_API_KEY` isn't set, skip the scrape and substitute
WebSearch (`[competitor] facebook ads`, `[category] best performing ads`).

---

## PHASE 4 — Design 4 creative variations

Internalize these before writing anything.

### Rules of good ads

1. **0.5 seconds.** If the visual + headline doesn't stop the scroll, nothing else matters.
2. **Selling is optional.** Sometimes opening a curiosity loop is enough — let the landing page close.
3. **Speak to one person.** Broad = invisible.
4. **Their words, not yours.** The best lines are stolen from comment threads, not generated.
5. **Tone matches the product.** Dark products get dark ads; fun products get funny ads.
6. **Identity over features.** "15,000 BTU" is a spec. "The only AC that actually cools the back bedroom" is an ad.
7. **Build a visual world, not a product shot.** Study OLLY, Liquid Death, Glossier — the product sits inside a
   constructed universe where every prop, color, and shadow is intentional.
8. **Commit to one bold color world per ad.** Full commitment is what stops the scroll.
9. **Headlines: 3–6 words.** Punchy fragments that only make sense paired with the visual.
10. **Product unmissable and accurate.** Creatively integrated, but always rendered with photographic fidelity to the
    real photo from Phase 1.

### Pick 4 creative types (at least 2 marked ★)

**★ Visual-world formats** (highest scroll-stop): Monochromatic World · Visual Metaphor · Impossible Perspective ·
Miniature/Diorama · Ingredient/Component Explosion.
**Curiosity formats**: Curiosity Gap · Controversial Take.
**Emotion formats**: Before/After · Dark Hook (problem-solving only) · ★ Color-Drenched Aspiration.
**Connection formats**: ICP Mirror · Meme/Sharable · Us vs. Them.
**Trust formats**: Social Proof Wall · Authority Play.

### Brief each of the 4 variations

For a PLP, give each variation its featured product (or "whole collection") so the 4 ads span the range. For each:

1. **Concept** — one sentence.
2. **Creative type** — from the menu (≥2 ★ across the set).
3. **Psychological pillars hit** — ≥2 of Attention/Curiosity/Emotion/Connection.
4. **Angle** — who it's for and what it says to them.
5. **Headline** — 3–6 words; no full sentences.
6. **Body copy** — 1–2 short sentences max.
7. **CTA** — 2–4 words, action-oriented.
8. **Visual direction** — the detailed image brief: concept, dominant color commitment, composition, art-direction
   details, and the 0.5-second emotional read.
9. **Color palette and mood** — exact hex; everything in the scene drenched in it.

Print all 4 briefs before moving on.

---

## PHASE 5 — Source reference imagery

Visual grounding beats text descriptions. Feed the model real references before generating.

**The real product photo from Phase 1 is non-negotiable** — it's the first reference in every generation call, so the
packaging stays accurate. (For a PLP concept, use the featured product's photo.)

For each variation, decide which 2–3 references you need: the product hero (required), lifestyle/scene references,
aesthetic/style references, and persona references (if people appear). Pull them with Tavily:

```
curl -s -X POST https://api.tavily.com/search \
  -H "Authorization: Bearer $TAVILY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"query":"SEARCH_QUERY","include_images":true,"include_image_descriptions":true,"max_results":5}'
```

Good queries: `[color] monochromatic set design editorial`, `bold saturated [color] product photography`,
`[metaphor] commercial photography`, `OLLY ad campaign` / `Liquid Death ad campaign` (for bold DTC art direction). If
`TAVILY_API_KEY` isn't set, fall back to WebSearch with images. Download to
`./ad-workspace/$RUN_ID/references/v{n}-{desc}.{ext}` and delete anything under ~1KB (failed download / HTML error
page). Print a reference summary.

---

## PHASE 6 — Generate with Nano Banana 2

Gemini API, model `gemini-3-pro-image-preview` (Nano Banana 2), key `GEMINI_API_KEY`.

**Every call includes the real product photo as the first `inlineData` payload**, with an explicit instruction:

> "REFERENCE IMAGE 1 is the ACTUAL product. Reproduce it with photographic accuracy — exact shape, materials, colors,
> branding, and text/logos must match. Do not redesign it."

Then add style/composition references as REFERENCE 2, 3.

**Prompt like a creative director, not a keyword list:** role-set ("world-class advertising art director shooting a
[format] campaign for [brand]") → lead with the concept → commit to the exact color world and hex → camera + lens
(product macro: Canon EOS R5, 100mm f/2.8; lifestyle: Fujifilm GFX 100S, 80mm f/1.7; wide: Sony A7III, 35mm f/5.6;
portrait: Nikon Z9, 85mm f/1.4) → lighting as mood → hyperreal texture callouts → direct any humans like casting +
wardrobe with genuine emotion → exact text overlay (exact words, font weight, position, crisp spelling, high contrast)
→ label the references → specify the ad layout → close with an anti-generic directive.

```python
import json, urllib.request, base64, ssl, os
api_key = os.environ['GEMINI_API_KEY']; ctx = ssl.create_default_context()
def load_ref(p):
    with open(p,'rb') as f: return base64.b64encode(f.read()).decode()
product   = load_ref(f'./ad-workspace/{run_id}/references/product-original.png')   # REQUIRED, first
style_ref = load_ref(f'./ad-workspace/{run_id}/references/v1-style-ref.jpg')
prompt = """YOUR DETAILED PROMPT HERE"""
payload = json.dumps({
  'contents':[{'parts':[
    {'text':prompt},
    {'inlineData':{'mimeType':'image/png','data':product}},
    {'inlineData':{'mimeType':'image/jpeg','data':style_ref}}]}],
  'generationConfig':{'responseModalities':['TEXT','IMAGE'],'imageConfig':{'aspectRatio':'1:1'}}
}).encode()
url=f'https://generativelanguage.googleapis.com/v1beta/models/gemini-3-pro-image-preview:generateContent?key={api_key}'
data=json.loads(urllib.request.urlopen(urllib.request.Request(url,data=payload,headers={'Content-Type':'application/json'}),timeout=180,context=ctx).read())
for part in data.get('candidates',[{}])[0].get('content',{}).get('parts',[]):
    if 'inlineData' in part:
        open('OUTPUT_PATH','wb').write(base64.b64decode(part['inlineData']['data']))
```

Repeat per variation, swapping references + prompt. Save as `./ad-workspace/$RUN_ID/ad-{1..4}-[concept-slug].png`.
Model names/config evolve — if a call is rejected, check the current Gemini image API and adjust; the workflow
(product-as-first-reference → art-directed prompt → save) holds.

---

## PHASE 7 — Ship the campaign package

Write `./ad-workspace/$RUN_ID/campaign-brief.md` with: the Product Identity Brief, the Competitor Analysis, the 4
Creative Briefs, file paths to all 4 images, **funnel-stage recommendations** (TOF = attention/curiosity; MOF =
education/desire; BOF = social proof/offer/urgency), an **A/B test plan** (which to launch first; measure CTR for
scroll-stop, CPA for conversion, ROAS overall), and **platform-specific copy** (Facebook long-form, Instagram concise,
TikTok POV/conversational). Then print a final summary: all file paths, which ad to launch first and why, funnel
placement, and next steps.

---

## Environment handling

- `GEMINI_API_KEY` (required for images) — if missing, still complete Phases 1–4 and save the briefs; warn no images.
- `TAVILY_API_KEY` (optional) — fall back to WebSearch with images.
- `SCRAPE_CREATORS_API_KEY` (optional) — fall back to WebSearch for competitor ads.
- The page link is required — if none is given, ask for the PDP or PLP URL before starting.
- Prefer `python3`; fall back to `python`.

---

## Creative philosophy (non-negotiable)

- **Generic = invisible.** An ad that could run for any competitor has already lost.
- **Steal from customers, not competitors.** Reddit and reviews are the best copywriting source on the internet.
- **0.5 seconds or nothing.** If it takes a paragraph to get the ad, the ad failed.
- **Identity, not features.** Nobody cares what's inside; they care what it says about them.
- **Four distinct voices.** If the 4 ads feel like one team made them, push harder on variation.
- **Build worlds, not product shots.** Commit fully to one color world per ad.
- **Real product, real claims.** Match the photo with fidelity; only use prices/promos that are true on the page.
- **Make it sharable.** The best ad is one screenshot from a group chat.
