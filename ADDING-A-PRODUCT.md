# Adding a new product to the site — copy/paste guide

This is the workflow you asked for: add a new plugin or connector by pasting a
few self-contained blocks into GitHub, without touching or re-uploading
anything that already works.

You'll touch **4 spots**, always in this order:

1. Create the new plugin's own page (one new file).
2. Add its card to the Home page grid.
3. Add its card to the Products page grid.
4. Add its link to the footer (which appears on every page — 13 places, see note below).

---

## 1. Create the product's page

In GitHub, go to the `plugins/` folder → **Add file → Create new file**.
Name it `plugins/yourslug.html` (lowercase, no spaces — e.g. `plugins/newtool.html`).

Copy the entire contents of an existing similar page (`plugins/visiontag.html`
is a good plain template) and paste it into the new file. Then edit only:

- The `<title>` and `<meta name="description">` in the `<head>`
- The status tag text, product name (`<h1>`), and hero paragraph
- The three feature blocks under "How it works"
- The pricing tier cards (see the pricing patterns below)
- The `Explore the rest of the suite` grid — this can stay as-is; you'll add cross-links properly once the product also appears in the main grids below

Commit the new file.

### Which pricing pattern to copy

- **Metered SaaS with a free tier** (like VaultMind, DocuMind, etc.): copy the three `.tier` cards (Free / Pro / Agency) from `plugins/visiontag.html`.
- **Free WordPress.org plugin + one paid Pro download** (like AgentBridge AI): copy the two-card Free/Pro block from `plugins/agentbridge.html`.
- **Pricing not decided yet** (like ServiceLock): copy the single "Pricing to be announced" block from `plugins/servicelock.html`.

## 2. Add its card to the Home page

Open `index.html`, find the `<div class="grid-4">` block under `<section id="products">`. Paste this snippet as one of the `<a>` blocks inside it (anywhere in the list — order doesn't matter):

```html
<a class="product-card" href="plugins/yourslug.html">
  <div class="mark">YS</div>
  <div>
    <div class="role">One-line category</div>
    <h3>Your Product Name</h3>
  </div>
  <p class="desc">One or two sentences describing what it does for the visitor.</p>
  <div class="foot">
    <span class="price-badge mono">$X–$Y/mo</span>
    <span class="arrow">View plugin &rarr;</span>
  </div>
</a>
```

Replace `YS` with a short 2-letter mark (check existing ones aren't reused), and `yourslug` with the filename you created in step 1.

## 3. Add the same card to the Products page

Open `products.html`, find the same `<div class="grid-4">` block, and paste the **identical** snippet from step 2 in there too.

## 4. Add it to the footer everywhere

The footer's Products list is repeated at the bottom of **every page** (13 files right now — every plugin page, plus Home, Products, About, Contact, the 3 legal pages, and the thank-you page). In each one, find:

```html
<h5>Products</h5>
<ul>
```

and add one line inside that `<ul>`:

```html
<li><a href="plugins/yourslug.html">Your Product Name</a></li>
```

**Note the path changes depending on where you are:** pages inside `/plugins/` need `../` in front of every link (e.g. `../plugins/yourslug.html`, `../about.html`), while pages at the root (`index.html`, `about.html`, etc.) don't. Copy the path style already used by the neighboring `<li>` lines in whichever file you're editing, and you'll get it right automatically.

---

## Why it's 13 footer edits and not 1

This site has no shared template engine running live — every HTML file is a
complete, independent, static document. That's what makes it free to host
and impossible to hack (see the README for why static was the right call).
The tradeoff is that a change repeated on every page has to be pasted into
every page. If this becomes a real chore as the catalog grows, the fix is to
regenerate the whole site from the Python script this was originally built
with (ask for it) rather than hand-editing 13+ files by hand each time.
