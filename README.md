# FX Profit Calculator

A tiny, elegant single-page web app for calculating FX profits on NGN⇄USD transfers. Enter the client’s naira amount, your provider’s NGN/USD rate, and your per-dollar margin. The app shows the USD delivered, effective rate, provider cost, your profit (₦), and profit % — with slick UI and instant updates.

## Features

* ✨ Polished, responsive UI (no frameworks yet)
* 🧮 Instant calculations as you type
* 🔤 “Amount in words” for NGN (e.g., `20,000,000 → Twenty Million Naira`)
* ⚡ Quick margin presets (₦3/₦5/₦10/₦15/₦20 per USD)
* 📌 Fee notice for transfers under \$10,000
* 🇳🇬 Locale-aware formatting (`en-NG`)

## Live demo / How to run

Just open `index.html` in any modern browser. No build step, no dependencies.

```
.
├── index.html   # everything in one file (HTML/CSS/JS)
```

## How it works (math)

Inputs:

* `A` = Client NGN amount
* `R_p` = Provider rate (NGN per USD)
* `M` = Your margin (NGN per USD)
* `F_usd` = Transfer fee in USD (default 25, see note below)
* `R_eff` = Effective client rate = `R_p + M`

Displayed outputs:

* **USD delivered**: `USD = (A - F_ngn) / R_eff`, where `F_ngn = F_usd * R_p`
* **Provider cost (₦)**: `Cost_ngn = USD * R_p`
* **Your profit (₦)**: `Profit_ngn = USD * M`
* **Profit %**: `Profit_pct = (Profit_ngn / Cost_ngn) * 100`

> Note: The current implementation **always deducts** the \$25 fee (converted to NGN) before computing USD, and only **shows** the fee notice when `USD < 10,000`. See “Known caveats” for a better fee rule.

## Usage

1. Type the client’s NGN amount (commas allowed).
2. Enter the provider NGN/USD rate.
3. Enter your per-USD margin or tap a quick preset.
4. Hit **Calculate Profit** (or just keep typing — it auto-updates).

## Assumptions

* Currency is NGN for inputs and costs; USD for delivered amount.
* Margin is **per USD** in NGN (not a percentage).
* “Amount in words” renders the **integer** NGN part only.

## Known caveats & suggested fixes

1. **Fee application logic**

   * Business rule says: “\$25 fee for transfers under \$10,000.”
   * Current code subtracts the fee **unconditionally** (then only shows a banner for `<$10k`).
   * **Recommendation:** Compute a preliminary `USD_pre = A / R_eff`.

     * If `USD_pre < 10000`, apply the fee: `USD = (A - F_ngn) / R_eff`.
     * Else, `USD = USD_pre` and fee = 0.
   * This avoids fee-driven circularity and matches the rule.

2. **Input validation**

   * Guard against zero/negative `R_p` or `M`. Show friendly inline errors.
   * Prevent `A < F_ngn` (would yield negative USD).

3. **Rounding**

   * Display is `toFixed(2)`; consider internal math with full precision and only round for UI.

4. **“Amount in words”**

   * Ignores decimals; that’s fine for NGN, but note it in UI.

## Customization

* **Change fee**: update `transferFeeUSD` in `calculateProfit()`.
* **Remove fee entirely**: set to `0` or add a checkbox to toggle.
* **Preset margins**: edit the quick buttons in the HTML.
* **Theme**: tweak the gradient in `body` and the button styles.
* **Copy/Share**: add a “Copy results” button using `navigator.clipboard.writeText`.

## Accessibility & UX

* Labels are associated with inputs; consider adding `aria-live` to results for screen readers.
* Add `inputmode="decimal"` for numeric fields on mobile.
* Consider inline error messages for invalid states (e.g., provider rate missing).

## Security

* Pure client-side — no data leaves the browser.
* If hosting, serve over HTTPS.

## Roadmap ideas (money & insights, Perre 😉)

* **Rate source integration**: auto-pull live NGN/USD from a provider; add a manual override.
* **What-if optimizer**: slider to maximize `M` while keeping USD delivered above a target or keeping client rate <= competitor’s.
* **Quotes/Receipts**: export a branded PDF for clients; include timestamp, rate, fee breakdown.
* **Deal tracker**: local storage of past quotes; compute average margin and total ₦ profit.
* **Widget/Embed**: drop-in snippet for WhatsApp Business mini-site or a Notion/portfolio page.
* **Referral flow**: embed affiliate links to your provider(s); track clicks.
* **PWA**: installable on phone with offline support.

## Contributing

PRs welcome. Keep it framework-free and lightweight. If you add dependencies, justify them in the PR.

## License
[MIT](./LICENSE) © 2025 Ebiperre Iyoro
