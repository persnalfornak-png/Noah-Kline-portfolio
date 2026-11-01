# Portfolio Deep-Build Checklist
Started: 2026-10-07 17:24 EDT
Instructions: survive context resets by reading this file first. Mark [x] when done, add notes inline.

## Noah's picks from the brainstorm (2026-10-07)
- [1] Full case-study pages — YES, built
- [2] Video as hero — YES. Video NOT chosen by Noah; a default was picked (Phase 2). Swappable.
- [3] Grain/film texture overlay — YES, built
- [4] "Numbers" stats strip — MAYBE. Built, but the figures are placeholders.
- [5] Magnetic/cursor-aware buttons — YES, flagged "may be removed later" — built to be trivially removable
- [6] Custom cursor "View" label on hover — NOT MENTIONED — SKIPPED
- [7] Dark grain/vignette on hero image — "maybe not" — SKIPPED
- [8] Favicon + social share preview image — YES, built
- [9] Mobile responsive pass — YES, done

---

## IMPORTANT CORRECTIONS DISCOVERED 2026-10-07 (settled — do not re-litigate)

The original tile copy described the projects wrongly. After looking at the actual
source files, the real subjects are:

| Project | What it actually is |
|---|---|
| Layering | NOT "portrait & studio photography". It is physical torn-paper photomontage — B&W portraits printed, hand-torn, re-registered into composite faces, then rephotographed. |
| BW Hands | NOT plain "hand studies". It is black & white double-exposure portraits — hands multiplied over faces, shot on location (gym, field, stands). Plus a companion 4-panel cropped-eye sheet. |
| Drifting | NOT "CG + motion short films". It is commercial film work for a drift driving academy (workshop interview, car details, plus exterior coverage at a second rural site). Drive path: Bussnisses/Drifting. |
| Sailing | On-the-water plus FPV drone documentary of Hobie catamarans racing (NZ coastline, sail numbers "NZL 272"). |

Other established facts:
- Layering scans are stored 90 degrees rotated. `sips -r 270` makes them upright; already applied to everything in `assets/layering/`.
- Filenames like "Sky Short 1 CG 4k" mean colour graded, not computer graphics.
- A 5th Drive project is unused: `Bussnisses/The lost Spring` — a day spa shoot. Candidate for a 5th case study.
- ffmpeg is NOT installed and must not be installed without Noah's explicit per-instance approval (standing guardrail). Video work uses a self-built native Swift/AVFoundation tool, `_vidtool` (source `_vidtool.swift`). Rebuild: `swiftc -O -o _vidtool _vidtool.swift`.
  - `./_vidtool frames <in> <prefix> <t1,t2,...>` writes JPEG stills
  - `./_vidtool trim <in> <out> <start> <dur> <720|540|1080|high>` writes a trimmed, web-optimised mp4

---

## File / URL structure (decided in Phase 1)

    projects/portfolio/
      index.html                      homepage
      work/layering.html              case studies (flat folder, one file per project)
      work/bw-hands.html
      work/drifting.html
      work/sailing.html
      assets/css/site.css             ALL styling, shared by every page (tokens from apple-DESIGN.md)
      assets/css/_capture.css         dev-only override used for screenshot verification
      assets/js/site.js               reveal observer, video hero, count-up
      assets/js/magnetic.js           OPTIONAL magnetic buttons (Phase 4)
      assets/favicon.svg | favicon-32.png | apple-touch-icon.png | og-image.jpg
      assets/layering/ bwhands/ drifting/ sailing/    web-sized imagery
      video/hero.mp4 (720p), hero-540.mp4 (mobile), hero-poster.jpg
      video/broll.mp4                 unused alternate hero candidate (vertical)
      _cap-*.html, work/_cap-*.html   dev-only screenshot copies, NOT linked from the site
      _vidtool, _vidtool.swift, _*.sh build scripts
      _shots/                         verification screenshots

Styling was moved out of the inline <style> block in index.html into
assets/css/site.css so all five pages share one token system. Do not re-inline it.

---

## Execution checklist

### Phase 1: Case study pages (item 1) — DONE
- [x] Decide URL/file structure — `work/<slug>.html`, flat, shared stylesheet
- [x] Layering case study page — process write-up + 7 images
- [x] BW Hands case study page — write-up + 7 images + eye-strip feature
- [x] Drifting case study page — write-up + 5 stills
- [x] Sailing case study page — write-up + 6 stills
- [x] Homepage tiles link to the real pages (the whole tile is the link now)
- [x] Pulled additional supporting images per project from Drive
- [x] Every page has hero, meta strip (role/medium/output/year), prose, galleries, CTA band, prev/next pager

### Phase 2: Video hero (item 2) — DONE
- [x] Pick default video (decision + reasoning below)
- [x] Download / trim / compress
- [x] Full-bleed looping muted autoplay hero
- [x] Poster fallback + reduced-motion static fallback
- [x] Swap instructions (below, and as a comment in index.html)

Chosen clip: a 14-second section (132s-146s) of `Sailing/repost.mov` — an
unbroken FPV drone chase of a yellow catamaran across the bay.

Why this one, over the candidates this file originally suggested:
- `Drifting/Copy of Copy of Broll.mp4` (the original suggestion) is 1080x1920 vertical. Full-bleed on a desktop hero it crops to roughly 35% of the frame height and upscales horizontally — soft and badly composed.
- The Drifting 4K clips are a talking-head interview in a garage. A static person on camera is a weak hero and ties the whole site to one client.
- The sailing footage is landscape 1920x1080, continuously moving, legible at any crop, and reads as "creative portfolio" rather than one client's ad.

Encoding: `./_vidtool trim assets/raw/sailing-repost.mov video/hero.mp4 132 14 720`
gives 12.0 MB at 720p; the `540` preset gives 7.9 MB. AVFoundation's
`shouldOptimizeForNetworkUse` puts the moov atom first so playback can start
before the file finishes downloading.

HOW TO SWAP THE HERO CLIP: replace `video/hero.mp4`, `video/hero-540.mp4` and
`video/hero-poster.jpg`. Nothing in CSS or JS is clip-specific. To re-cut from a
different source, re-download it from Drive and run `_vidtool trim`. The raw
sources were deleted after use — re-fetch with `_fetch-assets.sh` / `_fetch-video.sh`.

### Phase 3: Grain/film texture overlay (item 3) — DONE
- [x] Subtle film grain: a single fixed `<div class="grain">` using a static inline SVG fractal-noise data URI
- [x] Performance: no per-frame JS. One 160px tile, `steps(1,end)` background-position drift over 8s
- [x] Opacity tuned to .035 with `mix-blend-mode:multiply` (.025 and no animation under reduced motion)

