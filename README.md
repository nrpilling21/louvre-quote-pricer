# eFans made-to-order louvre pricer

Static single-page tool: price a made-to-order louvre or grille, see what each supplier
costs and what the job makes, and compare against Alpha Air and CVE Shop.

- `index.html` is the whole tool (fonts embedded, no build step, no network calls).
- Same look and design system as the louvre drawing tool.
- Deploy on Vercel; pushing to `main` redeploys.

## Where the numbers come from

**Systemair — confirmed.** All five of Louise's January 2026 sheets are transcribed alongside
this file: WL38 weather louvre, NV1 non-vision, SD single deflection, DD double deflection and
ECG egg crate — 1,081 price cells. Her rules, applied in the tool:

    list  = sheet x 1.05          (July 2026 increase)
    paint = x 1.15 on top         (stocked RAL only)
    ours  = less our 20% trade discount

### Verification against real quotes

22 Systemair quotes were recovered from the mailbox (mostly from Trash — Louise's priced
replies are deleted rather than filed, so that is where the quote history lives). 18 were
in range and testable against the sheets. **10 reconcile exactly**, which confirms the rule:

| Ref | Series | Size | Finish | Rule | Result |
|---|---|---|---|---|---|
| D4584 | WL38 | 310x310 | RAL 8017 | x1.05 x1.15 | £57.78 exact |
| D4634 | NV1 | 600x250 | RAL 9005 | x1.05 x1.15 | £55.46 exact |
| D4630 | NV1 | 900x255 | RAL 9010 | x1.05 x1.15 | £86.60 exact |
| D4613 | NV1 | 800x800 | RAL 9010 | x1.05 x1.15 | £150.85 exact |
| D4601 | NV1 | 500x500 | RAL 9010 | x1.05 x1.15 | £73.38 exact |
| D4578 | NV1 | 1000x400 | RAL 7012 | x1.05 x1.15 | £100.36 exact |
| D4603 | NV1 | 640x330 | SAA | x1.05 | £62.50 exact |
| D4549 | NV1 | 800x250 | SAA | x1.05 | £62.67 exact |
| D4629 | DD | 1000x100 | std | x1.05 | £52.95 exact |
| D4565 | DD | 670x670 | SAA | x1.05 | £127.05 exact |
| D4519 | SD 32BS | 730x530 | SAA | x1.05 | £70.09 exact |

So WL38, NV1, DD and SD are all verified on live quotes, in both mill and painted form.
**SD 32BS is priced as plain SD** — no frame surcharge.

Nothing is made over 1m x 1m; the tool says so and points at Louise (WL50 for louvres).

**The four grille sheets are triangular** — only the larger x smaller combination is priced —
so the tool normalises the size before looking up. A 400x900 and a 900x400 grille return the
same price, which is correct. WL38 is a full grid and is not normalised, so it stays asymmetric
(400x900 = £80.42, 900x400 = £73.70).

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
### Variants that carry an uplift the sheets do not show

- **25F flat frame with paint prep is +10%.** Both D4531 lines land on exactly
  (sheet x 1.05) x 1.10 — "primed" £34.24 and "dipped & burned for painting" £35.93.
  The SD sheet's note says 25F is the same price, so the 10% is the prep, not the frame.
  **Not modelled in the tool.**
- **Ladder type appears to be priced in.** D4575 (NV1 500x800 SAA, our line says LADDER TYPE)
  came back 25% above the plain figure with no separate line for it. **Not modelled.**
- **NV4 is a different series** with no sheet — D4595 is 71% above NV1 for the same size.
  The tool prices NV1 only.

### Still unexplained — ask Louise

- **D4625** NV1 100x1000 antique brass: quoted £65.97, sheet gives £47.97 mill / £55.17 painted.
- **D4574** NV1 500x240 SAA: quoted £41.88, sheet gives £37.21.
- **D4506** ECG 225x520 standard white: quoted £35.73, sheet gives £32.48. **This is the only
  ECG quote in the mailbox, so the egg crate sheet is effectively unverified.**
- **D4600** NV1 950x950 RAL 9010: quoted £190.13 = sheet x 1.15 with no 5%. Looks like the July
  increase was missed on that quote rather than the sheet being wrong.
- **D4619** WL38 1000x1000 non-stock RAL: quoted £225.68, sheet gives £205.16.

### ECG extras not yet in the tool

45-degree core (painted only) +30%, channel frame same price, core only 2/3 price.
ECG and SD can be made 50mm high.
- The sheet header says mesh not included; Louise's covering email says "c/w Birdmesh".
- Carriage is a flat £35. Louise notes real carriage varies by postcode, quantity and size.

## Pricing rules baked in

- Margin fixed at 60% — measured from 17 supplier-quote/draft-order pairs matched on the
  #D reference. Stock items run at 33.6% (Shopify, 12 months); made-to-order is the higher one.
- **Carriage.** The customer always pays a flat £35. What the supplier charges *us* varies a lot,
  so it is folded into the cost base and recovered inside the product line:
  `sell = (goods + their carriage) / (1 - margin)`, then the product line is `sell - 35`.
  `supplier-carriage-observed.csv` holds the 54 quotes this was fitted from.

  Postcode turns out to be a weak driver — Aberdeen came back cheaper than central London,
  and the same postcode gets different figures on different jobs. What decides it is
  **parcel vs pallet**, set by longest dimension, total area and quantity:

  | Band | Systemair | London Vents |
  |---|---|---|
  | Parcel — max dim <=1050mm, <=1m2, qty <=6 | £22 | £45 |
  | Long parcel — <=1500mm, <=2m2 | £34 | £65 |
  | Pallet — <=2500mm, <=6m2 | £74 | £128 |
  | Multi-pallet | £166 | £150 |

  Systemair is consistent and says what it is doing ("CARRIAGE SINGLE PALLET £96.00") —
  median error £2, 19 of 23 within £15. London Vents quote a coarse ladder that tracks size
  loosely — median error £20, so treat theirs as an allowance rather than a prediction.

  At a flat £35 we were recovering carriage on only **19% of London Vents jobs** and 70% of
  Systemair ones. Across the 54 sampled quotes that is **£1,221 of unrecovered carriage**.

- **Supplier choice is made on landed cost, not goods.** London Vents carriage is dearer on
  18 of 22 head-to-head jobs, median £22. Including it flips the decision on most sizes —
  e.g. a 1000x1000 mill louvre is £3.24 cheaper from London Vents on goods but £19.76 dearer
  once carriage is in.
- All customer-facing figures inc VAT at 20%. Cost and profit are ex VAT.

⚠️ Internal tool. It shows cost base, margin, profit per job and real customer prices.
Do not put it on a public URL without protection.
