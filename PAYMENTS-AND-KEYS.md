> **Update (payments provider):** Lemon Squeezy is winding down, so paid checkout now runs through **Creem** (creem.io), the Merchant of Record. Everything below that says "Stripe Payment Link" works the same way with a **Creem hosted checkout link**: create each product and plan in your Creem dashboard, copy its checkout link, and paste it into the `href` marked `TODO: replace with Creem checkout link URL` in the HTML. Creem collects tax, sends receipts (showing yomi@lehemnetwork.org as support), and handles subscription billing and the customer portal. The Free buttons ("Get free on WordPress.org" / the app address) are separate TODOs.

# Payments and API keys — what goes where

Reading through your message, there are actually **two separate Stripe
integrations** at play here, plus a couple of other credentials. Mixing them
up is the easiest way to end up with a key in the wrong place, so here's the
map first.

## The two Stripe integrations are not the same thing

**1. Your website's own checkout** — for selling Pro plugin zips (and
eventually ServiceLock access) directly on freshbreadpublishing.com. This is
what's currently a placeholder ("Buy Pro" buttons link to the Contact page
for now). This is what this guide is mainly about.

**2. Each product's own internal Stripe key** — e.g., AgentBridge AI Pro has
its *own* Stripe Settings field inside the WordPress admin, because it
processes payments for *your customer's* WooCommerce store, not for you.
Same with ServiceLock's `.env` file — it needs its own Stripe key when
*deployed*, to charge *ServiceLock's* customers' deposits. **Neither of
these ever touches your website's code or Netlify settings.** They're
configured by whoever installs/deploys that product, in that product's own
admin screen or `.env` file — that's a customer/deployment-time step, not a
website-update step.

Everything below is about integration #1 only.

## Recommended approach for now: Stripe Payment Links

Since you don't have Stripe keys yet, and don't want to wire up a backend
just to take a payment, **Stripe Payment Links** is the right starting
point:

- No code changes needed on this site at all — you just paste a URL.
- No secret key is ever exposed in the website's HTML/JS, because there's
  no API call happening client-side — the button is a plain link to a page
  Stripe hosts.
- Works immediately on a fully static site (GitHub Pages or Netlify — no
  difference).

**How to set it up once you have a Stripe account:**

1. In the Stripe Dashboard → **Payment links** → create one for each paid
   product (e.g. "AgentBridge AI Pro — $__").
2. Set its **after payment** behavior to redirect to a page on your own
   site — e.g. `https://freshbreadpublishing.com/downloads/agentbridge-ai-pro.html`
   (see "The download page" below for what that page needs).
3. Copy the Payment Link URL Stripe gives you.
4. In `plugins/agentbridge.html`, find the line commented
   `<!-- TODO: replace with Stripe Payment Link URL once created -->` and
   replace `../contact.html` with that URL.
5. Repeat for every other paid product as you create its Payment Link.

That's the entire integration. Nothing else on the site needs to change.

## The download page (after payment)

Since a Payment Link can redirect anywhere after checkout, that's also your
"gate": create a simple page per product (e.g.
`downloads/agentbridge-ai-pro.html`) with a single download button pointing
at wherever you host the zip (GitHub, see below).

**Honest security note:** a Payment Link redirect is not a real access
gate — the download page's URL isn't secret, so someone who guesses or finds
it could download without paying. For a small catalog at this stage, that's
a normal, accepted tradeoff most small software sellers live with (it relies
on the URL not being advertised anywhere but the post-payment redirect).
**If you want a real gate later** (the zip is only ever served after Stripe
confirms payment), that requires a small serverless function — Netlify
supports this natively (Netlify Functions), and since you're already using
Netlify for the contact form, extending it to verify a Stripe session before
returning a signed, time-limited download link is a contained, well-defined
next step. Flag it when you're ready and it can be scoped properly rather
than guessed at now.

## Where to host the Pro zip files

You mentioned GitHub. Two options, different tradeoffs:

- **Public GitHub repo (or a public release asset):** simplest — a direct
  link works immediately, no extra setup. Downside: the zip's URL is public
  the moment it exists, whether or not someone paid (same caveat as above).
- **Private GitHub repo:** the zip isn't publicly reachable by a plain link
  at all — downloading it requires an authenticated request (a GitHub
  token). This is more secure but means a plain "download" button on a
  static page *can't* fetch it directly; you'd need the Netlify Function
  mentioned above to fetch it server-side (using a token stored in Netlify's
  environment variables) and hand it to the visitor. This is the "do it
  properly" version of the gate.

**For now, given you want placeholders and no backend work yet:** a public
repo (or a public, unlisted release asset URL) paired with the Payment Link
redirect above gets you a fully working purchase-and-download flow today,
with the known tradeoff stated plainly above. Upgrade to the private-repo +
Netlify Function version whenever real revenue makes the extra security
worth building.

## Netlify environment variables — for later, when you build the real gate

You already correctly guessed that secrets shouldn't go in GitHub. Here's
why, precisely: anything committed to a GitHub repo is either fully public
(public repo) or visible to anyone with repo access (private repo) — it is
never hidden from the browser or from collaborators, and it stays in the
repo's history even if you delete it later unless you rewrite history
entirely. A secret key belongs somewhere that only your server-side code can
read at request time, never in a file that ships to the browser.

**Netlify environment variables** are exactly that: values set in
**Site configuration → Environment variables**, readable only by Netlify
Functions running server-side, never sent to the visitor's browser. When you
build the real payment-gating function later, that's where the Stripe
secret key and a GitHub access token (for pulling from a private repo) would
go.

**For the Payment Links approach in this guide, you don't need this at
all yet** — there's no secret key involved anywhere in a Payment Link
button. This section is here so you know where things go once you're ready
to build the tighter version.
