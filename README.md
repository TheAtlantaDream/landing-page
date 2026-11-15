# VoiceCoder landing page

The public website for [VoiceCoder](https://github.com/TheAtlantaDream/VoiceCoder), a hands-free coding assistant: you describe a change out loud, an AI agent makes it in your own workspace, and you review the result in VS Code. The page is a single static `index.html`. An animated three.js background shows cubes moving like a sound wave, which turn into a microphone every so often. Over it sit a home headline and three subpages: **What we do**, **Set up** and **About us**. There's no build step, framework or server code.

## Edit content

Everything is in `index.html`. Each editable section starts with an `EDIT:` comment block. Search for `EDIT:` to jump between them.

- **Headline and main button:** the `EDIT: HOME` block (`<div class="hero-center">`).
- **What we do:** the intro paragraph, the three numbered step cards (`<ol class="card-grid">`) and the feature chips.
- **Set up:** the "What you need" chips and the numbered steps. If you change a command, change it in both the `<code>` text and the Copy button's `data-copy` attribute.
- **About us:** the team intro and one card per person. Each card has a `TEAM MEMBER` comment block saying what to fill in (name, role, description, photo).
- **Photos:** add them to `assets/team/` as `member-1.webp` … `member-4.webp`. See [`assets/team/README.md`](assets/team/README.md) for size and format. A missing photo shows as a coloured square.

Search for `TODO(team)` to find everything that still needs real values.

## Config

Links that depend on the Coder deployment are set in one place, at the top of the main `<script>` in `index.html`:

```js
const CONFIG = {
  coderUrl: "https://coder.example.com",   // TODO(team): real Coder deployment URL
  template: "voicecoder",                  // TODO(team): template name in Coder
  repoUrl: "https://github.com/TheAtlantaDream/VoiceCoder"
};
```

Links marked `data-link="create"` go to `${coderUrl}/templates/${template}/workspace?mode=auto`, `data-link="workspaces"` go to `${coderUrl}/workspaces`, and `data-link="repo"` go to `repoUrl`. The same URLs are also written in the HTML, so the links work even if the script fails. Update both if you change them.

## Run locally

From this folder:

```
python3 -m http.server 8000
```

Then open <http://localhost:8000/>. Any static file server works. Without Python: `npx serve .`

## Deploy (GitHub Pages)

1. Merge your branch into `main`.
2. In the repo, open **Settings → Pages → Build and deployment**: Source "Deploy from a branch", Branch `main`, folder `/ (root)`.
3. The site goes live at `https://<owner>.github.io/<repo>/` within a minute or two. A custom domain is optional, on the same screen.

The page uses only relative paths and hash routes (`#what-we-do`, `#setup`, `#about-us`), so it works from a sub-path like `/<repo>/`. Keep it that way: don't add root-absolute paths like `/assets/...`. The empty `.nojekyll` file stops GitHub Pages from processing the site with Jekyll.

## Accessibility

- The background animation can be paused with the **Pause animation** button at the bottom. With "reduce motion" turned on in the operating system, it starts paused.
- Subpages scroll with the mouse wheel, trackpad or keyboard, even though the scrollbar is hidden.
- The page uses no trackers or analytics. three.js is served from `assets/vendor/` (r128, MIT licence in `three.LICENSE`); the only outside requests are to Google Fonts.
