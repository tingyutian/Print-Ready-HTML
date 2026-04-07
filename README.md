# Print-Ready-HTML

A CSS skill for making HTML pages print correctly — keeping your colors, backgrounds, and layout intact while removing the browser's auto-injected headers and footers.

---

## The problem this solves

When you print a webpage (or save it as PDF), browsers do three annoying things by default:

1. **Strip all background colors and images** to save ink
2. **Inject headers/footers** with the page title, URL, and current date
3. **Let content spill onto a second page** with no warning

This skill fixes all three.

---

## What's included

| File | What it does |
|------|-------------|
| `print-ready.css` | Drop-in print stylesheet |
| `template.html` | Starter HTML with everything wired up |
| `SKILL.md` | Full reference for Claude (AI skill definition) |

---

## Defaults

| Setting | Default | Why |
|---------|---------|-----|
| Paper size | **Letter portrait** (8.5" × 11") | Standard US paper |
| Body padding | `10mm` | Keeps content off the paper edge |
| Font size | `10pt` | Readable and space-efficient |
| Background printing | Forced on via CSS | So your colors actually show up |

Need A4 or landscape? See [Changing paper size](#changing-paper-size) below.

---

## Quickstart

### 1. Use the template

Copy `template.html` and put your content inside `<div class="page">`:

```html
<div class="page">
  <h1>Your Title</h1>
  <p>Your content here...</p>
</div>
```

### 2. Print it

Open in Chrome → `Ctrl+P` (or `Cmd+P` on Mac)

### 3. Enable background graphics

In the print dialog → **More settings** → turn on **"Background graphics"**

> This is a Chrome user toggle that CSS alone can't override. You need to enable it once.

---

## Key features

### ✅ Colors and backgrounds print correctly

```css
* {
  -webkit-print-color-adjust: exact;
  print-color-adjust: exact;
}
```

Without this, every background color shows up as white on paper.

---

### ✅ No browser date/title/URL in the margins

```css
@page {
  margin: 0;
}
```

Browsers inject their own header and footer text into the page margin area. Setting margin to `0` removes that space entirely. Body padding is used instead to keep content from touching the edges.

---

### ✅ Content won't overflow onto a second page

The `.page` container is locked to the exact dimensions of Letter paper:

```css
.page {
  width: 215.9mm;
  max-height: 279.4mm;
  overflow: hidden;
  box-sizing: border-box;
}
```

If content is still too tall, a small script scales it down to fit automatically:

```html
<script>
  window.addEventListener('load', () => {
    const page = document.querySelector('.page');
    if (!page) return;
    const maxPx = (279.4 - 20) * (96 / 25.4);
    if (page.scrollHeight > maxPx) {
      const scale = maxPx / page.scrollHeight;
      page.style.transform = `scale(${scale})`;
      page.style.transformOrigin = 'top left';
      page.style.width = `${100 / scale}%`;
    }
  });
</script>
```

---

## Changing paper size

Override the `@page` size value in your print styles:

| Paper | CSS value |
|-------|-----------|
| Letter portrait *(default)* | `size: Letter portrait` |
| Letter landscape | `size: Letter landscape` |
| A4 portrait | `size: A4 portrait` |
| A4 landscape | `size: A4 landscape` |
| Legal | `size: Legal portrait` |

Also update `.page` dimensions to match. Reference:

| Paper | Width | Height |
|-------|-------|--------|
| Letter portrait | 215.9mm | 279.4mm |
| A4 portrait | 210mm | 297mm |
| Letter landscape | 279.4mm | 215.9mm |
| A4 landscape | 297mm | 210mm |

---

## Hiding elements from print

Add `class="no-print"` to anything that shouldn't appear on paper (buttons, navbars, etc.):

```html
<button class="no-print">Download</button>
```

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Backgrounds/colors are missing | Enable "Background graphics" in Chrome print dialog |
| Browser date/title still showing | Make sure `@page { margin: 0 }` is inside `@media print` |
| Content spills onto a second page | Check that `.page` has `max-height` set and no `min-height` anywhere |
| Content is cut off at the bottom | Switch from `overflow: hidden` to the JS scale approach |
| Extra blank page | Remove any `min-height: 100vh` from print styles |
| Shadows or rounded corners look off | Add `box-shadow: none; border-radius: 0` to print styles |

---

## Pre-print checklist

- [ ] `print-color-adjust: exact` on `*`, `body`, `.page`
- [ ] `@page { margin: 0; size: Letter portrait; }` inside `@media print`
- [ ] `body { padding: 10mm }` inside `@media print`
- [ ] No `min-height: 100vh` in print styles
- [ ] `.page` has `max-height` and `overflow: hidden`
- [ ] Screen-only elements hidden with `.no-print`
- [ ] *(In Chrome)* More settings → "Background graphics" enabled
- [ ] Tested with `Ctrl+P` / `Cmd+P` before sharing

---

## License

MIT
