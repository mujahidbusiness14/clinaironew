# Clinairo Website

AI Front Desk automation website for private clinics — static HTML/CSS/JS,
dark navy SaaS design, fully animated, 9 pages, HIPAA-compliant messaging,
and a free revenue-loss audit calculator on the homepage.

## Project structure

```
clinairo-project/
├── public/                        ← this is the actual website (deploy this folder)
│   ├── index.html                 ← homepage (hero, diagram, audit calculator, etc.)
│   ├── features.html
│   ├── services.html
│   ├── case-studies.html          ← Dr. Siddique case study + video
│   ├── testimonials.html
│   ├── about.html
│   ├── contact.html
│   ├── privacy-policy.html
│   ├── terms.html
│   ├── sitemap.xml
│   ├── robots.txt
│   └── videos/
│       └── Dr.Abubakar-Feedback-About-Clinairo.mp4   ← ADD YOUR VIDEO FILE HERE
├── firebase/
│   ├── firebase-config.json       ← your real Firebase project config
│   └── firestore.rules            ← your existing Firestore security rules
├── package.json
├── vercel.json
└── README.md                      ← you are here
```

## 1. Add your video

Drop your testimonial video file into:

```
public/videos/Dr.Abubakar-Feedback-About-Clinairo.mp4
```

The Case Studies page already points to this exact filename — no code
changes needed, just add the file.

## 2. Push to GitHub

From inside the `clinairo-project` folder:

```bash
git init
git add .
git commit -m "Initial Clinairo website"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/clinairo-website.git
git push -u origin main
```

If the video file is large (over ~50MB), consider using
[Git LFS](https://git-lfs.com) so GitHub doesn't reject the push:

```bash
git lfs install
git lfs track "*.mp4"
git add .gitattributes
```

## 3. Deploy on Vercel

1. Go to [vercel.com](https://vercel.com) and sign in (GitHub login is easiest)
2. Click **Add New → Project**
3. Import the `clinairo-website` repo you just pushed
4. On the configuration screen:
   - **Framework Preset:** Other
   - **Root Directory:** click "Edit" and set it to `public`
5. Click **Deploy**

Vercel will give you a live `.vercel.app` URL immediately. To use
**clinairo.com**:

1. In the Vercel project, go to **Settings → Domains**
2. Add `clinairo.com` and `www.clinairo.com`
3. Update your domain's DNS (wherever it's registered) with the records
   Vercel shows you — usually one A record and one CNAME record
4. DNS changes can take a few minutes to a few hours to propagate

## 4. Firebase — contact form (already wired in)

The contact form on `public/contact.html` is **fully connected** to your
Firebase project (`encouraging-trilogy-3mln4`) and writes directly to
Firestore — no server, no Express, no build step needed. This works because
your Firestore security rules already allow public `create` requests that
match the exact `consultation_requests` schema, so the browser can write
straight to the database.

Files included for reference:

- `firebase/firebase-config.json` — your real project config (also embedded
  directly in `contact.html`)
- `firebase/firestore.rules` — your existing security rules, included here
  so you have them versioned alongside the site (deploy via Firebase CLI or
  paste into Firebase Console → Firestore → Rules if you ever need to
  restore them)

**What happens on submit:**
1. Visitor fills out the form and clicks "Book Free Consultation"
2. The Clinic Type dropdown gets folded into the message text (your
   Firestore rules require the exact 6-field shape: `name`, `clinicName`,
   `email`, `phone`, `message`, `createdAt` — no extra fields allowed)
3. The document is written to the `consultation_requests` collection with
   `createdAt` set via `serverTimestamp()`
4. On success, the green confirmation message shows and the form resets
5. On failure, a red error message shows with a fallback email address

**Before going live, do this one thing:** in Firebase Console → Authentication
→ Settings → **Authorized domains**, add `clinairo.com` and
`www.clinairo.com`. Without this, submissions from your live domain will be
silently rejected even though it works locally and on the Vercel preview URL.

**To view submissions:** Firebase Console → Firestore Database →
`consultation_requests` collection. Only your admin email
(`mujahidbusiness14@gmail.com`, signed in via Firebase Auth) can read them,
per your existing security rules — the public can only create new entries,
never read others.

**Note on your existing `vercel.json`:** your other project's Vercel config
(`"framework": "vite"`, `buildCommand`, SPA rewrites) is built for a
Vite + Express single-page app and doesn't apply here — this site is plain
static multi-page HTML with no build step. The `vercel.json` included in
this project (just `cleanUrls` + `trailingSlash`) is the correct one to use;
don't swap in the other one or every page will get rewritten to `index.html`.

**Note on the audit calculator:** the homepage's Free Revenue Audit
calculator is currently front-end only (it computes and displays results,
but doesn't save anything). It wasn't wired to Firestore since no schema was
provided for it — let me know if you want those submissions captured too and
I'll add a second collection following the same pattern.

## 5. Before going live — HIPAA & legal review

- The Privacy Policy and Terms pages already contain your real content —
  no placeholders remain
- Before publicly claiming "HIPAA Compliant," make sure Business Associate
  Agreements (BAAs) are actually signed with every vendor in your stack
  (Vapi, Twilio, OpenAI/Azure OpenAI, GoHighLevel) — the site's compliance
  messaging assumes this is done
- Update `sitemap.xml` and `robots.txt` if your final domain differs from
  `clinairo.com`

## What's already built in

- Fully responsive (mobile, tablet, desktop)
- Every "Book Free Consultation" button and nav link cross-links correctly
  across all 9 pages
- Free Revenue Audit calculator on the homepage (real formulas, live
  count-up results)
- Animated "How It Works" system diagram, animated phone chat demo,
  animated case study stats
- Premium multi-column footer on every page
- SEO: unique title + meta description per page, sitemap.xml, robots.txt,
  favicon
- No stock photography anywhere — all visuals are coded/animated