BUG FOUND AND FIXED: the first version used `inset:-50%`, which widens the
document's scrollable area in Chrome and breaks narrow viewports. It is now
`inset:0` and drifts via background-position. Do not reintroduce an oversized
fixed layer.

To remove site-wide: delete the one `<div class="grain">` from each page.

### Phase 4: Magnetic buttons (item 5) — DONE
- [x] Magnetic hover: document-level mousemove; the element travels 32% of the cursor offset, its label lags at 40% of that
- [x] Trivially removable (below)
- [x] Applied to the hero CTAs and the "Get in touch" / "All projects" CTA band on all four case studies
- Automatically disabled under `prefers-reduced-motion` and on coarse/touch pointers.

REMOVAL (2 steps, nothing else depends on it):
  1. Delete the `<script src=".../magnetic.js">` line from the 5 pages.
  2. Optionally drop the `magnetic` class token from the buttons (harmless if left).
site.css and site.js contain zero references to `.magnetic`; the effect's own CSS
is injected by the module itself.

### Phase 5: Favicon + social share (item 8) — DONE
- [x] `assets/favicon.svg` — black field, white "N" split by a horizontal seam (a nod to the torn registration in Layering). Legible at 16px.
- [x] <head> links on all 5 pages (svg, 32px png, apple-touch-icon, theme-color)
- [x] `assets/og-image.jpg` 1200x630, rendered from an HTML card over the hero poster frame
- [x] OG + Twitter meta tags on all 5 pages, with per-page title and description

### Phase 6: Mobile responsive pass (item 9) — DONE
- [x] Hero text sizing — all display type uses `clamp()`; hero runs 34px to 56px
- [x] Tile stacking — the tile row is now a 2-up CSS grid (4 projects = a clean square block) collapsing to 1 column at <=833px. It was `flex 1 1 380px`, which orphaned the 4th tile on its own row.
- [x] Nav on mobile — brand left, links right via `space-between` at <=833px, tighter gaps at <=640px. No hamburger needed for 3 links.
- [x] Scroll-reveal smoothness — opacity/transform only (compositor-friendly); the observer unobserves after firing
- [x] Video hero on mobile — `data-src-small` swaps in the 7.9 MB 540p encode BEFORE load when the viewport is <=833px, `saveData` is set, or effectiveType is 2g/3g. `playsinline` and `muted` are set in markup and again in JS for iOS. If `play()` is rejected the <video> is removed and the poster stands in.
- Breakpoints follow apple-DESIGN.md: 1068 / 833 / 734 / 640 / 480.
- Verified at 1440, 834 and 500 px.

### Phase 7: Numbers strip (item 4) — BUILT, FIGURES ARE PLACEHOLDERS
- [x] Animated count-up strip on the homepage, Apple style, tabular numerals, reduced-motion safe (renders the final value instantly)
- [ ] NEEDS REAL FIGURES FROM NOAH. Currently: 4 projects / 5+ years behind a camera / 3 mediums / 2 cameras in rotation. Edit `data-count`, `data-suffix` and the `.l` label in index.html, or delete the whole `<section class="numbers">` if he doesn't want it.

---

## Open questions for Noah (none of these block anything)
1. Hero clip — the sailing drone shot is a defensible default, not his choice. Easy swap (Phase 2).
2. Numbers strip — real figures, or delete the section.
3. About copy — index.html still carries the placeholder bio ("Short bio goes here...").
4. Drifting client name — deliberately not named. There is a visible note on that page saying client and deliverables need confirming before it goes public; remove it once confirmed.
5. The Lost Spring (day spa) — worth a 5th case study?
6. Contact email — persnalfornak@gmail.com is used in the footer and CTAs. Confirm that is the address he wants public.
7. Magnetic buttons — he flagged these as possibly unwanted. Removal is 1 line per page.

## Verification method (reuse this)
Chrome's LEGACY `--headless` clamps the viewport to a 500px minimum and silently
crops narrower screenshots. It looks exactly like a CSS overflow bug but is not —
this cost real time today. Use `--headless=new`. Also pass
`--force-prefers-reduced-motion` so scroll-reveal elements are not captured at
opacity 0, and load the `_cap-*.html` copies (they link `_capture.css`, which
collapses the 100vh sections so a tall viewport captures a whole page in one shot):

    "/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
      --headless=new --disable-gpu --hide-scrollbars --force-prefers-reduced-motion \
      --virtual-time-budget=8000 --screenshot=out.png --window-size=1440,5200 \
      "http://localhost:8950/_cap-index.html"

Regenerate a `_cap-*.html` after editing its real page — it is a copy, not a view.
Preview server: `node projects/portfolio/preview-server.js` on port 8950.

## Status log
- 2026-10-07 17:24 EDT: Checklist created. Starting Phase 1.
- 2026-10-07 17:30 EDT: Pulled 20 stills and 3 videos from Drive. Found no ffmpeg; built `_vidtool` (Swift/AVFoundation) rather than installing anything.
- 2026-10-07 17:35 EDT: Inspected every asset. Rewrote all four project descriptions — the originals were wrong (see corrections table). Fixed the 90-degree rotation on the Layering scans.
- 2026-10-07 17:38 EDT: Extracted CSS to assets/css/site.css. Rebuilt index.html with the video hero, grain, 2x2 tiles and numbers strip. Wrote all four case-study pages.
- 2026-10-07 17:40 EDT: Favicon and OG card generated. Found and fixed the grain-overlay document-width bug.
- 2026-10-07 17:43 EDT: Established the headless-Chrome verification recipe (500px clamp, reduced motion). Verified all pages at 1440 / 834 / 500.
- 2026-10-07 17:45 EDT: Fixed the tile grid (3+1 to 2x2), gallery row heights, portrait-artwork presentation, and a duplicate frame on Drifting. Added CTA bands to all case studies.
- 2026-10-07 17:46 EDT: Trashed 510 MB of raw downloads and frames (re-fetchable via the _*.sh scripts). Project is now 121 MB.
- PHASES 1-6 COMPLETE. Phase 7 built but needs real numbers. Everything still open is content Noah has to supply (see Open questions), not engineering.


## Round 2 — Noah's feedback (2026-10-07 18:21 EDT) — ALL DONE
- [x] Magnetic buttons — PULL 0.32 -> 0.07, travel hard-capped at 4px, hover field
      shrunk from 90px to 10px past the box, and activation is now exclusive (one
      button at a time, all others reset on the same frame). The overlap was a
      feedback loop: the transformed box was being re-measured, so an active
      button chased the cursor. It now measures the untransformed box.
