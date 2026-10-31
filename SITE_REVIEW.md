# Site Review — jsolon.online

Review of this repository as of 2026-10-31: what it is, and what could be improved.

## What this repo is

**jsolon.online** is the personal portfolio site of **Joash A. Solon**, a licensed architect
and master plumber in Taytay, Rizal, Philippines. It is a pure static site — no build system,
no dependencies, no backend — built on the **HTML5 UP "Hyperspace"** template and served via
GitHub Pages (`origin: ujsolon/jsolon.online`, `CNAME` → `www.jsolon.online`).

### Layout

| Path | Role |
|---|---|
| `index.html` | The real site: fixed sidebar nav → intro/bio → 3 spotlight projects (HAMCC chapel, Bamert, Haseli) → six "Eyes/Lips/Hands/Arms/Body/Legs" capability cards → contact → footer |
| `pdfs/` | resume + one project deck each + a full `portfolio.pdf` |
| `images/` | project cover renders + leftover template demos |
| `assets/js` | jQuery 3.6.0 + HTML5 UP's `scrollex` / `scrolly` / `breakpoints` / `util` + `main.js` |
| `assets/css`, `assets/sass` | compiled `main.css` (73 KB, unminified) + full SCSS source |
| `generic.html`, `elements.html` | untouched template demo pages — still live on the domain |

Commits show a clean arc: template → personalized content → restructure around projects →
tag styling → a profile-pic add/revert. Active, hand-maintained, ~11 commits.

## Possible improvements

### Correctness / polish (cheap, do first)

1. **Copy bug** at `index.html:56` — `"Hello, this is A featured project showcasing…"`.
   That's a leftover editor note plus a stray capital. The site also switches voice: the bio
   at `index.html:38` reads as first person, while `index.html:100` refers to "Joash" in third
   person.
2. **Template demo pages are publicly reachable.** `/generic.html` and `/elements.html` link to
   "Generic" / "Elements" nav items and show lorem-ipsum `pic01`–`pic06` images. Anyone who
   guesses the URL sees an unfinished template, and search engines can index them. Delete them,
   or `noindex` them and strip them from the nav.
3. **`portfolio.pdf` (3.2 MB) is linked nowhere.** There is a "View Resume" button at
   `index.html:43` but no "Download Full Portfolio". `cover1.png` (865 KB) is likewise
   unreferenced. Either wire both up or delete them.

### Performance (biggest measurable win)

4. Images are the payload. `bamert.png` is **1.7 MB** and `hamcc.png` 952 KB for what renders as
   a ~600 px thumbnail — both PNGs of photographic renders. Converting to WebP/AVIF at 2×
   display size takes the homepage from ~3.6 MB to roughly 300 KB.
5. `assets/js/main.js:159` sets `background-image` from the *same* `<img src>`, so each cover is
   fetched as both an image and a CSS background — and `$img.hide()` at `main.js:166` means the
   `<img>` exists purely to feed the background.
6. **2.9 MB of webfonts.** Five formats (`.eot`, `.svg`, `.ttf`, `.woff`, `.woff2`) per family.
   EOT/SVG/TTF are IE/legacy and dead weight — the SVG files alone are 1.8 MB.
   `fontawesome-all.min.css` also ships every icon for roughly eight actually used.
7. `main.css` is 3,908 unminified lines, and the whole jQuery 3.6.0 stack (89 KB + ~20 KB of
   plugins) exists only to run scroll-reveal and smooth-scroll. About 40 lines of
   `IntersectionObserver` + CSS would replace it and remove the dependency (3.6.0 is several
   releases behind).

### Discoverability & accessibility

8. The `<head>` (`index.html:8-14`) has **no `meta description`, no canonical, no Open Graph or
   Twitter tags, no favicon, no JSON-LD**. For a site whose main traffic is a link pasted into
   chat or email, the missing preview card is a real loss.
9. `user-scalable=no` at `index.html:11` blocks pinch-zoom — a WCAG 1.4.4 failure that modern iOS
   ignores anyway. Remove it. There is also no skip link, no `<main>` landmark, and
   `prefers-reduced-motion` is not honored despite the fade-up animations.

### Content (highest value for an architect's site)

10. Missing trust signals a PH client looks for: a **PRC/BOA license number**, and a verifiable
    one. Also absent: project address/type/year metadata, client testimonials, a drawing-process
    timeline, and a LinkedIn or **WhatsApp** link — WhatsApp is the default business channel in
    the Philippines, and `mailto:` is a weak call to action.
11. Project covers open a raw PNG in a new tab (`target="_blank"` at `index.html:52`). A lightbox,
    or a per-project page with the full plate set, would showcase the work far better than a PDF
    download.
12. Three projects is thin. If more decks exist, add a filterable grid rather than another
    spotlight block. Note that adding a project today means copy-pasting a 15-line HTML block,
    which is acceptable at this scale.

### Repo hygiene

13. `README.txt` is still HTML5 UP's own template readme — it does not describe this site.
    Rewrite as `README.md`: what the site is, how to deploy, how to add a project, and the
    CNAME/Pages setup. Keep the html5up credit (required by CCA 3.0 — currently satisfied in the
    footer at `index.html:176`).
14. No `.gitignore` (this is a OneDrive folder, so sync artifacts can leak in), no CI, no
    analytics. A small GitHub Actions workflow running link-check + Lighthouse CI on push would
    catch dead links and size regressions automatically. The ~10 MB of binaries also makes every
    clone heavy.

## Suggested order of work

1. Quick tier — copy fixes, remove the template pages, wire up `portfolio.pdf`, add SEO/social
   meta tags.
2. Media tier — image format/size pass, font subsetting, drop legacy font formats.
3. Framework tier — replace jQuery + plugins with vanilla `IntersectionObserver`.
4. Content tier — license number, WhatsApp/LinkedIn, per-project pages or lightbox.
5. Repo tier — real `README.md`, `.gitignore`, CI checks.
