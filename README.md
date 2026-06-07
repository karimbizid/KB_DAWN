# KB Dawn

A custom Ghost theme by **Karim Bizid**, built on top of the official [Dawn](https://github.com/TryGhost/Dawn) starter theme.

> Ghost ≥ 5.0 required

---

## What is this?

KB Dawn started as an unmodified copy of Ghost's minimal Dawn theme and has been extended over multiple versions into a fully bespoke editorial layout. The changes below describe everything that was added or replaced compared to the original Dawn baseline.

---

## Custom features

### 1. Fixed light mode (`default.hbs`)
The original Dawn respects a Ghost admin setting to switch between light and dark colour schemes. KB Dawn always renders in light mode — the `theme-dark` / `theme-light` class toggle based on `@custom.color_scheme` has been removed and replaced with a hard-coded `class="theme-light"`.

---

### 2. Category-first homepage (`home.hbs`)
The homepage is built around **four content pillars** (Audio, Music, Technology, Flightsim), each rendered as a vertical column showing the five most recent posts in that category.

**Additions vs. Dawn:**
- **Welcome text injection** — if a Ghost page with slug `welkomtekst` exists, its body is pulled in as an intro block above the grid; falls back to the site description.
- **Sub-tag labels on cards** — each pillar column fetches posts with `include="tags"` so sub-tags (e.g. `technology-linux`) are available on the card. A small JavaScript snippet strips the pillar prefix and displays the remainder as a styled label (e.g. `#linux`).
- **Tag label CSS** — `.home-card-tags` / `.home-card-tag` utility classes added inline.

---

### 3. Pillar tag pages with sub-tag filter bar (`tag.hbs`)
Tag pages for the content pillars are split into two sections:

| Section | Content |
|---------|---------|
| **Algemeen** | Ghost *pages* (not posts) that carry this pillar tag — always visible, never filtered |
| **Alle posts** | All *posts* for the pillar, with an interactive sub-tag filter bar above them |

**Filter bar behaviour:**
- Sub-tags are detected automatically from a hidden `<span>` embedded in each post card that lists all tag slugs.
- Only tags whose slug starts with the pillar prefix (e.g. `technology-`) are shown as filter chips.
- Chips work as an **OR filter**: selecting multiple chips shows posts matching any of them.
- A "geen resultaten" message is shown when the active selection returns nothing.
- Tag labels on cards strip the pillar prefix: `technology-linux` → `#linux`.

**Tag naming convention for sub-tags:**
```
<pillar>-<subtopic>
```
Examples: `technology-linux`, `audio-tv`, `flightsim-737`.
A sub-tag that does *not* follow this convention will not appear as a filter chip on the pillar page.

---

### 4. Smart tag labels on single posts (`post.hbs`)
Post detail pages display the post's tags as styled chips below the title (`.single-tags` class: uppercase, bold, brand colour).

A JavaScript snippet automatically strips the pillar prefix from sub-tag labels at render time, so visitors only see the meaningful part:

```
technology + technology-linux  →  Technology  #Linux
```

The detection is purely slug-based (no hardcoding of pillar names), so it works for any pillar/sub-tag combination.

---

### 5. Tag chips on list cards (`partials/loop-home.hbs`)
The shared card partial used in all list views was extended with a tag row. Every public tag on a post or page is rendered as a `<span class="home-card-tag" data-slug="…">` element. This provides:

- Visual sub-tag context on every card in every list.
- A reliable DOM anchor that the filter-bar JavaScript can read from.

---

### 6. Viewpoints — a cross-pillar opinion section (`page-viewpoints.hbs`)  

A completely new page template that auto-applies to the Ghost page with slug **`viewpoints`**.

**How it works:**
- Lists every Ghost *page* that carries the internal tag `#viewpoint` (slug: `hash-viewpoint`).
- Renders a filter bar across *all* secondary tags found on those pages — not restricted to a single pillar.
- The filter bar is built from two sources combined: a hidden tag-slug span (captures all tags including internal ones) and the rendered card tag elements (public tags). This dual-source approach ensures new tags appear as chips regardless of their slug structure.
- Clicking a chip filters the list to only viewpoints that carry that secondary tag (OR logic across multiple chips).

**Ghost setup required:**
1. Create a Ghost page with slug `viewpoints`.
2. Tag any page with the internal tag `#viewpoint` to include it in the listing.
3. Add any secondary (public) tag to a viewpoint page to make it filterable — no naming convention required.

---

### 7. Floating Table of Contents (`partials/toc.hbs`)

On posts and pages, a floating TOC is automatically generated from the headings in the content and displayed as a sticky sidebar to the right of the text column. The logic lives in `partials/toc.hbs`, which is included by all post templates (`post.hbs`, `custom-full-feature-image.hbs`, `custom-narrow-feature-image.hbs`, `custom-no-feature-image.hbs`) and `page.hbs`.

**Behaviour:**
- Positioned dynamically via JavaScript: the left edge of the TOC is calculated from the right edge of the first `<p>` in `.gh-content`, so it always aligns with the actual text column regardless of full-width images.
- Hidden automatically when the screen is too narrow to fit the TOC next to the content without overlap.
- Requires at least **2 headings** in the content — on shorter posts it stays hidden.
- Reads **H2** (main sections) and **H3** (sub-sections) from `.gh-content`.
- H3 entries are slightly indented and smaller to reflect hierarchy.
- **Feature-image aware**: when a post has a feature image (`.single-media`), the TOC starts below the image and smoothly follows it upward as the user scrolls until the image leaves the viewport, then settles below the navigation header.
- **Inactive entries** are displayed in light grey.
- **Active entry** (the section currently in view) is displayed in bold dark text and updates automatically as you scroll.
- Clicking an entry smooth-scrolls to that heading.
- No configuration needed — it builds itself from whatever headings are in the post.

---

## Tag architecture overview

```
Pillar tags (public)        Sub-tags (public)           Special tags (internal)
────────────────────        ─────────────────           ───────────────────────
audio                       audio-tv                    #viewpoint  (hash-viewpoint)
music                       technology-linux
technology                  flightsim-737
flightsim                   …
```

- **Pillar tags** drive homepage columns and tag-page URLs.
- **Sub-tags** must be prefixed with `<pillar>-` to appear in the pillar filter bar and to have their label auto-shortened on cards and post pages.
- **`#viewpoint`** is a Ghost internal tag (prefix `#` in admin) used exclusively to flag pages as viewpoints. It is never shown to visitors.

---

## Version history

| Version | Notes |
|---------|-------|
| V1.0 | Initial custom homepage grid and category layout |
| V2.x | Iterative refinements (card design, tag display, CSS) |
| V3.2 | Sub-tag filter bar on pillar tag pages; smart label stripping on post pages |
| V3.3 | Viewpoints page template with cross-pillar filter bar |
| V3.4 | Floating Table of Contents on posts and pages |

---

## Development

```bash
npm install
npm run dev      # watch + livereload
npm run zip      # build and package for Ghost upload
```

Ghost requires the theme to be uploaded as a zip. The release zip for each version lives in the project root as `KB_DAWN_Vx.x.zip`.

---

## Credits

Based on [Dawn](https://github.com/TryGhost/Dawn) by [Ghost](https://ghost.org) — MIT licensed.  
Custom development by [Karim Bizid](https://karimbizid.nl).