- [x] Drifting -> the client is **Wide Span Sheds**, a shed builder. NOT a driving
      academy. Page retitled, slug left as drifting.html so existing links keep
      working. Tile, pagers, meta tags and body copy all rewritten.
      - The drift academy (D-Spec Racing / Drift Academy International, "Global
        Headquarters" sign on the shed) is the CUSTOMER in one of the films, not
        the client. That is where the earlier mistake came from.
      - The second film is a rural lifestyle-block shed built for a boat.
      - Films are customer stories; the Wide Span Sheds brand only appears on the
        end card.
- [x] The Lost Spring — the Drive folder HAS content now (7 files). Built as the
      5th case study: work/lost-spring.html + assets/lostspring/ (8 stills).
      It is a day spa: mineral pools, subtropical planting, swing bridge, cafe,
      red-roofed pavilions. The films are continuous one-take laps of the property
      ending on the TLS logo. Added to the homepage grid and the pager loop.
- [x] Hero video — REPLACED. The old one was raw GoPro sailing footage: flat,
      uncorrected fisheye, visibly warped horizon. Exactly what Noah objected to.
      New hero is 8s from The Lost Spring pool lap (tls-pool2.mov, 15s-23s):
      colour graded, gimbal-smooth, and a single continuous move with no cuts.
      - Verified stability by sampling frames 0.5s apart: level horizon, constant
        speed, consistent exposure.
      - NOT taken from Wide Span Sheds despite Noah pointing at it: those films
        are cut at 2-3s per shot, so nothing in them is long enough to loop.
        Confirmed by dense frame sampling of the main cut and both shorts.
        If Noah wants a drift-car hero anyway, the best single shot is
        sky-main-4k.mp4 15.0-17.4s (head-on telephoto, car sliding in smoke) —
        it is just under 2.5s.
- [x] persnalfornak@gmail.com still public in every footer and CTA band. Untouched.
- [x] Stats strip — real figures, 3 stats not 4: 2.5 years photography /
      1.5 years video / 2 years photography classes. No project count invented.
      site.js count-up gained `data-decimals` support, otherwise Math.round
      displayed 2.5 as "3".

### Round 2 incidental fixes
- Tile grid: 5 projects orphaned the last tile in the 2-up grid with dead white
  space beside it. Added `.tile-row > :last-child:nth-child(odd){grid-column:1/-1}`
  so an odd final tile spans full width.
- Lost Spring tile switched from `light` to `dark-2`; dark ink over the mid-tone
  pool image failed contrast.
- Layering / BW Hands tile subtitles corrected on the homepage to match what the
  case studies actually say (torn-paper photomontage / B&W double-exposure).

### Round 2 notes
- `_vidtool trim` will NOT overwrite an existing output file — it silently leaves
  the old one in place. `rm -f` the target first or you will verify a stale file.
- AVAssetExportSession presets are quality-based with no bitrate control, and
  dense moving foliage compresses badly: the hero is 11.4 MB at 720p / 7.4 MB at
  540p for 8s. Comparable to the 12 MB clip the site already shipped, but a real
  encoder would do far better. Revisit if ffmpeg is ever approved.

## Still open for Noah (unchanged from round 1)
- About copy — index.html still carries the placeholder bio ("Short bio goes here...").
- Magnetic buttons — still removable in 1 line per page if he dislikes the subtler version.

## Status log
- 2026-10-07 18:2x EDT: Round 2 feedback received, items above added. Starting round 2 fixes.
- 2026-10-07 19:20 EDT: Round 2 complete. Re-downloaded the Drifting + Lost Spring
  sources, identified the real client from the footage, rebuilt the hero, added the
  5th case study, fixed the tile grid. Verified all pages at 1440 with headless
  Chrome (recipe above).


## Round 3 — Noah's feedback, part 1 (2026-10-07 21:22 EDT) — MORE COMING IN NEXT MESSAGE, do not start until part 2 arrives
- [ ] Hero video wrong pick — Noah wants the Lost Spring clip that goes UNDER THE SWING BRIDGE specifically, not whichever clip round 2 used. Find/use that one.
- [ ] Add breathing room (vertical spacing/padding) between sections — specifically between the hero video and the tile row (Layering / BW Hands / Wide Span Sheds / Sailing). Likely applies between other sections too — use judgement but this one named explicitly.
- [ ] Layering tile image: (a) recenter/reposition so the white paper edges/background aren't visible at the sides of the crop, (b) convert to black & white to match the other tiles (currently in color, should be B&W like BW Hands etc).
- [ ] Lost Spring section (just below the hero): change the background image to something different (current one not specified as bad, just "choose a different image").
- [ ] Re-center the stats text block under the Lost Spring section (2.5yr photography / 1.5yr video / 2yr classes) — currently misaligned.
- [ ] Sailing tile background photo needs to change — current one "doesn't represent it well," pick a stronger shot from the Sailing footage/stills.
- [ ] EVERY case study page (Layering, BW Hands, Wide Span Sheds, Sailing, Lost Spring) needs a full photo gallery showing ALL his photos for that project, with individual fullscreen/lightbox viewing — this is a significant feature to build across all 5 case studies.
- [ ] Noah has an award-winning photo — finalist, National Youth Art Awards New Zealand. Need him to identify/send which photo this is. It should go on the MAIN page in the Wide Span Sheds tile/subheading area specifically, replacing the 3rd (rightmost) photo currently there. 1st and 2nd Wide Span Sheds photos are good, keep those.
- [ ] Wide Span Sheds 3rd photo slot: Noah wants it changed, "potentially a video" instead of a photo.
- About bio: still not provided by Noah — no placeholder swap yet, waiting on him.

## Status log
- 2026-10-07 21:2x EDT: Round 3 feedback part 1 logged above. WAITING for part 2 (Noah said "more instructions in next message") before dispatching a build subagent. Do not start Round 3 work until both parts are captured.


## Round 3 — Noah's feedback, part 2 (2026-10-07 21:24 EDT) — now complete, both parts received, OK TO START
Part 2 is entirely about the Sailing section:
- [ ] Sailing section needs a new cover photo (confirmed again from part 1)
- [ ] ALL images underneath/within the Sailing section/case-study need to be replaced — there are duplicate photos currently (same image used twice) and the rest are weak choices. Re-select with more care/consideration — look at the full Sailing folder contents in Drive, don't just grab the first few files.

## Round 3 — FULL COMBINED LIST TO EXECUTE (both parts merged, start now)
1. [x] Hero video: swap to the Lost Spring clip that goes UNDER THE SWING BRIDGE. DONE.
     Found it by frame-scanning all three Lost Spring clips. The bridge is in
     BOTH pool laps, but only `Tls pool lap 1.mov` actually passes UNDERNEATH it:
       - lap1: approach from 15.0s, bridge deck sweeps overhead 18.2-19.8s, clear by 20.5s.
       - lap2: crosses the same bridge ~17.0-17.8s but laterally/at a distance — it
         goes PAST, not under. Round 2 used lap2, which is why it was wrong.
     New hero = lap1 15.0s +8.0s, so the pass-under lands ~3.5s into the loop.
       `./_vidtool trim assets/raw/tls-lap1.mov video/hero.mp4 15.0 8.0 720` (11.3 MB)
       `./_vidtool trim assets/raw/tls-lap1.mov video/hero-540.mp4 15.0 8.0 540` (7.4 MB)
       poster = frame @17.0s (bridge spanning the pool), resized to 1600px.
2. [x] Breathing room between hero and tile row. DONE (site.css).
     `.hero-video + .tile-row { margin-top:var(--sp-section) }` — 80px of canvas so the
     two full-bleed blocks stop fusing into one slab. Also added the same pause
     between the tile row and the numbers strip. Scales to --sp-xxl <=833px and
     --sp-xl <=480px so phones do not get a dead screenful of white.
3. [x] Layering tile: recentred + converted to B&W. DONE.
     The old tile used `layering/hero.jpg`, which is a photo of the print still
     sitting on its white mount — hence pale paper bands down both sides of the
     crop — and it carried a blue/cyan cast while every other tile is neutral B&W.
     New file `assets/layering/tile-bw.jpg`: cropped inside the mount
     (`sips -c 1760 1180`, 1180x1760) then converted with the Generic Gray
     Gamma 2.2 ICC profile. Verified side-by-side in a tile-sized crop — no
     white edges, neutral greyscale. The Layering CASE STUDY hero now uses the
     same file, since it crops even wider and had the same problem.
4. [x] Lost Spring tile background changed. DONE.
     This is the full-width 5th tile directly under the hero. It was
     `lostspring/hero.jpg` — another green-pool frame, so it simply repeated the
     hero video above it. Now `assets/lostspring/tile.jpg`, frame @43s of
     "Tls pool lap 1.mov": the red-roofed pavilions above the pool, with the
     pink umbrellas and planting. Colourful, clearly reads "day spa", and shares
     no subject matter with the hero clip.
5. [x] Re-centre the stats strip. DONE — and the cause was a real bug, not a nudge.
     `.numbers .inner` was `grid-template-columns:repeat(4,1fr)` but the strip only
     holds THREE stats since round 2 dropped the invented project count. The three
     items packed into columns 1-3 leaving a dead 4th column, so the whole block
     sat visibly left of centre. Now `repeat(auto-fit,minmax(180px,1fr))`, which
     tracks the real child count and stays centred if a 4th stat is ever added.
6. [x] Sailing fully recut: new cover + all case-study images replaced. DONE.
     CONFIRMED: the Drive Sailing folder still holds exactly ONE file,
     `repost.mov` (116.6 MB, 159.6s, 1920x1080, 24fps). There are no stills in
     Drive, so every image is a frame extracted from that clip, as instructed.
     Method: scanned all 159s at 2s intervals (80 frames) as a contact sheet,
     shortlisted 18, reviewed those at full resolution, then cut 13 finals.
     Frame timecodes (so this is reproducible without re-reviewing):
       cover.jpg 76s · yellow-big 124s · orange-hike 90s · orange-crew 110s
       orange-close 92s · overhead 100s · rainbow-pair 150s · rainbow-crew 152s
       headland 98s · yellow-headland 120s · fleet 134s · establish 56s
       dark-hull 40s
     Why the old set was weak: it was drawn almost entirely from the first 30s,
     which is distant grey-water material, and the old `hero.jpg` and
     `chase.jpg` were near-identical frames — that was Noah's duplicate.
     ALSO FOUND IN THE FOOTAGE: the title card names the event —
     "Double Trouble Multihull Regatta, 29-30 Nov 2025, Whitianga". That is now
     in the page's meta strip and body copy. (Whitianga is also where The Lost
     Spring is, which is a nice accident.)
     FIXED STALE COPY: the page still claimed the site's hero video was cut from
     this footage. That stopped being true in round 2. Rewritten.
