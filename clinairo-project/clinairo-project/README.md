# Clinairo Website

Static HTML/CSS/JS. No build step. Deploy the `public/` folder.

## Structure

```
clinairo-website/
├── public/                 ← deploy this folder
│   ├── index.html          Homepage (leak map, walkthrough, audit, pricing)
│   ├── features.html       Seven modules in detail
│   ├── case-studies.html   Dr. Abubakar Siddique — real numbers + video
│   ├── testimonials.html   Verified client feedback
│   ├── about.html          Vision, mission, values, team
│   ├── contact.html        Lead form → Firestore + EmailJS
│   ├── blog.html           Index with category filters
│   ├── blog-*.html         16 SEO posts (med spa / aesthetics keywords)
│   ├── privacy-policy.html
│   ├── terms.html
│   ├── 404.html
│   ├── sitemap.xml         25 URLs
│   ├── robots.txt
│   ├── site.webmanifest
│   ├── favicon.* / apple-touch-icon.png / android-chrome-*.png / og-image.png
│   └── videos/
│       └── Dr.Abubakar-Feedback-About-Clinairo.mp4   ← ADD THIS FILE
├── firebase/
│   ├── firebase-config.json
│   └── firestore.rules     ← DEPLOY THIS (updated schema)
├── vercel.json
└── README.md
```

## Before you deploy — three required steps

**1. Add the testimonial video.**
Put the file in `public/videos/` named:

    dr-abubakar-testimonial.mp4

All lowercase, hyphens only, no full stops in the name. The pages also accept the old
`Dr.Abubakar-Feedback-About-Clinairo.mp4` as a fallback, but the lowercase name is safer
on static hosts.

**If the video doesn't play after deploying, check in this order:**

1. **Open the file URL directly** — `https://www.clinairo.com/videos/dr-abubakar-testimonial.mp4`
   If it 404s the file isn't deployed. If it downloads, the file is fine.
2. **Is it inside `public/`?** The video must be at `public/videos/`, not `videos/` at the
   repo root. Vercel serves the folder you set as Root Directory.
3. **Did GitHub reject it for size?** Files over 100MB are refused outright, and pushes
   over ~50MB warn. Run `git lfs install && git lfs track "*.mp4"` and re-push, or host
   the video on a CDN and change the `<source src>`.
4. **Filename characters.** The original name contains a full stop (`Dr.Abubakar`), which
   some static hosts mishandle. Renaming to `dr-abubakar-testimonial.mp4` removes the risk.
5. **Check the browser console** for a 404 on the video URL — that confirms it's a file
   path problem rather than a codec one.

If all sources fail, the page now shows a visible message naming the expected filename
instead of a black box, so you can diagnose it on the live site.

**2. Deploy the updated Firestore rules.**
`firebase/firestore.rules` replaces the old `audit_submissions` validator. The homepage
audit now collects consult-economics fields instead of missed-call fields, so the old
rules will reject writes. Deploy via Firebase CLI or paste into Console → Firestore → Rules.

**3. Authorise your domain in Firebase.**
Console → Authentication → Settings → Authorized domains → add `clinairo.com` and
`www.clinairo.com`. Without this, live form submissions fail silently.

## If the favicon doesn't appear

All icon files sit in `public/` and are referenced with absolute paths (`/favicon.ico`).
That is correct once the site is served from the domain root. If icons are missing:

1. **Check Vercel's Root Directory is set to `public`.** If it is set to the repo root,
   `/favicon.ico` resolves to a file that isn't there. This is the most common cause.
2. **Hard-refresh.** Browsers cache favicons aggressively and separately from pages.
   Ctrl/Cmd+Shift+R, or open the site in a private window.
3. **Confirm it loads directly** — visit `https://www.clinairo.com/favicon.ico` in a
   browser. If that 404s, the deploy root is wrong. If it downloads, the files are fine.
4. **File previews won't show it.** Opening the HTML from disk or a preview host makes
   `/favicon.ico` point at that host's root, not your folder. Not a bug in the site.

### Getting it into Google Search results

Google reads the favicon from your **homepage** `<head>` and re-crawls it on its own
schedule — expect days to weeks, not hours. Requirements, all already satisfied here:

- Icon on the same domain as the homepage, at a stable URL
- Crawlable — `robots.txt` allows everything, including the icon files
- Square, at least 8×8px — the ICO ships 16×16 and 32×32
- Declared with `<link rel="icon">` on the homepage

To speed it up: submit `sitemap.xml` in Google Search Console, then request indexing on
the homepage. Google will not show a favicon for a site it has not indexed.

## Recommended before launch

- **Enable Firebase App Check** (reCAPTCHA). Without it, anyone can script writes to
  `consultation_requests` and fill your database with junk.
- **Legal review** of `privacy-policy.html` and `terms.html`. Both carry a visible notice.
  Prioritise Terms sections 7 (guarantee), 12 (warranties), 13 (liability) and the
  governing-law clause.
- **The HIPAA badge is now live in the footer on all 26 pages.** It replaced the
  "Built for US Practices" badge. Make sure this is actually true before launch: every BAA
  in Section 9.1 of the strategy document signed, and the PHI data-flow map complete.
  A published HIPAA claim you can't document is a legal and reputational risk in this
  market, and it's the first thing a clinic's compliance reviewer will ask you to evidence.
  To remove it, search for `HIPAA COMPLIANT` in the footer of each page.

## Deploy on Vercel

1. Push to GitHub
2. Vercel → Add New → Project → import the repo
3. Framework Preset: **Other**
4. Root Directory: **public**
5. Deploy, then add your domain under Settings → Domains

`vercel.json` handles clean URLs and 301-redirects the retired `/services` path to
`/features`.

## Scheduling

Calendly (`https://calendly.com/contact-clinairo/30min`) is linked from 13 places, all
opening in a new tab with `rel="noopener"`:

- Contact page — sidebar, a dedicated booking panel, step 3, form success, bottom CTA
- Homepage — contact sidebar, form success, audit success
- Features, case studies, testimonials, about, blog — secondary CTA

The audit remains the primary call to action everywhere. Calendly is the alternative
path for people who would rather talk than fill in a form.

**Worth doing on the Calendly side:** rename the event from "30 Minute Meeting" to
something like "Clinairo — Consult Economics Review", remove Calendly branding (paid
tier), set working hours that make sense for US callers, and write your own confirmation
and reminder emails.

## Analytics

Google Analytics 4 (`G-F86MY7M3YV`) is installed in the `<head>` of all 26 pages.

**Custom events already firing:**

| Event | Where | Why it matters |
|---|---|---|
| `generate_lead` | Homepage + contact form submit | Your actual conversion. Includes practice type and booking software as parameters |
| `audit_submitted` | Homepage audit "Get My Full Audit Breakdown" | Sends the calculated annual recoverable figure as `value`, so you can see lead quality not just volume |
| `book_call_click` | Any Calendly link, all 26 pages | Which page drives bookings |
| `email_click` | Any mailto link | |
| `read_depth` | 16 blog posts, at 25/50/75/100% | Which articles are actually read vs bounced |

**Mark these as key events in GA4:** Admin → Events → toggle "Mark as key event" on
`generate_lead`, `audit_submitted` and `book_call_click`. Without this they're recorded
but won't appear in conversion reports.

**Data takes 24–48 hours** to appear in standard reports. Use Realtime to confirm the tag
is live immediately after deploying.

**Also worth doing:** link GA4 to Google Search Console (Admin → Product links) so you can
see which search queries lead to conversions, not just which pages get traffic.

## Backend

- **Contact form** (`contact.html`) writes to Firestore `consultation_requests` and fires
  an EmailJS notification. Firestore rules require exactly six fields, so practice type,
  booking software, consultation volume and locations are folded into `message`.
- **Consult audit** (`index.html#audit`) writes anonymously to `audit_submissions` —
  no name, email or phone attached.
- Read access on both collections is restricted to the admin email in the rules file.

## Design system

One accent (sky blue `#3E9DFF`), green (`#34D399`) as a positive-state colour only,
no gradients outside the phone mockup. Dark navy base with warm ivory light sections
(`.light` class re-declares the variables). Inter for text, JetBrains Mono for numbers.
Every page carries a global mobile safety layer preventing horizontal overflow.
