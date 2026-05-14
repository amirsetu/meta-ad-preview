# Meta Ad Preview Tool — RapidGrade

## What this is
A single-file HTML app (`meta-ad-preview.html`) that lets the RapidGrade team preview Meta ads (Instagram Feed, IG Stories, FB Mobile, FB Desktop) inside accurate phone/monitor mockups before publishing. Built with React (CDN), no build step required — just open in browser.

## Project location
`/Users/amirsetu/Documents/Claude/Projects/Meta Ad Preview/meta-ad-preview.html`

## Tech stack
- React 18 (CDN via unpkg, Babel standalone)
- All CSS is inline via React style props — no external stylesheets
- localStorage for saving/loading ads between sessions
- No backend, no build tool, no npm

## Current placements implemented
| Placement | Component | Notes |
|---|---|---|
| IG Feed | `IGFeed` | Primary focus — most accurate |
| IG Stories | `IGStories` | Full-screen 9:16 with progress bars |
| FB Mobile | `FBMobile` | Facebook native card |
| FB Desktop | `FBDesktop` | Full FB feed with sidebars |

## IG Feed — exact layout order (matches real Instagram)
1. Status bar (9:41, signal, wifi, battery SVGs)
2. Instagram app nav bar (italic wordmark + heart/DM icons)
3. Previous post stub ("vantixmag...") — gives feed context
4. **Ad post:**
   - Profile header: gradient avatar, "rapidgrade" bold, "Ad" gray, gray pill Follow button, "···"
   - Image (full-width, respects `ad.aspectRatio`)
   - CTA strip (white bg, black bold headline text left, "›" right, thin separator)
   - Action bar: ♡ 48 · 💬 · ↺ · ➤ 1 | 🔖 — all SVG outline icons, counts inline
   - Caption: 2-line clamp with "more" (real IG behavior)
   - "View all 12 comments" + "3 DAYS AGO"
5. Next post stub (diekaffeefreunde.de) — gives scroll context
6. Instagram bottom nav bar (Home, Reels, Post, Search, Profile)

## Phone frame specs
- Outer bezel: `#1c1c1e`, borderRadius 50, side buttons as absolute divs
- Screen: `width: 375px, height: 812px` (iPhone standard)
- Status bar: 44px fixed, then flex column for feed content
- The IGFeed component manages its own scroll internally (flex + overflow)

## Ad data model
```js
{
  id: null,
  name: "",
  image: null,          // base64 JPEG, resized to max 1440px
  primaryText: "",      // truncates at 125 chars in preview
  headline: "",         // shown in CTA strip
  description: "",      // optional, shown in some placements
  cta: "Learn More",   // from CTA_OPTIONS list
  destinationUrl: "",   // used for domain display + click-through
  aspectRatio: "4/5",  // CSS aspect-ratio value: "1/1", "4/5", "9/16"
}
```

## Character limits (with color coding)
| Field | Recommended | Max |
|---|---|---|
| Primary Text | 125 | 500 |
| Headline | 27 | 40 |
| Description | 30 | 125 |

Color logic: green = under rec, orange = over rec, red = over max.

## Aspect ratio selector
Three buttons above the creative upload: **1:1 · 4:5 · 9:16**
Selected ratio stored in `ad.aspectRatio` and applied to the image `<div>` as CSS `aspect-ratio`.

## Save/load system
Uses `localStorage`. Index stored at key `rg-ads-index`, individual ads at `rg-ad:{id}`. Export button downloads a JSON backup file.

## Key design decisions made
- **No blue "Follow"** — real IG uses a gray pill button `{background: "#efefef", borderRadius: 10}`
- **"Ad" not "Sponsored"** — real IG feed ads show "Ad" below username, not "Sponsored · 🌐"
- **CTA strip is black, not blue** — text is bold black `#000`, not `#0095f6`
- **CTA shows headline field OR falls back to CTA button text** — `cut(ad.headline, 40) || ad.cta`
- **Caption is 2-line clamped** — uses `-webkit-line-clamp: 2`, always shows "more" after
- **Action bar counts are inline** — "48" sits next to the heart SVG, "1" next to the send icon
- **Repost icon included** — real IG has 4 action icons: heart, comment, repost, send

## What still could be improved
- IG Stories placement could use more polish
- FB Desktop is functional but not pixel-perfect
- No video/carousel creative support yet
- The "48 likes" and "12 comments" counts are hardcoded — could be editable fields
- Could add a "copy text" button for each field
- Dark mode toggle for the preview background