7. [x] Lightbox galleries on every case study. DONE — and on the homepage too.
     New component `assets/js/lightbox.js` (~12 KB, vanilla, zero dependencies)
     plus a LIGHTBOX block in site.css. Markup contract: put `data-lightbox="Name"`
     on any container; every <img> inside becomes a trigger. Containers sharing a
     name form ONE album, so prose can sit between gallery rows and the viewer
     still pages through the whole project in document order.
     Features: fullscreen overlay, prev/next, keyboard (Esc / arrows / Home /
     End), focus trap + focus restore, aria-modal, touch swipe to page and
     swipe-down to dismiss, neighbour preloading, loading pip, caption from
     figcaption-or-alt, scroll lock. Every transition is behind
     prefers-reduced-motion: no-preference.
     DEDUPE: the editorial rows deliberately re-show a few of a project's best
     photographs that also appear in the full grid. The first build counted
     those twice (Sailing read "8 / 18" for 13 photos). collect() now keys each
     album by src, so a repeated file maps to the first entry's index instead of
     appending. Verified per page:
       Layering 25 tags -> 16 unique · BW Hands 16 -> 9 (+1 eye strip album)
       Wide Span Sheds 24 -> 20 · Sailing 18 -> 13 · Lost Spring 24 -> 17
       Homepage Snapshots 6 -> 6
     MORE IMAGES PULLED FROM DRIVE, as asked:
       Layering  12 -> 16 (added P1017377 + the in-camera multiple exposures
                 "lochie jumping" and "Maxrowing", plus "Hayden bunched").
                 NOTE: those three are NOT torn-paper work — they are multiple
                 exposure motion studies (box jump, rowing ergo). They are in
                 the layering Drive folder because they are layered in-camera.
                 Added a paragraph to the case study explaining that second
                 strand rather than passing them off as collage.
                 P1017377 was stored 90deg rotated like the rest of that batch;
                 `sips -r 270` applied.
       Wide Span Sheds 10 -> 20 (graded 4K frames: drift car head-on and
                 mid-slide, the aerial donut, the black shed exterior and doors,
                 four aerials including one with the client branding)
       Lost Spring 8 -> 17 (added the swing bridge and the under-bridge pass,
                 cabana, pool-wide, bush, umbrellas, roof-view, finial)
       BW Hands  unchanged at 9 — the Drive folder holds exactly 8 files and all
                 of them were already on the page, so that grid is genuinely complete.
     Verified by driving a real bubbling click in headless Chrome and dumping the
     DOM; the viewer opens with the right album, count and caption on all 6 pages.
