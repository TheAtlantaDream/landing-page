# LANDING_SETUP.md — get the VoiceCoder landing page into its repo and onto GitHub Pages

**For:** the Claude Code session working in the **landing page repo** (not `voicecoder-frontend`, not the outer `VoiceCoder` repo).
**Owner:** Seth (#4). Save this file as `CLAUDE.md` at the repo root so it's read every session.
**Starting point:** `index.html`, which comes with this file. **The design is approved.** Your job is to clean it up, make it maintainable and accessible, and deploy it. Don't redesign it.

---

## 1. What the page is

A single static HTML page:
- **Background:** a three.js scene where cubes in the VoiceCoder palette float as a block sound wave that reacts to the cursor. On a timer, the cubes morph into a side-mounted tube microphone and back.
- **Header:** "VoiceCoder" on the left; nav on the right: **What we do**, **Set up**, **About us**.
- **Home:** a two-line pill and pixel-block headline ("Code / With your" · "VoiceCoder / Hands-free") and a **Start coding with your voice** button.
- **Subpages:** a frosted panel over the animated background for each. Routing is by hash only: `#what-we-do`, `#setup`, `#about-us`; an empty hash is home.

**Palette:** bg `#F5F4EF` · text `#111111` · grey block `#EAEAE4` · white `#FFFFFF` · sage `#71906E` · mint `#98FB98` · coral `#FF6060` · yellow `#FFFFC5` · black `#141414`.
**Fonts:** Space Grotesk (text) and Silkscreen (pixel blocks), from Google Fonts.

## 2. Hard rules

- **Static only.** One `index.html` plus files under `assets/`. No framework, bundler, build step, server code, or npm runtime dependencies.
- **Keep the look.** Don't change layout, colours, fonts, copy or animation except where a task below says so. If something seems wrong, list it in your hand-back instead of changing it.
- **No trackers or analytics**, and no third-party scripts besides Google Fonts (and three.js until §3.4 replaces it with a local copy).
- **No secrets** in the repo (there shouldn't be any; this page has no API calls).
- `main` deploys live once Pages is on. **Work on a branch and don't push to `main` yourself.** Hand back to the human.

## 3. Tasks (in order)

### 3.1 Repo layout
```
index.html
assets/
  team/          # photos the team adds (see 3.3)
  vendor/        # three.min.js (see 3.4)
  favicon.svg
.nojekyll        # empty file; stops GitHub Pages from running Jekyll
CLAUDE.md        # this file
README.md        # see 3.7
```

### 3.2 Clean-up (no visual change)
- Remove every `vid="…"` attribute (they're leftovers from a design tool; there are 26).
- Add `<!DOCTYPE html>` if it's missing, and keep `<html lang="en">`.
- `<title>VoiceCoder · Code with your voice</title>` and `<meta name="description" content="Code by voice. Describe a change out loud and an AI agent writes it in your own workspace. Built for developers who can’t rely on a keyboard or mouse.">`.
- Open Graph tags: `og:title`, `og:description`, `og:type=website`.
- A simple `assets/favicon.svg`: a coral `#FF6060` rounded square with three short black vertical bars like a sound wave. Link it with `<link rel="icon" href="assets/favicon.svg" type="image/svg+xml">`.
- **Put all links the team must fill in in one place.** At the top of the main script, add:
  ```js
  const CONFIG = {
    coderUrl: "https://coder.example.com",   // TODO(team): real Coder deployment URL
    template: "voicecoder",                  // TODO(team): template name in Coder
    repoUrl: "https://github.com/TheAtlantaDream/VoiceCoder"
  };
  ```
  Give every link that depends on these a `data-link` attribute (`create`, `workspaces`, `repo`), and set its `href` from `CONFIG` on load: `create` → `${coderUrl}/templates/${template}/workspace?mode=auto`, `workspaces` → `${coderUrl}/workspaces`. Keep a working `href` in the HTML as well, so the links still work if the script fails.
- Leave the setup command `coder templates push voicecoder --directory ./template` as is, but add `<!-- TODO(team): confirm template push command -->` next to it.

### 3.3 Team photos (About us)
Replace each coloured "Photo" placeholder with an image slot that falls back to the coloured square:
```html
<img class="photo bg-coral" src="assets/team/member-1.webp" alt="TODO(team): describe the photo, e.g. Seth smiling in a grey hoodie" width="140" height="140" loading="lazy">
```
- Keep the existing `.photo` sizing (140×140, square). Add `object-fit: cover`.
- If the file is missing, the coloured background should still show: give the `img` the background class, and add a small `onerror` that removes `src`/`alt` and sets `role="img"` + `aria-label="Photo of <name>"`. No broken-image icon.
- Names stay as `[TEAMMATE NAME]` placeholders except **Seth (Front end)**. Write a short `assets/team/README.md`: square crop, about 400×400, WebP or JPG, under about 100 KB, filenames `member-1` … `member-4`.

### 3.4 Self-host three.js
- Put **three.js r128** (the version the page uses) at `assets/vendor/three.min.js`. Get it with `npm pack three@0.128.0` and copy `package/build/three.min.js` out of it; don't add `three` to a `package.json`.
- Change the script tag to `<script src="assets/vendor/three.min.js"></script>`.
- Add the three.js MIT license as `assets/vendor/three.LICENSE`.

### 3.5 Accessibility fixes (the only allowed visual changes)
1. **Pause control for the animation.** The background moves continuously, so a pause control is required. Put a small button in the empty bottom bar (`.edge-nav.bottom`), styled like the nav (frosted pill, uppercase small text): **"Pause animation"** / **"Play animation"**, with `aria-pressed`. While paused, stop the render loop's motion (keep showing the last frame).
2. **Reduced motion.** If `prefers-reduced-motion: reduce`, start paused: render one static frame of the wave and skip the mic morph timer. The button still lets users press Play.
3. **Readable small text.** The nav/meta text is `0.65rem`. Raise it to `0.8rem`, keeping the uppercase and letter-spacing.
4. **Cursor.** Remove the global `cursor: crosshair`. Use the default cursor, `pointer` on links and buttons, and keep crosshair only on `#canvas-container` if you like.
5. **Decorative canvas.** Give the canvas container `aria-hidden="true"`.
6. **Focus.** Every link and button needs a visible focus outline (keep the existing ones; add one where missing).
7. **Scrolling on subpages.** Scrollbars are hidden on purpose. Make sure the content still scrolls with the mouse wheel, trackpad and keyboard: give `.page-view` `tabindex="-1"` and focus it together with the heading on route change, so arrow keys and PageDown scroll it.
8. **Mobile.** At 375px wide: the nav may wrap but must stay visible (remove the `display: none` on `.main-nav` under 600px); the headline stays readable; no horizontal scroll.

### 3.6 Verify locally
```
python3 -m http.server 8000
```
Then open `http://localhost:8000/` and check:
- Home, `#what-we-do`, `#setup` and `#about-us` all render. The nav marks the current page (`aria-current`), and the brand returns home.
- The copy buttons on Set up copy their commands.
- The wave reacts to the cursor and recenters when the cursor leaves the page. The mic morph happens about every 21 s. Pause/Play works. Reduced motion starts paused (emulate it in DevTools → Rendering).
- No console errors, and nothing loads from a CDN except Google Fonts.
- Lighthouse (DevTools): **Accessibility ≥ 95**, Best Practices ≥ 95. Report the scores.
- Keyboard only: you can reach and use everything, and focus is always visible.

### 3.7 README.md
Include: what the site is (one paragraph); a **Edit content** section (where the headline, step text and team cards are in `index.html`, and how to add photos); **Config** (the `CONFIG` object); **Run locally** (the `http.server` command); and **Deploy** (below).

### 3.8 Deploy (GitHub Pages)
Write this in the README; the human does the clicks:
1. Merge the branch into `main`.
2. Repo **Settings → Pages → Build and deployment**: Source "Deploy from a branch", Branch `main`, folder `/ (root)`.
3. The site goes live at `https://<owner>.github.io/<repo>/` within a minute or two. A custom domain is optional, on the same screen.

The page uses only relative paths and hash routes, so it works from a sub-path like `/<repo>/` without changes. Keep it that way: no root-absolute paths like `/assets/...`.

## 4. Hand-back

Report: the files changed or added, the Lighthouse scores, anything left as `TODO(team)` (the Coder URL, template name, teammate names, photos, the template push command), and anything you noticed but didn't change. **Don't push to `main`.**
