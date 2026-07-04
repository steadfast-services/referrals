# Steadfast Senior Referrals — User Flow & Guide

**Platform:** Steadfast Senior Referrals  
**URL:** GitHub Pages (static SPA)  
**Last Updated:** July 4, 2026

---

## Table of Contents

1. [Platform Overview](#1-platform-overview)
2. [Site Structure](#2-site-structure)
3. [Family User Flows](#3-family-user-flows)
4. [Provider User Flows](#4-provider-user-flows)
5. [Admin / Team Workflows](#5-admin--team-workflows)
6. [All Forms — Quick Reference](#6-all-forms--quick-reference)
7. [Provider Directory — How It Works](#7-provider-directory--how-it-works)
8. [Pricing & Payments](#8-pricing--payments)
9. [Common Questions](#9-common-questions)
10. [Setup Checklist (Before Going Live)](#10-setup-checklist-before-going-live)
11. [All URLs — Quick Reference](#11-all-urls--quick-reference)
12. [Manual Testing Guide](#12-manual-testing-guide)

---

## 1. Platform Overview

Steadfast Senior Referrals is a free senior care matchmaking platform for Massachusetts families. It connects families seeking care with vetted local providers via a searchable directory and personalized referral service.

### Business Model at a Glance

| Who | What They Get | What They Pay |
|-----|--------------|---------------|
| Families | Free directory search + personalized matching | $0 (or $19.99/mo for dedicated advisor) |
| Providers | Qualified referrals sent to them | 10–15% commission on first month of care |
| Steadfast | Commission revenue + Premium subscriptions | — |

### Tech Stack

- **Frontend:** Single HTML file — vanilla HTML/CSS/JavaScript, no frameworks
- **Hosting:** GitHub Pages (free)
- **Forms:** Formspree (email forwarding)
- **Payments:** Stripe (payment link)
- **Data:** Provider directory lives in a Google Sheet (the editable "database"), fetched by the browser as CSV and cached in `localStorage`. A tiny hardcoded fallback list in `index.html` covers the rare case where the sheet can't be reached and no cache exists yet. See [Section 7](#7-provider-directory--how-it-works) for details.

---

## 2. Site Structure

The site is a **Single-Page Application (SPA)** — all six sections live in one HTML file. Navigation between sections uses the `show()` JavaScript function (not page reloads).

```
index.html
├── HOME          — Hero, trust signals, how-it-works, quick search, quick contact
├── DIRECTORY     — Searchable, filterable provider grid
├── PRICING       — Free vs. Premium plan comparison + corporate tier
├── CONTACT       — Personalized help form for families
├── JOIN          — Provider application form
└── PRIVACY       — Privacy policy (footer-linked, hidden from main nav)
```

### Navigation Flow Diagram

```
HOME
 ├── [Search Providers] ──────────────────────────────→ DIRECTORY
 ├── [Ask a Question] ────────────────────────────────→ CONTACT
 ├── [Quick Search Widget] → pre-fills filters ────────→ DIRECTORY
 └── [Quick Contact Form] → Formspree email

DIRECTORY
 └── [Inquire →] on any card → pre-fills name ─────────→ CONTACT

PRICING
 ├── [Find a Provider — Free →] ──────────────────────→ DIRECTORY
 └── [Get a Personal Advisor] ────────────────────────→ Stripe checkout

CONTACT
 └── [Form submit] → Formspree email → team responds same day

JOIN
 └── [Form submit] → Formspree email → team reviews within 48 hrs

FOOTER (all pages)
 └── Links to all main sections + cross-link to Steadfast Senior Services
```

---

## 3. Family User Flows

### Flow A — Self-Directed Search (Browse & Inquire)

**Best for:** Families who know what type of care they need and their region.

```
Step 1  Land on home page
        ↓
Step 2  Read hero section + "How It Works" steps
        ↓
Step 3  Use Quick Search widget
        • Select Care Type (e.g., "Companion Care")
        • Select Region (e.g., "South Shore")
        • Click "Search Providers →"
        ↓
Step 4  Land on filtered Directory
        • Browse provider cards (name, rating, reviews, bio, tags)
        • Re-filter anytime using the dropdown filters
        ↓
Step 5  Click "Inquire →" on chosen provider
        • Contact form opens, pre-filled: "I'm interested in [Provider Name]"
        ↓
Step 6  Complete contact form
        • Add name, email/phone, care details, timeline
        • Click "Send My Request →"
        ↓
Step 7  Success message: "Request Received! You'll hear from us today."
        ↓
Step 8  Team responds same day with provider's contact info + alternatives
```

**Time to submit:** ~2 minutes  
**Response time:** Same day (usually 2–4 hours)

---

### Flow B — Personalized Matching (Ask for Help)

**Best for:** Families unsure where to start, complex situations, or multiple care needs.

```
Step 1  Land on home page
        ↓
Step 2  Click "Ask a Question" in the hero
        ↓
Step 3  Complete the full Contact form (7 fields)
        • Name, phone, email
        • Care type needed
        • Region in Massachusetts
        • Describe the situation (budget, preferences, specific needs)
        • How soon is care needed?
        ↓
Step 4  Click "Send My Request →"
        ↓
Step 5  Success message shown. Team receives email.
        ↓
Step 6  Team manually reviews situation
        • Identifies top 3–5 matching providers
        • Considers region, care type, urgency, budget
        ↓
Step 7  Team calls or emails family with curated recommendations
        ↓
Step 8  Family contacts chosen provider(s) directly
```

**Time to submit:** ~3 minutes  
**Response time:** Same day

---

### Flow C — Premium Advisor (Dedicated Support)

**Best for:** Families who want a dedicated human guide through the process.

```
Step 1  Navigate to Pricing page
        ↓
Step 2  Read plan comparison
        • Free: Self-service directory access
        • Premium ($19.99/mo): Dedicated advisor + curated shortlist
        ↓
Step 3  Click "Get a Personal Advisor"
        ↓
Step 4  Stripe checkout opens — enter card details
        ↓
Step 5  Payment processed ($19.99)
        ↓
Step 6  Team assigns dedicated care advisor
        ↓
Step 7  Advisor calls within 2 business hours for 30-min intake call
        ↓
Step 8  Advisor delivers curated shortlist of top 3 providers
        ↓
Step 9  Weekly follow-up calls until care is arranged
        ↓
Step 10 Cancel anytime (no long-term contract)
```

**Revenue per customer:** $19.99/month  
**Expected churn trigger:** Once care is arranged

---

## 4. Provider User Flows

### Flow D — Provider Application & Onboarding

**Best for:** Licensed MA senior care agencies wanting qualified referrals.

```
Step 1  Provider finds Steadfast (Google, word of mouth, directory)
        ↓
Step 2  Reads "Join as Provider" page
        • Reviews 6 provider benefits
        • Understands commission-only model (no upfront fee)
        ↓
Step 3  Completes provider application form (12 fields)
        • Business name, contact name, phone, email
        • Primary service type + service areas (MA regions)
        • Years in business
        • Approximate hourly rate
        • Licensed in Massachusetts? (Yes / No / N/A)
        • Carries liability insurance? (Yes / No)
        • Business bio / description
        • Any additional notes
        ↓
Step 4  Submits application
        → Team receives email via Formspree
        ↓
Step 5  Team reviews within 48 hours
        • Verifies legitimacy (license, insurance, years in business)
        • Checks service areas align with MA regions covered
        ↓
Step 6  If approved: Team schedules 15-min verification call
        • Confirms details, explains commission structure
        • Answers questions from provider
        ↓
Step 7  Provider profile manually added as a new row in the Google Sheet
        → No code changes, no commit needed → live within a page refresh
          (visitors with a cached copy see it once the cache revalidates)
        ↓
Step 8  Provider appears in directory
        • Searchable by care type and region
        • Featured status available (gold border + badge)
```

**Approval timeline:** 24–48 hours + brief call  
**Cost to provider:** $0 upfront; 10–15% commission on successful placements

---

### Flow E — Provider Receives a Referral

**Current process (manual — no provider dashboard yet):**

```
Step 1  Family submits contact form inquiring about [Provider Name]
        ↓
Step 2  Formspree forwards email to team inbox
        ↓
Step 3  Team reviews family's care needs
        ↓
Step 4  Team emails provider:
        Subject: "Referral: [Family Name] in [City/Region]"
        Body: Family name, care needs, location, budget, urgency, contact info
        ↓
Step 5  Provider reviews and responds to team (or calls family directly)
        ↓
Step 6  If provider accepts:
        • Team notifies family
        • Family and provider arrange first meeting / service start
        ↓
Step 7  After care begins:
        • Provider invoices Steadfast for 10–15% of first month's care cost
        • Team processes commission (currently manual)
```

**Response expectation:** Provider should respond to referrals within 24 hours  
**Commission trigger:** Care begins (not when inquiry is made)

---

## 5. Admin / Team Workflows

### Receiving & Responding to Family Inquiries

1. Check Formspree email inbox (or connected Gmail).
2. Review form submission — note care type, region, urgency, budget.
3. Cross-reference with PROVIDERS array — match by `services[]` and `regions[]`.
4. Identify top 3–5 providers that fit.
5. Email or call the family same day with:
   - Recommended providers (name, phone/email, brief description)
   - Next steps (what to ask the provider, what to expect)
6. Log contact in whatever CRM/spreadsheet the team uses.

### Adding a New Provider to the Directory

Open the **Provider Directory Google Sheet** (link in [Section 11](#11-all-urls--quick-reference)) and add a new row. No code changes, no commit, no deploy — the site reads this sheet directly.

| Column | Example | Notes |
|--------|---------|-------|
| `id` | `9` | Next sequential number. Determines the icon (see below). |
| `name` | `Agency Name LLC` | |
| `type` | `Home Health Aide` | Primary service type label shown under the name |
| `services` | `Home Health Aide\|Personal Care` | Pipe-separated (`\|`), must match filter values below |
| `regions` | `Greater Boston\|North Shore` | Pipe-separated (`\|`), must match filter values below |
| `cities` | `Lynn, Salem, Beverly, Peabody` | Plain comma-separated text, shown as-is |
| `rating` | `4.8` | Numeric |
| `reviews` | `24` | Integer |
| `years` | `8` | Years in business |
| `featured` | `false` | `true` = gold border, appears first in results |
| `bio` | `2–3 sentence description...` | Focus on differentiators and trust signals |
| `tags` | `Medicare Certified\|Bilingual Staff\|Available 24/7` | Pipe-separated (`\|`), shown as green pills |
| `link` | `https://example.com` | Optional — adds a "Visit Site" button instead of "Inquire" |

**Valid `services` values** (must match filter dropdown exactly):
- `Home Health Aide`, `Companion Care`, `Assisted Living`, `Memory / Alzheimer's Care`, `Adult Day Programs`, `Medical Transportation`, `Hospice & Palliative Care`, `Personal Care / Non-Medical`

**Valid `regions` values** (must match filter dropdown exactly):
- `Greater Boston`, `North Shore`, `South Shore`, `MetroWest`, `Central MA`, `Western MA`, `Cape Cod & Islands`, `South Coast`

**No `icon` column in the sheet.** Emoji icons are keyed by `id` in `index.html` (`PROVIDER_ICONS`) instead — a prior attempt to store emoji directly in Sheets corrupted multi-byte emoji on CSV import. New rows automatically get a default 🏢 icon unless someone adds a mapping for that `id` in the code. Ask a developer to add one if a new provider needs a specific icon.

After saving the row, changes are live immediately for new visitors. Existing visitors with a cached copy (see [Section 7](#7-provider-directory--how-it-works)) pick it up automatically the next time the page loads and revalidates in the background — no manual cache-busting needed.

### Making a Provider "Featured"

Set the `featured` column to `true` for their row in the Google Sheet. Featured providers:
- Appear at the top of all search results
- Display a gold card border
- Show a "⭐ Featured" badge in the top-right corner

---

## 6. All Forms — Quick Reference

| Form | Location | Fields | Submission | Team Action |
|------|----------|--------|------------|-------------|
| Quick Contact | Home page | Name, Contact, Message | Formspree | Call/email within hours |
| Quick Search | Home page | Care Type, Region | Local JS | Redirects to filtered directory |
| Directory Filters | Directory page | Service, Region, Rating | Local JS | Real-time results, no submission |
| Family Inquiry | Contact page | Name, Phone, Email, Care Type, Region, Situation, Timeline | Formspree | Call/email same day |
| Provider Application | Join page | 12 business fields | Formspree | Review + call within 48 hrs |
| Premium Upgrade | Pricing page | Card info | Stripe | Assign advisor within 2 hrs |

### Form Behavior on Submit

All Formspree forms use AJAX (no page reload):
- **Button changes** to "Sending…" and disables during submission
- **On success (200):** Form hides, green success banner appears
- **On failure:** Red error message appears, button re-enables
- **Reset:** "Submit Another" button restores the form

---

## 7. Provider Directory — How It Works

### Data Source

Provider data lives in a Google Sheet (link in [Section 11](#11-all-urls--quick-reference)), which acts as the editable "database." On page load, `index.html`:

1. Reads any cached copy from the visitor's `localStorage` immediately, so the directory can render without waiting on a network request.
2. Fetches the sheet's published CSV export in the background.
3. On success, replaces the in-memory `PROVIDERS` array, re-renders the directory if it's currently open, and refreshes the `localStorage` cache.
4. On failure (offline, sheet unreachable), silently keeps whatever was already in `PROVIDERS` — the existing cache, or a small hardcoded `FALLBACK_PROVIDERS` list if there's no cache yet (e.g. a visitor's very first, offline load).

There is still no backend server — all filtering happens in the browser in real time, same as before. The sheet must remain shared as **"Anyone with the link — Viewer"** or the fetch will fail (silently falling back to cache/fallback data) — see [Section 11](#11-all-urls--quick-reference).

### Filtering Logic

When a user changes any filter, `renderProviders()` runs immediately:

1. **Service Type filter** — Checks if the selected type appears in the provider's `services[]` array (substring match, case-insensitive).
2. **Region filter** — Checks if the selected region appears in the provider's `regions[]` array.
3. **Rating filter** — Checks if `provider.rating >= selected minimum` (4.5+ or 4.0+).
4. Featured providers always sort to the top of results.
5. Result counter updates: "Showing X providers."
6. If no matches: "No Providers Found" message with a link to the contact form.

### What Each Provider Card Shows

- Icon + name + "Vetted ✓" badge
- Service type + years in business
- Star rating + numeric rating + review count
- 2–3 line bio
- Service tags (blue pills) — e.g., "Companion Care", "Errand Support"
- Special tags (green pills) — e.g., "Medicare Certified", "Bilingual Staff"
- Region + cities served
- "Inquire →" button (or "Visit Site" for providers with their own link)

---

## 8. Pricing & Payments

### Free Plan (Families)

- Full directory access
- Filter by service type, region, rating
- Submit inquiries to any provider
- Same-day team response
- Cost: $0, no account required

### Premium Plan ($19.99/month)

- Everything in Free, plus:
- Dedicated care advisor assigned
- 30-minute intake call within 2 business hours
- Curated shortlist of top 3 providers for your situation
- Weekly follow-up calls until care is arranged
- Exclusive access to newly listed providers
- Cancel anytime

**Payment:** Stripe payment link → $19.99 charged immediately → recurring monthly until cancelled.

### Corporate / EAP

- Bulk pricing for HR teams: $10–20/employee/month
- Offered as part of employee benefit packages
- Inquiries go through the Contact form (no automated flow yet)

### Provider Commission Model

- Providers pay nothing to list or apply
- When a referred family starts care: provider pays 10–15% of first month's care cost
- Commission is invoiced manually (no automated billing between Steadfast and providers yet)

---

## 9. Common Questions

**Q: Can a family search without submitting any form?**  
Yes. The directory is fully public. Families can browse and filter providers with zero friction. A form is only needed to inquire about a specific provider or request personalized matching.

**Q: What happens if no providers match a family's filters?**  
The directory shows "No Providers Found" with a link to the Contact form, where the team can assist manually.

**Q: How does a provider get added to the directory?**  
Manually. A provider submits an application → team reviews → team adds a new row to the Provider Directory Google Sheet ([Section 11](#11-all-urls--quick-reference)) → live for new visitors immediately, and for returning visitors once their cached copy revalidates.

**Q: Is there a provider login or dashboard?**  
Not in the current version. Referrals are forwarded to providers via email. A self-service dashboard is planned for a future version.

**Q: How does the team know which provider a family is interested in?**  
When a family clicks "Inquire →" on a provider card, the contact form pre-fills the message textarea with: `"I'm interested in learning more about [Provider Name]."` The team sees this in the Formspree email.

**Q: Can a provider be listed in multiple regions?**  
Yes. The `regions[]` array accepts multiple values. The provider's card will appear in search results for any of their listed regions.

**Q: What makes a provider "Featured"?**  
Setting the `featured` column to `true` for their row in the Google Sheet. Featured providers pay a higher commission rate or are partners. They appear first in all search results with a gold card border and "⭐ Featured" badge.

---

## 10. Setup Checklist (Before Going Live)

These items are required for the site to function fully. Currently set to placeholder values in `index.html`.

### Provider Directory Google Sheet

1. Confirm the sheet's sharing is set to **"Anyone with the link" → Viewer** (Share button, top right of the sheet). Without this, the site's fetch gets a 401 and every visitor silently sees only the 3-provider hardcoded fallback instead of the full directory.
2. Confirm the CSV export URL in `index.html` (`PROVIDER_SHEET_CSV_URL`) matches the sheet's file ID.
3. See [Section 11](#11-all-urls--quick-reference) for the sheet link and [Section 12](#12-manual-testing-guide) for how to verify this end-to-end.

### Formspree (3 forms) — ✅ Done

Live endpoints wired up under the "Steadfast Senior Services" Formspree project (3 separate
forms, one per site form):

| Site form | Formspree form | Endpoint |
|-----------|-----------------|----------|
| Quick Contact (home page) | Quick Contact | `https://formspree.io/f/xkolklzn` |
| Family Inquiry (Contact page) | Family Inquiry | `https://formspree.io/f/xvzjdjqg` |
| Provider Application (Join page) | Provider Application | `https://formspree.io/f/xdaryrdl` |

Double-check each form's **Settings → notification email** in the Formspree dashboard points to
the inbox you want leads landing in (`steadfastseniorservices@gmail.com` or otherwise). Watch
the 50 submissions/month free-tier cap across all three combined — upgrade to Formspree Personal
($15/mo, 200/month) if volume exceeds it.

### Stripe (1 payment link)

1. Create a Stripe account at [stripe.com](https://stripe.com)
2. Create a Payment Link for a $19.99/month recurring subscription product
3. In `index.html`, replace `REPLACE_WITH_YOUR_STRIPE_PAYMENT_LINK` in the Pricing section
4. Test the checkout flow with Stripe's test card

### GitHub Pages (hosting)

- Already live. No additional setup needed unless adding a custom domain.
- To add a custom domain: go to repo Settings → Pages → Custom domain → enter your domain → update your DNS.

### Formspree Free Tier Limit

- Free plan: 50 submissions/month across all forms
- If volume exceeds this: upgrade to Formspree Basic ($25/month) for 1,000 submissions/month

---

## 11. All URLs — Quick Reference

| What | URL | Notes |
|------|-----|-------|
| **Live site** | https://steadfast-services.github.io/referrals/ | GitHub Pages, built from `main` |
| **GitHub repo** | https://github.com/steadfast-services/referrals | Source for `index.html` |
| **Provider Directory Google Sheet (edit)** | https://docs.google.com/spreadsheets/d/1-bGJgeYhE3hhLjBZQkLf_0Ok4jKW4T3n2cszMKGdQ60/edit | The editable provider "database" — add/edit/remove rows here |
| **Provider Directory CSV export (used by the site)** | https://docs.google.com/spreadsheets/d/1-bGJgeYhE3hhLjBZQkLf_0Ok4jKW4T3n2cszMKGdQ60/export?format=csv | What `index.html` actually fetches — must stay shared as "Anyone with the link – Viewer" |
| **Cross-linked sister site** | https://steadfast-services.github.io/errands/ | Steadfast Senior Services (companion care/errands) — linked from footer and Provider #0's card |
| **Formspree** | https://formspree.io | Dashboard for the 3 forms (Quick Contact, Family Inquiry, Provider Application) — currently placeholder IDs, see [Section 10](#10-setup-checklist-before-going-live) |
| **Stripe** | https://stripe.com | Payment link for the $19.99/mo Premium plan — currently placeholder, see [Section 10](#10-setup-checklist-before-going-live) |
| **Business phone** | (781) 929-4623 | Shown in footer |
| **Business email** | steadfastseniorservices@gmail.com | Shown in footer |

---

## 12. Manual Testing Guide

Use this checklist after any change to `index.html`, the Google Sheet, or before going live. No build step is required — just open `index.html` directly in a browser or serve it locally.

### Local preview

```bash
cd path/to/referral
python -m http.server 8000
# then open http://localhost:8000/index.html
```

(Opening the file directly via `file://` also works for everything except the provider-data fetch, which some browsers block for local files — use the local server above to test that part.)

### A. Navigation

- [ ] Home page loads with hero, trust signals, "How It Works," quick search, quick contact form
- [ ] Every footer link (Home, Find Providers, Pricing, Join as Provider, Privacy Policy) opens the correct section
- [ ] Mobile nav (hamburger) opens and closes correctly on a narrow viewport

### B. Provider Directory data (Google Sheets integration)

- [ ] Open the Directory tab — confirm all 9 providers appear (not just the 3 fallback ones). If only 3 show up, the sheet likely isn't shared as "Anyone with the link – Viewer" (401 on fetch) — check DevTools Network tab for a failed request to `export?format=csv`.
- [ ] In DevTools → Application → Local Storage, confirm a `providersCache` key exists after the first load
- [ ] Edit a value in the Google Sheet (e.g. change a rating or bio), reload the page, and confirm the new value appears (may take a moment as it revalidates in the background — the first render is from cache, then it swaps in fresh data)
- [ ] Add a new row to the sheet, reload, confirm the new provider appears in the directory with the default 🏢 icon
- [ ] Temporarily rename `PROVIDER_SHEET_CSV_URL` to a bad URL, reload with an empty cache (clear Local Storage first) — confirm the page doesn't break and shows the 3-provider `FALLBACK_PROVIDERS` list instead. Revert the URL afterward.

### C. Directory search & filters

- [ ] Filter by Service Type — only matching providers show
- [ ] Filter by Region — only matching providers show
- [ ] Filter by Rating (4.5+ / 4.0+) — only matching providers show
- [ ] Combine multiple filters — results narrow correctly
- [ ] Filter to a combination with no matches — "No Providers Found" message appears with a working link to Contact
- [ ] Featured providers (gold border + "⭐ Featured" badge) always sort first
- [ ] Result counter ("Showing X providers") updates correctly
- [ ] Click "Inquire →" on a card — Contact form opens with the message pre-filled with that provider's name
- [ ] Click "Visit Site →" on Provider #0 (Steadfast Senior Services) — opens the errands site in a new tab

### D. Home page quick search

- [ ] Select a Care Type + Region in the home page quick search widget, click "Search Providers →" — lands on Directory with those filters already applied

### E. Forms (Quick Contact, Family Inquiry, Provider Application)

- [ ] Submit each form with valid data — button changes to "Sending…" and disables
- [ ] On success: green success message appears, form hides
- [ ] "Submit Another" resets the form correctly
- [ ] With Formspree still on placeholder IDs, expect the red error message to appear — this is expected until [Section 10](#10-setup-checklist-before-going-live)'s Formspree setup is complete; confirm it fails gracefully rather than breaking the page

### F. Pricing / Payments

- [ ] Free vs. Premium plan comparison renders correctly
- [ ] "Find a Provider — Free →" links to Directory
- [ ] "Get a Personal Advisor" opens the Stripe link (placeholder until [Section 10](#10-setup-checklist-before-going-live) is complete)

### G. Privacy page

- [ ] Loads from the footer link and reads correctly (not in main nav)

---

*Document covers index.html as of July 4, 2026. Update when new pages, forms, or integrations are added.*
