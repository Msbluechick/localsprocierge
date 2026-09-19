# localsprocierge.com — full specification

*Status: written 2026-09-18 from the source in this repository.*

---

## 1. What it is

The public marketing site and waiting-list capture for **ProCierge**, served at
**localsprocierge.com** (GitHub Pages, `CNAME`).

It is not the app. The app lives in `Msbluechick/ProCierge`. This repository holds a single static
site whose entire job is: explain the village, state the price honestly, and capture an email
address against a town.

**Owner:** Roots N Remedies LLC — a software company, Mississippi, USA.
**Contact:** contact@localsprocierge.com

---

## 2. Scope — and the constraint that defines it

| In scope | Out of scope |
|---|---|
| Positioning copy | Any app functionality |
| The membership price table | Any account, login, or session |
| Waiting-list capture (email + optional town) | Any personal data beyond those two fields |
| Privacy Policy, Terms & Refunds, Support | Analytics, ad pixels, trackers |

⛔ **No ad trackers, no analytics, no third-party scripts.** The only outbound request the page
makes is the waiting-list POST the visitor deliberately submits. Everything else — fonts aside — is
a local asset in this repository.

⛔ **L-03 binds every sentence on this page.** This is outward-facing marketing for a product that
has not launched, which makes it the single highest-risk surface in the whole estate for an
unprovable business claim. Every number on the page must be traceable to something Kayla decided.

---

## 3. Architecture

```
index.html          373 lines — the whole site: markup, CSS custom properties, one inline script
privacy-policy.html  98 lines
terms.html          101 lines — Terms & Refunds
support.html         69 lines
privacy.html         12 lines — redirect/stub kept so an old App Store URL does not 404
CNAME               localsprocierge.com
robots.txt, sitemap.xml
appicon.png · banner.png · logo.png · splash.png · favicon.ico
heart.png
orb-family.png · orb-help.png · orb-pulse.png · orb-verified.png
```

**Zero build step. Zero dependencies. Zero framework.** A single hand-written HTML file per page,
deployed by pushing to the branch GitHub Pages serves.

### Design system, as implemented in `:root`

| Token | Value | Role |
|---|---|---|
| `--bg` | `#0B1C1B` | deep forest ground |
| `--bg-lift` | `#0E2622` | raised surfaces |
| `--gold` / `--gold-bright` / `--gold-deep` | `#E0BD63` / `#F6E3A1` / `#B8862F` | the village lantern |
| `--teal` | `#14C3A0` | pulse / live |
| `--amber` | `#F2A02A` | warmth |
| `--red` | `#E23B4E` | the heart, and the Red Door's colour |
| `--ink` / `--sub` / `--muted` | `#F3ECD8` / `#CFE0DA` / 62% sub | type |

**Type:** Cinzel (display) · Cormorant Garamond (serif) · IBM Plex Sans (body) · IBM Plex Mono.

**Ground:** three stacked radial gradients on a fixed `body::before` at `z-index: -1` — teal from
the upper right, gold from the upper left, amber from below.

⛔ **L-52 — her art is sacred. No flat-green backgrounds.** The layered gradient ground is the rule,
not a decoration.

**Accessibility as built:** `:focus-visible` outlines in `--gold-bright` at 3px offset on every
link and button; `scroll-behavior: smooth`; the brand wordmark hides under 480px and the nav
collapses to a single Join button under 680px.

---

## 4. Page structure

| Section | Purpose |
|---|---|
| Sticky header | brand, nav (What it is · Membership · Join), animated heart, mobile Join button |
| Hero | the positioning paragraph + "Join the waiting list" / "See a first look" |
| Ribbon | *Launching soon in the Gulf South · Join the list → 3 months free* |
| **What you get** | four cards: the Pulse of your town · Real, verified people · Family-safety tools · Neighbors helping neighbors |
| **See it** | first-look media — *full trailer coming soon* |
| **Be a Blessing** | the heart, with a tap-to-open modal |
| **Membership** | the price table (§5) |
| **We never take a cut** | the differentiator paragraph |
| **Join** | the waiting-list form (§6) |
| Footer | legal links, contact, company line |

### The Heart — the modal

> ProCierge doesn't count likes, followers, or clout. The one thing it keeps is your heart.
>
> Every time you show up for a neighbor — a ride, a meal, a hand, a kind word — your heart beats a
> little stronger. **No public score. No leaderboard.** Just you, and the quiet record of the good
> you put into your village.
>
> Be a blessing.

⭐ *No public score, no leaderboard* is not marketing copy borrowed from the app — it is
`the-rules` **L-07** (no conclusions, no score) reaching the marketing surface. The two must stay
in agreement.

The heart image is masked with a radial `mask-image` and animated on a 2.6s `beat` keyframe with
`transform-origin: 50% 40%`.

---

## 5. The price table — the highest-risk content on the site

> When ProCierge opens, it's a straightforward monthly subscription — pick your plan, cancel
> anytime. That's the whole business. **No ads, no selling your data.**

