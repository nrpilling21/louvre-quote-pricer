# eFans made-to-order louvre pricer

Static single-page tool: price a made-to-order louvre or grille, see what each supplier
costs and what the job makes, and compare against Alpha Air and CVE Shop.

- `index.html` is the whole tool (fonts embedded, no build step, no network calls).
- Same look and design system as the louvre drawing tool.
- Deploy on Vercel; pushing to `main` redeploys.

## Where the numbers come from

**Systemair — confirmed.** `systemair-WL38-list-jan2026.csv` is Louise's WL38 price sheet,
transcribed. Her rules, applied in the tool:

    list  = sheet x 1.05          (July 2026 increase)
    paint = x 1.15 on top         (stocked RAL only)
    ours  = less our 20% trade discount

Verified to the penny against quote #D4584 — 310x310 RAL 8017 rounds up to the 350 band,
£47.85 x 1.05 x 1.15 = £57.78 list, less 20% = £46.22 nett. Both match what was quoted.
WL38 is not made over 1m x 1m; the tool says so and points at Louise for WL50.

**London Vents — estimated.** Fitted from 264 single-panel weather louvre quotes
(Jun 2025 – Sep 2026): `cost = 9.30 + 100.31 x area(m²) + 12.82 x perimeter(m)`, uplifted
to today. Leave-one-out mean error 15.7%, median 12.9%. Finish is included in their nett
price — no colour premium (mill £177/m² vs RAL £175/m² across 153 quotes).

**Similar Quotes** are 114 real line prices from Shopify draft orders, inc VAT.

## Known open questions

- Louise's sheet is annotated "less 45mm for overall size". The tool keys the matrix on the
  size you type. If the sheet is actually keyed on overall size, every price shifts by up to
  one band. **Confirm before this is used on customer quotes.**
- Quote #D4619 (1000x1000, non-stock RAL) was quoted at £225.68 list; the sheet gives
  £205.16. Unexplained — possibly priced as a WL50.
- The sheet header says mesh not included; Louise's covering email says "c/w Birdmesh".
- Carriage is a flat £35. Louise notes real carriage varies by postcode, quantity and size.

## Pricing rules baked in

- Margin fixed at 60% — measured from 17 supplier-quote/draft-order pairs matched on the
  #D reference. Stock items run at 33.6% (Shopify, 12 months); made-to-order is the higher one.
- Carriage is a pass-through, added after margin, never marked up. Verified against #D4610.
- All customer-facing figures inc VAT at 20%. Cost and profit are ex VAT.

⚠️ Internal tool. It shows cost base, margin, profit per job and real customer prices.
Do not put it on a public URL without protection.