8. [x] Wide Span Sheds 3rd slot changed — and it IS a video now. DONE.
     Slots 1 and 2 (shop-a, site-b) untouched per Noah. Slot 3 was `site-c.jpg`,
     a flat late-afternoon exterior; it is now a 5s muted autoplay loop,
     `video/wss-shed.mp4` (2.9 MB, 540p) + `video/wss-shed-poster.jpg`.
     WHICH CLIP AND WHY NOT A DRIFT CAR: the racing material is cut at roughly
     0.3-0.7s per shot — verified by sampling 5.5-11.0s at 0.35s intervals — so
     nothing in it survives looping. The black-shed exterior at 24.4-29.4s of
     "Copy of Copy of Sky Short 1 CG 4k.mp4" is the one genuinely continuous,
     smoothly moving, evenly exposed span in the clip, and the flagship building
     is the right subject for a shed builder anyway.
     Plumbing: new `[data-loop-video]` handler in site.js — muted/playsInline
     forced for iOS, plays only while on screen via IntersectionObserver, and
     under prefers-reduced-motion it is left PAUSED so the poster frame stands
     in and the cell still reads as a photograph. CSS matches the image cells
     exactly so the row keeps one baseline.
     If Noah would rather have a still here, the best single frame is
     `assets/drifting/drift-head-on.jpg` (@6.2s, the liveried BMW head-on).
     NOTE ON LOCATION: Noah said "main homepage", but the homepage has no Wide
     Span Sheds photo row — each homepage tile is a single background image.
     The only 3-up Wide Span Sheds photo row on the site is the one on the case
     study page, so that is what was changed. Worth confirming with him.
9. [!] STILL BLOCKED ON NOAH — award photo. No guess was made, as instructed.
     Noah has a National Youth Art Awards (New Zealand) FINALIST photograph that
     he wants in the Wide Span Sheds 3rd slot. He has not sent or named the file,
     and nothing in Drive identifies it, so it has not been chosen for him.
     A loud dev comment marked ">>> TODO - BLOCKED ON NOAH <<<" sits directly
     above that slot in work/drifting.html with swap instructions.
     TO SWAP WHEN HE SENDS IT: delete the <video> in that row, put the photo back
     as a plain <img> (the row already carries data-lightbox="Wide Span Sheds",
     so it will open full screen automatically), and caption it with the award.
10. [—] About bio — no action taken, as instructed. index.html still carries
     "Short bio goes here...". Waiting on Noah's real text.

Noah said his laptop stays on overnight and expects this to run long, with possible token/context resets — keep this TODO.md fully current after every item so a fresh session can resume instantly.

## Status log
- 2026-10-07 21:2x EDT: Round 3 fully logged (both parts). Dispatching overnight build subagent now.


---

## Round 3 — COMPLETE (2026-10-08 00:25 EDT)

8 of 10 items done. Item 9 is blocked on Noah (award photo — deliberately not
guessed). Item 10 is waiting on Noah's bio text. Nothing else is outstanding.