| Plan | Price | Copy as published |
|---|---|---|
| **Resident** | **$9.99 / month** | Full access for one verified neighbor — the map, events, community, and help board. |
| **Family** | **$19.99 / month** | **At launch:** everything in Resident, plus kid accounts, trusted circles, and family-safety tools for the whole household. |
| **Local Business — Solo Local** | **$49 / month** | Food trucks, mobile services & one-person shops. Be findable to the whole village. |
| **Local Business — brick-and-mortar** | **$79 / month per location** | Light up your pin with today's specials. |

### We never take a cut

> Not of a sale, not of a favor, not of a dollar neighbors hand each other. Your membership is the
> only thing we ever charge — Facebook, TikTok, and Nextdoor can't say that.

### ⚠️ Two wordings on this page that are load-bearing

1. **"At launch:"** on the Family tier. It is a promise with a date attached rather than a claim
   about today. ⛔ Removing those two words converts a roadmap item into a false statement about a
   shipping feature.
2. **"full trailer coming soon"** in the See-it section. Same function. The section must not imply
   the trailer exists.

⛔ Per **L-03**, no price, tier, inclusion or free-month figure changes on this page without Kayla
setting it. This is the surface where an invented business term does the most damage.

---

## 6. The waiting list

### The offer, as published

> Join the list and you're a **Founding Villager — 3 months free** when ProCierge opens in your
> town. **Everyone who joins after launch gets one month.** We'll only email you about ProCierge —
> nothing else, ever.

This matches `ProCierge/INTRO-OFFER.md` exactly: everyone gets 1 free month; the early cohort gets
3. ⚠️ The two documents must be changed together or they will disagree.

### The mechanism

`joinWaitlist(e)` — a plain inline handler, no library:

1. Reads `#wl-email` (required, `type="email"`, `autocomplete="email"`) and `#wl-town`
   (optional, `autocomplete="address-level2"`).
2. Disables the button, shows "Saving…".
3. `POST` to the Supabase REST endpoint with the anon key, `Prefer: return=minimal`, and a body of
   `{ email, town: town || null, source: 'website' }`.
4. **Treats 201, 200 and 409 all as success** — 409 means already on the list, and telling a
   returning visitor they failed would be both untrue and unkind.
5. Swaps the form for the success panel: *"You're on the list ❤️ — We'll let you know the moment
   ProCierge opens in your area. Thank you for being an early one."*

### The two fields, and nothing else

The form collects an email and an optional town. That is the entire data footprint of this site.

> Your email stays private. **One verified human, one account — always.** (**L-47**)

**Security note.** The anon key in the page is a public publishable key by design; the protection is
the Row-Level Security policy on the waiting-list table, which must permit `INSERT` and **nothing
else** to the anon role. ⛔ A `SELECT` grant to anon on that table would publish the list. This is
the single security invariant of this repository.

---

## 7. Legal surfaces

| Page | Why it exists |
|---|---|
| `privacy-policy.html` | the canonical policy; linked from the footer and from the app stores |
| `privacy.html` | a 12-line stub kept so a previously published URL does not 404 |
| `terms.html` | Terms **& Refunds** — refunds named in the title because the product is a subscription |
| `support.html` | the support URL App Store Connect and Play Console both require |

⚠️ `ProCierge/AUDIT-BRIEF` history records a privacy-URL fix landing in this repo's last commit
(*"Fix ProCierge marketing: privacy URL, mobile Join, family copy, robots"*). The store listings
point at these pages; **changing a filename here breaks a store listing.**

---

## 8. ⛔ Why this specification contains no traffic or conversion projection

The brief this answers asked for funnel and revenue modelling. On this surface specifically, that
is the most dangerous thing that could be written.

**L-03** exists because an invented business term — *"free through advocates, permanently"* — was
once written into this project and nearly reached a state agency. A marketing site is precisely
where such a sentence escapes. A projected conversion rate on a pre-launch waiting list is a number
with no source, and once it exists in a repository it gets quoted back as though someone decided it.

**What this document states instead:** the four prices actually published, the two free-month
offers actually decided, and the two hedging phrases ("At launch:", "coming soon") that keep the
page true. Everything else is arithmetic for a worksheet that does not ship.

---

## 9. Invariants

1. **No tracker, no analytics, no third-party script.** The only outbound call is the waiting-list
   POST the visitor submits.
2. **Anon has INSERT on the waiting-list table and nothing else.**
3. **Every price and every free-month figure matches `ProCierge/INTRO-OFFER.md`.**
4. **"At launch:" and "coming soon" stay** until the things they hedge actually ship.
5. **No page filename changes** without updating both store listings.
6. **No public score, no leaderboard** — here or in the app (**L-07**).

---

## 10. Roadmap

1. Ship the first-look media and delete *"full trailer coming soon"* the same day.
2. Confirm the waiting-list RLS policy is INSERT-only for anon (§6) — this is a check that has
   never been seen to fail, and **L-29** says that is not yet a check.
3. On launch: change the ribbon and the Founding Villager copy from future to present tense, and
   drop the "At launch:" hedge from the Family tier at the moment it becomes true — not before.
4. Keep `sitemap.xml` and `robots.txt` current as pages are added.
