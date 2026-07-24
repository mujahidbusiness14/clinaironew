# Video Assets

Place the Dr. Siddique testimonial video here, named exactly:

```
Dr.Abubakar-Feedback-About-Clinairo.mp4
```

The Case Studies page (`public/case-studies.html`) already references this
path via:

```html
<source src="videos/Dr.Abubakar-Feedback-About-Clinairo.mp4" type="video/mp4">
```

## Recommended video specs
- Format: MP4 (H.264 video / AAC audio) — plays natively in every browser, no conversion needed
- Resolution: 1080p is plenty; avoid 4K, it just slows page load
- Keep file size under ~30–50MB if possible — compress with Handbrake or similar if the raw export is large
- Aspect ratio: 16:9 to match the video frame already built into the page

## Adding more videos later
As more clinics go live and you record more testimonials, drop additional
`.mp4` files in this folder and reference them the same way from wherever
you build out those future case studies.
