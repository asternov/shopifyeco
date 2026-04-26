# Lighthouse Baseline Report — aya.eco

**Date:** 2026-04-26
**Tool:** Lighthouse 13.1.0
**Mode:** Mobile (simulated throttling)
**Runs:** 3 per page (median values)

---

## Median Performance Metrics

| Metric | Home | Category | Product |
|--------|------|----------|---------|
| **Performance Score** | **27** | **27** | **28** |
| First Contentful Paint (FCP) | 10.3s | 3.2s | 5.6s |
| Largest Contentful Paint (LCP) | 53.3s | 36.1s | 33.9s |
| Total Blocking Time (TBT) | 2,322ms | 2,671ms | 2,706ms |
| Cumulative Layout Shift (CLS) | 0 | 0.112 | 0 |
| Speed Index | 15.8s | 16.1s | 9.8s |
| Total Page Weight | 10.1MB | 38.5MB | 19.7MB |
| Image Bytes | 4.6MB | 10.5MB | 2.5MB |

### Individual Run Scores

| Page | Run 1 | Run 2 | Run 3 |
|------|-------|-------|-------|
| Home | 26 | 27 | 27 |
| Category | 27 | 27 | 31 |
| Product | 33 | 27 | 28 |

---

## Resource Breakdown

| Resource Type | Home | Category | Product |
|---------------|------|----------|---------|
| Total requests | 428 | 353 | 423 |
| Images | 135 req / 4.6MB | 160 req / 10.5MB | 123 req / 2.5MB |
| Scripts | 114 req / 3.8MB | 99 req / 2.8MB | 114 req / 3.0MB |
| Media (video) | 0 req / 0 | 2 req / 10.3MB | 2 req / 12.7MB |
| Stylesheets | 16 req / 214KB | 11 req / 103KB | 11 req / 102KB |
| Fonts | 6 req / 109KB | 4 req / 76KB | 4 req / 76KB |
| Third-party | 259 req / 5.1MB | 221 req / 3.9MB | 246 req / 3.9MB |

---

## Optimization Opportunities

### 1. Image Delivery (all pages, score=0)

Images are served as JPEG without modern format optimization. Potential savings per page:

**Home** (11 images flagged):
- AYA-91.jpg: 111.4KB potential savings
- shakti-dress-powder-pink: 110.0KB
- greek-goddess-dress: 63.6KB
- AYA-71: 60.6KB
- dusty-pink-braided-overalls: 53.6KB
- ...and 6 more images

**Category** (55 images flagged):
- off-white-tribal-goddess: 371.3KB
- off-white-tribal-goddess (variant): 306.5KB
- dusty-blue-ambrosia-set: 254.6KB
- warm-boho-hoody: 227.3KB
- maharani-blouse: 210.8KB
- ...and 50 more images

**Product** (11 images flagged):
- yellow-gold-boho-linen-set: 204.5KB
- black-boho-linen-set: 125.9KB
- japan-dress-african-brown: 109.2KB
- off-white-boho-linen-set: 109.1KB
- ...and 7 more images

### 2. Render-Blocking Resources (all pages, score=0)

Multiple render-blocking scripts and stylesheets, primarily from Shopify extensions (analyzify) and theme CSS.

- Category page: theme.css blocking for 737ms, analyzify extension blocking for 1,074ms
- Home/Product: analyzify extension scripts blocking up to 186ms

### 3. Unused JavaScript (all pages, score=0)

| Page | Potential Savings (KB) | Potential Savings (ms) |
|------|----------------------|----------------------|
| Home | 1,328KB | 420ms |
| Category | 1,222KB | - |
| Product | 1,276KB | 6,560ms |

### 4. Unused CSS (all pages, score=0)

| Page | Potential Savings (KB) | Potential Savings (ms) |
|------|----------------------|----------------------|
| Home | 55.9KB | 210ms |
| Category | 57.6KB | 370ms |
| Product | 55.7KB | 180ms |

### 5. Unminified JavaScript (all pages)

~25-28KB potential savings across all pages.

### 6. Cache Policy (all pages, score=0)

Third-party scripts with inefficient cache lifetimes:
- Poptin pixel.js: 69.8KB
- Facebook SDK: 85.5KB + 112.3KB config
- Poptin client: 500KB
- notify-me SDK: 43.9KB

### 7. JavaScript Execution Time (all pages, score=0)

| Page | Main Thread Work | JS Execution |
|------|-----------------|--------------|
| Home | 11.2s | 6.0s |
| Category | 29.2s | 6.2s |
| Product | 11.4s | 7.3s |

### 8. CLS Issue — Category Page

CLS = 0.112 on Category page (exceeds 0.1 "good" threshold). Other pages have 0 CLS.

### 9. Unsized Images (all pages)

1 image element without explicit width/height on each page.

### 10. Video/Media Weight

Category page loads 10.3MB of media (video), Product page loads 12.7MB. These are significant contributors to total page weight.

---

## Key Findings and Priorities

### Critical Issues (biggest impact)

1. **Enormous page weight** — Category page is 38.5MB, Product is 19.7MB, Home is 10.1MB. The primary culprits:
   - **Video/media**: 10-13MB on Category and Product pages
   - **Images**: 2.5-10.5MB across pages, served as JPEG without modern formats (WebP/AVIF)
   - **Third-party scripts**: 3.9-5.1MB across all pages

2. **LCP is catastrophically slow** — 33-53 seconds across all pages. This is driven by the massive payload and render-blocking resources.

3. **TBT is very high** — 2.3-2.7 seconds, caused by heavy JavaScript execution (6-7s of JS execution time).

### Recommended Focus Areas

1. **Image optimization** (highest ROI for this project):
   - Convert to WebP/AVIF via Shopify CDN parameters
   - Add proper responsive `srcset` with appropriate widths
   - Lazy-load offscreen images
   - Add explicit width/height to prevent CLS

2. **Video optimization**:
   - Lazy-load videos below the fold
   - Consider poster images instead of autoplay
   - Compress video assets

3. **Third-party script audit**:
   - Evaluate necessity of Poptin, notify-me, analyzify
   - Defer non-critical third-party scripts
   - Consider removing unused analytics/marketing pixels

4. **Render-blocking resources**:
   - Defer analyzify extension scripts
   - Async-load non-critical CSS
