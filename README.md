# stefaniepender.com

Static personal site. No build step, no dependencies — plain HTML and one CSS file.
Hosted on GitHub Pages. `stefaniepender.io` redirects here.

## Preview locally

```sh
cd ~/Sites/stefaniepender.com
python3 -m http.server 8000
```

Open <http://localhost:8000>. Ctrl-C to stop. Hard-refresh (Cmd-Shift-R) after CSS edits.

## Files

```
index.html              home — hero + work grid
about/index.html        about + CV + contact
work/project-one/       project page template (duplicate this per project)
css/style.css           all styling; design tokens at the top of the file
images/                 placeholder-*.svg are stand-ins — replace them
404.html                not-found page
CNAME                   tells GitHub Pages the custom domain. Do not delete.
.nojekyll               skip Jekyll processing
sitemap.xml             update when you add a page
```

Everywhere you need to write something is marked `<!-- EDIT: ... -->` in the HTML.

## Add a project

```sh
cp -R work/project-one work/my-new-thing
```

Then edit `work/my-new-thing/index.html`, and add a card to the grid in
`index.html` by copying an existing `<li>` block. The folder name is the URL:
`/work/my-new-thing/`. Add the URL to `sitemap.xml`.

## Replace the placeholder images

Drop real files into `images/` and point the `src` at them. Keep the `width`/`height`
attributes roughly matching the real aspect ratio — that's what stops the page from
jumping while images load.

- Grid cards: ~1200×900 (4:3)
- Featured wide card: ~1680×720 (21:9)
- Portrait: ~1000×1250

JPG for photos, PNG for screenshots, SVG for diagrams. Resize to under ~300 KB each
before committing — a 6 MB camera JPEG makes the site feel slow.

For link previews, add `images/og.png` at 1200×630 (referenced by the `og:image`
tags). Until it exists, shared links show no preview image.

## Deploy

First time — see `DEPLOY.md`. After that, every push to `main` publishes in ~1 minute:

```sh
git add -A && git commit -m "Update work" && git push
```