### New files this round
    assets/js/lightbox.js            the reusable fullscreen viewer
    assets/layering/tile-bw.jpg      cropped + neutral-greyscale Layering cover
    assets/layering/p7377.jpg        + lochie-jump, max-rowing, hayden-bunched
    assets/lostspring/tile.jpg       + bridge, under-bridge, cabana, pool-wide,
                                     bush, umbrellas, roof-view, finial
    assets/sailing/*.jpg             all 13 replaced (old set deleted)
    assets/drifting/*.jpg            10 new graded-4K frames added to the 10 existing
    video/hero.mp4 / hero-540.mp4 / hero-poster.jpg   recut: swing-bridge pass
    video/wss-shed.mp4 / wss-shed-poster.jpg          5s loop for the 3rd slot

### Changed
    assets/css/site.css   LIGHTBOX block, FULL PROJECT GALLERY block, inline
                          gallery video block, hero/tile breathing room, the
                          .numbers auto-fit centring fix
    assets/js/site.js     [data-loop-video] handler (reduced-motion safe)
    index.html + all 5 case studies: lightbox wired, galleries added, tiles swapped

### Things a fresh session should know
- `_vidtool` gained an `info` mode: `./_vidtool info <file>` prints duration,
  dimensions and fps. The arg-count guard was relaxed from 4 to 3 to allow it.
- AVAssetImageGenerator is unreliable on the 4K 59.94fps files — `tls-reredo.mp4`
  returned zero frames across several attempts, and dense runs on the 4K Wide Span
  Sheds clip silently drop 2-3 frames out of 16. It is fine on all the 1080p
  sources. Re-run and check counts rather than trusting one pass.
- Review workflow that made the curation affordable: extract frames, downsize with
  `sips -Z 300`, generate a contact-sheet HTML, screenshot it with headless Chrome,
  and read ONE image instead of dozens. Worth reusing.
- Headless Chrome frequently fails on its FIRST invocation after a page is written
  and succeeds on an immediate retry. Not a page bug — just retry once.
- The exec preflight refuses chained interpreter commands
  (`cd x && python3 y.py`). Use the `workdir` argument, or build the artefact in
  JavaScript instead of shelling out. The contact sheets are generated in JS now.
- To functionally test the lightbox without a browser session: copy a page, inject
  a script that dispatches a real bubbling `MouseEvent('click')` on an `img.lb-trigger`
  and writes the result into a `#LBDIAG` div, then read it back with
  `--headless=new --dump-dom`. Much cheaper than screenshots. (Those `_diag-*`
  harnesses were deleted after use — recreate as needed.)
- `assets/raw/` was deleted again after use (was 936 MB). Everything is
  re-fetchable; frame timecodes for every extracted still are recorded above.
  Project is back to 154 MB.

### Verified
- Homepage at 1440: hero/tile breathing room present, Layering tile B&W with no
  white edges, Sailing and Lost Spring tiles swapped, stats strip centred.
- Sailing and Wide Span Sheds case studies captured full-page — no broken images,
  the video cell sits on the same baseline as the photos beside it.
- Lightbox opens with the correct album, count and caption on all 6 pages.
- Asset-reference audit across all 6 pages: no missing files.

### Open for Noah (unchanged, nothing blocking)
- Award photo for the Wide Span Sheds 3rd slot (item 9).
- About bio copy (item 10).
- Whether "Wide Span Sheds 3rd photo on the MAIN page" really meant the case
  study row — see the note in item 8.
- Magnetic buttons still removable in one line per page.

## Status log
- 2026-10-07 21:25 EDT: Round 3 started.
- 2026-10-08 00:25 EDT: Round 3 complete. Items 1-8 done and verified, 9 blocked
  on Noah with an in-page TODO marker, 10 untouched per instruction.


## Round 4 — Noah's feedback (2026-10-08 10:59 EDT)
- [ ] Hero video: use REREDO.mp4 (Drive file id 12Iyee1kgzJZZkWHzu5N-V55RZ9g4pGbD or duplicate 1sVfUdXPoFa_ZJ2xa5PxYmzTnxrIxEmY9, 470MB), trimmed from 00:24 to 00:37 (13 second clip). This replaces whatever hero clip round 3 used. Download, trim with ffmpeg if available (check again — wasn't installed as of round 1, may need another approach: AVFoundation/sips won't trim video, check for ffmpeg via brew or use a different tool), compress for web.
- [ ] NYAA award photo RESOLVED: it's BW Hands/_MG_3086.jpg (already downloaded locally as projects/portfolio/assets/bw-hands-2.jpg from round 1 — verify it's the exact right file, re-download from Drive id 1teR9W2HW_9I5m-xt6ph6ARPLVSZrdXWw to be safe).
- [ ] Build a new "featured/award" section positioned RIGHT BELOW THE HERO VIDEO (before the tile row) — showcase the _MG_3086.jpg image together with the artist statement text (full text, already exported: "Artist Statement\n\nThis photograph explores the internal and external layers of identity and can reveal the contrast between how a person feels within themselves and how they present to the world. Through this visual symbolism, the image communicates hidden emotions, values, memories, and social expectations, showing identity as complex, evolving, and deeply personal."). Consider mentioning it's a National Youth Art Awards (NZ) finalist entry — Noah hasn't explicitly said to label it as such on the page, but it's reasonable context; use judgement, could ask him to confirm exact wording if unsure, but don't block the whole feature on that.
- [ ] This NEW placement (below hero) likely supersedes the earlier Wide Span Sheds 3rd-slot question — Noah has not explicitly cancelled that instruction though. Leave the Wide Span Sheds 3rd slot as whatever round 3 already set it to (the steady exterior glide video), since Noah's latest message only talks about placement "below the hero shot" — do not duplicate the award photo in two places. If Noah wants it removed from Wide Span Sheds page instructions/TODO notes, he'll need to confirm — leave the in-page TODO marker about the award photo as resolved/removed once this section is built, note in TODO.md that placement was reassigned per his latest instruction.

## Status log
- 2026-10-08 ~11:00 EDT: Round 4 feedback logged. Dispatching subagent.


## URGENT CORRECTION to Round 4 (2026-10-08 14:23 EDT) — DONE 2026-10-08 20:14
Noah corrected himself: the NYAA award photo is **_MG_3085.jpg**, NOT _MG_3086.jpg as stated earlier. If the round 4 work already used _MG_3086.jpg, SWAP IT to _MG_3085.jpg (Drive id 1Ve5BBigZjfI5M_3NnSz-krw3OQLk5_KD, in the BW Hands folder). Check whatever section/placement round 4 built and correct the image reference.

## Round 5 — Noah's feedback (2026-10-08 14:23 EDT)
- [x] Add a video clip to the Sailing section: repost.mov trimmed from 2:11 to 2:18 (7 seconds). Drive id for repost.mov: 1iT0ETDOQQrTWa29DdJ2poVsYl7aFCGSS (116.6MB source). Add this as part of the Sailing case study (gallery or dedicated video moment — use judgement on exact placement within the existing Sailing page layout).
- [x] Noah says there ARE more Sailing photos somewhere he wants used — "go have a look." Re-search Drive thoroughly beyond the single "Sailing" folder (id 1_R2tp6g_RvBDvwzb-a6Jky1zOqhnOxmu) — check his whole Drive for sailing/regatta-related images (search terms like "Capture", "sailing", "regatta", "Whitianga", date ranges near repost.mov's timestamp etc). Capture 7299 - Copy.JPG (below) was found outside the Sailing folder, in the general Drive — there may be a cluster of similarly-named "Capture NNNN" files nearby worth checking (same folder/date).
- [x] Sailing HOMEPAGE cover/tile photo: use "Capture 7299 - Copy.JPG" (Drive id 1O1AivetrYh0kcQIC_2s-VmrzsJWDHPMA, 93.7KB, dated 2023-07-18 — note this predates repost.mov and other sailing content by ~2.5 years, may be from a different/earlier regatta — that's fine, just confirming it's the right file). Replace whatever round 3 used for the Sailing homepage tile with this image specifically.

## Status log
- 2026-10-08 14:2x EDT: Urgent image correction + round 5 items logged. Round 4 subagent still running (started with wrong filename _MG_3086 — sent it a live correction, also documenting here for safety in case message doesn't land before it finishes).


## Round 4 status: SUBAGENT CRASHED (2026-10-08 20:01 EDT)
Checked session history of the round 4 subagent (agent:main:subagent:2120c218-f839-44f4-9ed7-5212ce814520, runId c0a658fa-6d94-4ec7-8a2f-0cf249e8bebe) — it errored out (outcome=error, no final report) while mid-way through visually verifying the award section. The live correction message (_MG_3086 -> _MG_3085) was delivered but shows as "interrupted" — it landed right as the run was ending, so it's UNCONFIRMED whether the correction was ever applied.

Confirmed from reading index.html directly:
- Hero video (REREDO.mp4, trimmed ~24s-36.37s to avoid a logo flash glitch at 12.4s) — DONE, well documented in HTML comments, looks genuinely complete (video/hero.mp4 + hero-540.mp4 variants exist).
- Award section — EXISTS but still references _MG_3086.jpg / assets/award/mg3086.jpg in both the <img> src and the HTML comment above it ("Source file: BW Hands/_MG_3086.jpg"). THE CORRECTION TO _MG_3085 WAS NOT APPLIED. This is the first thing the next session must fix: re-download _MG_3085.jpg (Drive id 1Ve5BBigZjfI5M_3NnSz-krw3OQLk5_KD), regenerate assets/award/mg3085.jpg + mg3085-full.jpg (matching whatever sizing mg3086 used, 940x1500 display / 1380x2200 full), update the <img src>, data-full, and alt text, update the HTML comment, rename/clean up old mg3086 asset files.
- Tile row currently still has round 3's sailing cover (assets/sailing/cover.jpg, the rainbow-sail photo at ~76s of repost.mov) — round 5's "Capture 7299 - Copy.JPG" swap has NOT been applied yet.

## NEXT SESSION: do all of this in one pass
1. Fix award section: swap _MG_3086 -> _MG_3085 everywhere (image files, src/data-full attributes, alt text, HTML comment). Verify visually.
2. Round 5 item: Sailing homepage tile cover photo -> Capture 7299 - Copy.JPG (Drive id 1O1AivetrYh0kcQIC_2s-VmrzsJWDHPMA).
3. Round 5 item: add repost.mov clip trimmed 2:11-2:18 (7s) to the Sailing case study page (Drive id for repost.mov: 1iT0ETDOQQrTWa29DdJ2poVsYl7aFCGSS).
4. Round 5 item: search Drive more broadly for additional sailing/regatta photos beyond the single Sailing folder (Noah says there are more somewhere) — look near Capture 7299's location/timeframe (2023-07-18, "Capture NNNN" naming pattern) for a cluster of related files, and incorporate good ones into the Sailing case study gallery.
5. Verify EVERYTHING visually at the end (homepage + all 5 case studies) since multiple rounds have stacked changes — do a full regression pass, not just the new items.

## Status log
- 2026-10-08 20:01 EDT: Discovered round 4 subagent crashed before finishing the image correction. Documented exact state above. Dispatching a fresh session to finish round 4's correction + all of round 5 in one pass.


## Round 6 — Noah's feedback (2026-10-08 20:09 EDT) — QUEUE FOR AFTER round4fix+round5 finishes
- [ ] Lost Spring case study: replace photo snapshots/stills with actual PLAYABLE VIDEOS. Noah says there are only ~6 videos total for this project — use all of them as embedded playable video elements instead of static frame-grab stills.
- [ ] Wide Span Sheds case study: same idea — should be MOSTLY videos, not photos. Noah does want to keep some highlight PHOTOS too (explicitly mentioned: a shot of the car drifting around the track — he thinks it's cool, keep that kind of thing), but the bulk of the gallery should be actual playable videos of the work, not stills.
- [ ] (Reconfirmed, already in progress via round4fix+round5) Sailing cover photo -> Capture 7299 - Copy.JPG
- [ ] (Reconfirmed, already in progress via round4fix+round5) NYAA award photo -> _MG_3085.jpg correction (he confirmed again it was the wrong one)

## Status log
- 2026-10-08 20:09 EDT: Round 6 items logged (Lost Spring + Wide Span Sheds: swap stills for real playable videos). The round4fix+round5 session is still running — do NOT start round 6 until that one reports back; append it as a new dispatch afterward.


## Future feature request (2026-10-08 20:12 EDT) — NOT a portfolio content item, do after rounds settle
Noah wants a visual kanban-style to-do board he can use himself: columns like "To start", "In progress", "Waiting for approval", "Finished", with drag/reorder, viewable locally in his browser (he referenced "127.0.0.1" — a small local web app, same pattern as the preview-server.js approach). Build this AFTER round 6 is done, as a standalone tool separate from this portfolio site (could live in projects/kanban/ or similar, own tiny node server). Not urgent, no deadline given.

## Status log
- 2026-10-08 20:12 EDT: Logged future kanban board request for after round 6.


---

## Round 5 — COMPLETE (2026-10-08 20:15 EDT)

All five items from the "NEXT SESSION: do all of this in one pass" list are
done, including the award correction the crashed Round 4 session never applied.

### 1. Award photo correction — DONE
- Downloaded _MG_3085.jpg (Drive id 1Ve5BBigZjfI5M_3NnSz-krw3OQLk5_KD),
  3432x5472 original.
- Visually compared 3085 against 3086 side by side before swapping: 3085 is the
  **denser** variant — hands ghosted on BOTH sides of the face plus a third
  overlay. 3086 is the simpler single overlay. They are genuinely different
  frames, so the correction mattered.
- Generated assets/award/mg3085.jpg (940x1500) and mg3085-full.jpg (1380x2200),
  exactly matching the sizing the 3086 pair used.
- index.html: <img src>, data-full, alt text and the whole HTML comment block
  updated. The comment now records the correction so nobody reverts it.
- assets/award/mg3086.jpg and mg3086-full.jpg DELETED.
- Note: assets/bwhands/3086.jpg still exists and is still referenced twice in
  work/bw-hands.html. That is CORRECT and unrelated — it is a frame in the
  BW Hands series gallery, not the award section. Do not delete it.

### 2. Sailing homepage tile — DONE
- assets/sailing/tile.jpg = "Capture 7299 - Copy.JPG"
  (Drive id 1O1AivetrYh0kcQIC_2s-VmrzsJWDHPMA), 1324x680.
- Backlit keelboat against the Whitianga headland, glittering water.
- background-position set to center 60% so the hull and crew clear the caption
  scrim at tile size.
- Round 3's assets/sailing/cover.jpg is UNCHANGED and still used for the
  case-study hero and in the "Every frame" grid. Noah only asked for the
  homepage tile.

### 3. Sailing video moment — DONE
- video/sail-moment.mp4 — repost.mov 2:11 to 2:18 exactly (start 131s, 7.000s).
- repost.mov did NOT need the AV1 workaround: it is plain 1920x1080 24fps
  H.264, 159.625s. Both tools work on it.
  * ./_vidtrim repost.mov out 131 7 720  -> worked, 5.80 MB
  * ./_vidxcode repost.mov out 131 7 1280 720 2600 1 -> worked, 2.29 MB
  Shipped the _vidxcode encode (same 1280x720 24fps, 60% smaller).
- Poster video/sail-moment-poster.jpg is the clip's own first frame (131.2s).
- Content: drone chases a yellow Hobie across the bay, rainbow-sailed boat
  crossing behind, horizon rolling hard.
- Placed as a <figure class="figure-full"> immediately after the "Flying it"
  bullet list in work/sailing.html — it is literally the proof of the
  "chase, do not orbit" and "let the horizon tilt" claims in that list.
- Uses the existing data-loop-video handler, so it is reduced-motion safe
  (stays paused, poster shows) and only decodes while on screen.
- Added one CSS rule: `.figure-full video { width:100%; height:auto; }`
  so a video band matches a photo band. Nothing else in the CSS changed.

### 4. The missing sailing photos — FOUND
- They were NOT elsewhere in Drive. **Noah uploaded 22 new "Capture NNNN.JPG"
  files straight INTO the Sailing folder (1_R2tp6g_RvBDvwzb-a6Jky1zOqhnOxmu)
  at 2026-10-08T18:19Z** — i.e. after Round 3 read that folder and found only
  repost.mov, and at roughly the same time he sent the Round 5 message.
  If a future round "cannot find" files Noah insists exist, re-list the obvious
  folder first; he adds things mid-conversation.
- Drive-wide search syntax that works:
  `gog drive ls -a <acct> --json --all --query "name contains 'Capture'" --max 200 --fields 'files(id,name,parents,createdTime)'`
  **--all is required** — without it --query only searches the root folder and
  silently returns an empty list. That is almost certainly why earlier rounds
  concluded there was nothing there.
- All 22 are 1324x680 JPEGs, ~50-140 KB, dated 2023-07-18 — an EARLIER,
  surface-level/long-lens shoot, two years before the 2025 drone footage.
  7468 and 7479 each exist twice as literal duplicate pairs.
- Reviewed all 22 as one contact sheet (thumbs + grid HTML + one headless
  screenshot = one image read instead of 22). Selected 9:
      reg-mist       7276   lone yacht in flat haze
      reg-headland   7299   the tile frame, backlit against the headland
      reg-pinnacle   7519   crew working, rock pinnacle behind
      reg-red-kite   7435   red spinnaker against blue
      reg-hoist      7612   close on the bow, spinnaker going up
      reg-class56    7709   boat 56 close aboard, crew on the rail
      reg-rail       7785   three crew hiked out, boat heeling
      reg-mark       7773   mark rounding, overlapped boats
      reg-pair       7832   two boats running, one under a red kite
- Added to work/sailing.html as a **separate** section, "Before the drone",
  with its own short intro and three cols-3 rows, placed after "Every frame"
  and before the CTA band. Kept separate deliberately: "Every frame" is
  explicitly thirteen stills pulled from the 2025 drone footage, and folding
  2023 surface-level photos into it would make that copy false. Same
  data-lightbox="Sailing" album, so the viewer still pages the whole page.

### 5. Full regression pass — DONE
Captured all six pages full-page at 1440 through the _cap-*.html harness
(regenerated from the current source — the old ones were stale) and read them
as 4-6 slice review sheets. Shots in _shots/r5/.
- index — hero, award section showing the CORRECT 3085 frame, five tiles with
  the new Sailing tile, stats, about, snapshots, footer. Clean.
- sailing — hero, meta, idea/flying it, new video moment, editorial rows,
  overheads, Every frame, Before the drone 3x3, CTA, pager. Clean.
- layering, bw-hands, drifting (Wide Span Sheds), lost-spring — no regressions,
  no blank or broken cells, layout intact.
- Asset-reference audit across all six pages: 162 references checked, **zero
  missing files**. The only misses were `index.html#work`-style anchors, which
  are not files.
- grep for "3086" across html/css/js: only the two deliberate mentions inside
  the new award HTML comment, plus the unrelated and correct
  assets/bwhands/3086.jpg in bw-hands.html.

### Housekeeping
- assets/raw/ deleted again (was 616 MB — reredo.mp4, repost.mov, the award
  source and the 22 captures). Everything is re-fetchable; every Drive id and
  every timecode is recorded in this file.
- Removed stale harnesses _cap-award.html, _cap-r4.html, _cap-r4t.html and the
  unreferenced video/broll.mp4 (verified unreferenced before deleting).
- Regenerated _cap-index.html and work/_cap-*.html from current source.
- Project is 194 MB.

### Lessons for the next session
- `gog drive ls --query` needs `--all` or it only looks in root. This has
  probably cost two earlier rounds.
- Long chained `gog drive download` commands get cut off partway through
  (silently — the exec returns no output and only some files land). Download in
  parallel batches of ~4 separate execs and then verify the file count.
- Headless Chrome still fails on its first invocation and works on the retry.
  Run the capture loop SEQUENTIALLY, not with Promise.all, and re-check
  timestamps before trusting that a screenshot was regenerated.
- A 1440x6000 full-page PNG shrinks to an unreadable 264px-wide thumbnail.
  Slice it into 1500px bands laid out left-to-right at 0.5 scale and screenshot
  that sheet — one readable wide image per page.
- `sips --cropOffset` does not work here; do image slicing in HTML + Chrome.

### Still open for Noah (unchanged)
- About bio copy — index.html still says "Short bio goes here...".
- Whether the Wide Span Sheds 3rd gallery slot should stay as the Round 3
  exterior-glide video. It was left alone; the award photo lives below the hero
  on the homepage now, and is not duplicated there.
- Magnetic buttons still removable in one line per page.

## Status log
- 2026-10-08 20:15 EDT: Round 5 complete. Award correction applied and verified,
  Sailing tile swapped, 7s video moment added, 9 of 22 newly-uploaded regatta
  photos published in a new section, full six-page regression pass clean.


---

## Round 6 — IN PROGRESS (started 2026-10-08 20:20 EDT)

### Source inventory (confirmed from Drive, round 6)
Lost Spring folder "The lost Spring" (1VUdlHRyHwpvvVb00jDTRji4t3Cs44wne) — 7 files,
6 of which are the Lost Spring videos (REREDO.mp4 is the HOMEPAGE HERO source, not
a Lost Spring deliverable):
- dayspa 1.mp4        15fDd9KCGIJKzLMSKM6QbzXXI1nNYSffa   706.1 MB
- dayspa 1.1.mp4      1B1ZdQ_8p4ifB28iprx-ZQRgem0zRGcnO   733.4 MB
- dayspa 2.mp4        1FE8g1iNuBWbWEDpegEzzhqWoxTCp1S1I   734.6 MB
- dayspa 3.mp4        1QUnquFjJEB6iZ5SK78rZhn5cqof1rpuF   778.6 MB

---


## Round 15 - hero swap: homepage and Lost Spring, drift interlude removed

Per Noah's explicit request ("do everything but 2" against a 4-item plan -
item 2 was a fresh re-cut from original footage, which he skipped in favour
of just reusing the existing homepage clip as-is):

1. [x] Deleted the "Interlude - different project / Not part of the Lost
   Spring films" section and its drift-car video (video/drift-h265.mp4) from
   work/lost-spring.html entirely. That footage was byte-identical to the
   Creative Focus (drifting) page hero per Round 9's finding - it is not
   lost, it still exists at video/drift-h265.mp4 and is now the homepage
   hero (see item 2).
2. [x] Homepage hero (index.html) swapped from the Lost Spring interior-lap
   clip to the FPV drift-car footage (video/drift-h265.mp4, full 42s, played
   in full as before). hero-540.mp4 regenerated from the drift source at
   960x540/1400kbps via ./_vidweb; hero-poster.jpg replaced with
   drift-h265-poster.jpg.
3. [x] The clip that WAS the homepage hero (Lost Spring interior lap,
   originally REREDO.mp4 24s-37s per Round 4) was copied, not re-cut, to
   video/ls-hero.mp4 / ls-hero-540.mp4 / ls-hero-poster.jpg before the
   homepage files were overwritten, so no footage was lost in the swap.
4. [x] work/lost-spring.html's header (.cs-hero) converted from a static
   background-image to a video element using the ls-hero files, autoplay
   muted loop. Added one CSS rule (.cs-hero video) since no case-study page
   had a video hero before this round - reuses the existing .img-slot
   absolute-position/object-fit pattern, so no other case-study page is
   affected.

NOT done (Noah explicitly said skip): a fresh re-cut of "interior lap one"
24-37s from original/raw footage. The reused clip is the file that was
already live as the homepage hero, which the HTML comment history ties to
REREDO.mp4 24s-37s - functionally the same request, just without re-deriving
it from source.
