# 分析提示词：从上市公司财报还原其对私有公司股权持仓的精确价值

## Prompt（完整版）

---

You are a senior equity research analyst specializing in cross-holdings valuation and forensic accounting. Your task is to determine **exactly how many shares Company A (a public company) holds in Company B (a private company)**, and trace the precise fair value of that holding across every quarter, using only publicly available data.

## Context & Objective

Company A is publicly listed and holds equity in Company B, a private company. Company B has gone through multiple funding rounds (e.g., Series C, D, E, F, G...). Company A's holding is accounted for under **ASC 321** (Investments — Equity Securities without readily determinable fair value), meaning:

- The investment is initially recorded **at cost**
- The book value is adjusted only when there is an **"observable price change in orderly transactions for identical or similar investments"** — typically triggered by Company B's new funding rounds
- The adjustment formula is: **Shares held × New round's per-share price (PPS)**

Your goal is to produce:
1. The **exact number of shares** Company A holds in Company B
2. The **per-share price (PPS)** used at each ASC 321 adjustment event
3. A **complete quarterly tracking table** showing the book value of Company B's equity on Company A's balance sheet, for every quarter from initial investment to the most recent filing
4. **Cross-validation** against at least 3 independent data sources to confirm accuracy

---

## Step-by-Step Methodology

### Phase 1: Identify the Initial Investment

1. **Find the quarter when Company A first invested in Company B.** Look for:
   - Press releases announcing the investment
   - Earnings call transcripts mentioning the investment
   - The first quarter where "Purchases of strategic investments" in the cash flow statement shows a spike consistent with reported investment size

2. **Extract the initial investment amount** (e.g., $51M) from filings or press releases.

3. **Find the funding round's PPS** from one of these sources:
   - **Forge Global / Yahoo Finance** private company pages (e.g., `XXXX.PVT`) — these often show Certificate of Incorporation-level PPS data for each sub-class
   - **PitchBook, Crunchbase, or CB Insights** funding round databases
   - **SEC filings** (if any investors are mutual funds that must disclose holdings)

4. **Calculate shares held:** `Shares = Investment Amount ÷ PPS`

5. **CRITICAL: Verify that share count remains constant** in subsequent quarters. Check the cash flow statement for each subsequent period:
   - If "Purchases of strategic investments" shows amounts that align with other investments (not Company B), the share count is unchanged
   - If Company A participated in later rounds, you need to detect this and add the new shares

---

### Phase 2: Build the Quarterly Balance Sheet Tracking Table

For EVERY quarter from the quarter before Company A's investment to the most recent filing:

1. **Extract "Strategic Investments" (or equivalent line item) from the Condensed Consolidated Balance Sheet** in each quarterly press release or 10-Q filing.

2. **Extract from the Income Statement:**
   - "Gains (losses) on strategic investments, net" (or similar line item)

3. **Extract from the Cash Flow Statement:**
   - "Purchases of strategic investments"
   - "Proceeds from sales and maturities of strategic investments"

4. **Build the master table with columns:**

| Quarter | Date | Total Strategic Investments (BS) | Gains on Strategic Inv (IS) | Purchases (CF) | Proceeds (CF) |
|---------|------|--:|--:|--:|--:|

**Data source priority:** Always use quarterly press releases from Globenewswire/PR Newswire/Business Wire first. Fall back to SEC 10-Q/10-K filings only if press releases are incomplete.

**Important:** Some financial data (especially cash flow details) may only be reported on a semi-annual or 9-month cumulative basis in press releases. You may need to derive quarterly figures by subtraction (e.g., Q3 standalone = 9-month cumulative - H1 cumulative).

---

### Phase 3: Cross-Validation Using the Balance Sheet Identity

This is the **most powerful verification tool**. For every period where you have all three financial statements:

> **BS Change = Gains + Purchases - Proceeds**

Specifically:
- BS Change = Strategic Investments (end of period) - Strategic Investments (start of period)
- Gains = Cumulative gains on strategic investments for the period
- Purchases = Cumulative purchases for the period  
- Proceeds = Cumulative proceeds for the period

**If this equation does not balance to zero (or very close), your data has an error.** Go back and re-check. In our experience, this equation should match with **zero error** across all verifiable periods if the data is correctly extracted.

---

### Phase 4: Decompose Total Strategic Investments into Company B vs. Non-Company-B

Company A likely holds other strategic investments besides Company B. You need to separate them:

1. **For quarters with NO ASC 321 adjustment** (Company B PPS unchanged):
   - Company B book value = Shares × PPS from last adjustment
   - Non-Company-B = Total Strategic Investments - Company B book value

2. **For quarters WITH an ASC 321 adjustment** (new funding round):
   - New Company B book value = Shares × New PPS
   - Verify by checking: is the implied non-Company-B balance reasonable relative to prior quarters?

3. **Track non-Company-B balance separately** to ensure it follows a logical trajectory (gradual changes from other investments being bought/sold, not wild swings).

---

### Phase 5: Determine the Correct PPS for Each Funding Round

**THIS IS THE MOST CRITICAL AND NUANCED STEP.**

#### 5a: Understand Multi-Sub-Class Pricing

Private company funding rounds often have **multiple sub-classes with vastly different PPS**. For example, a single "Series E" round might include:

| Sub-class | PPS | Amount | Likely Investor Type |
|-----------|----:|-------:|---------------------|
| E-1 | $56.09 | $28.9B | Standard financial investors |
| E-2 | $20.93 | $16.1B | Strategic investors (e.g., cloud partners) with special terms |
| E-3 | $56.09 | $7.1B | Same as E-1 |
| E-4 | $50.48 | $7.7B | Similar to Company A's holding class |
| E-5 | $53.28 | $12.9B | Intermediate tier |

**The "headline" valuation / PPS reported in media is NOT necessarily what Company A uses.** Under ASC 321, Company A must use the PPS of the sub-class that is **"identical or similar"** to its own shares. This can be 10-15% lower than the headline price.

#### 5b: Determine Which Tranche Was Available at the Balance Sheet Date

ASC 321 uses **observable prices available on or before the balance sheet date**. If a funding round closes in multiple tranches:

- Tranche 1: Closes November 22 (BEFORE the Jan 31 balance sheet date) → Available
- Tranche 2: Closes March 3 (AFTER the Jan 31 balance sheet date) → NOT available

Company A can only use PPS from tranches that closed on or before its fiscal quarter-end date.

**You MUST know Company A's fiscal year-end** (e.g., January 31 for Zoom, not December 31) to correctly determine which tranches are available.

#### 5c: Back-Calculate PPS from the Balance Sheet

Use this formula:
```
Implied PPS = (Total Strategic Investments - Estimated Non-Company-B Balance) ÷ Shares Held
```

Then match this implied PPS against known sub-class PPS from Forge/PitchBook data. The match should be within 0.1% if your non-Company-B estimate is accurate.

#### 5d: Iterative Refinement

If the implied PPS doesn't match any known sub-class:
1. Re-examine your non-Company-B balance estimate
2. Check if you have the correct tranche timing
3. Verify the share count hasn't changed
4. Consider that Company A may have purchased or sold non-Company-B investments in that quarter

---

### Phase 6: Four-Way Cross-Validation

Validate your PPS findings against **four independent sources**:

1. **Company A's Balance Sheet** (back-calculated PPS as described above)
2. **Forge Global / Certificate of Incorporation data** (exact PPS per sub-class)
3. **Employee Tender Offers** (Company B's share buyback prices for employees — often matches one specific sub-class)
4. **Mutual Fund NAV marks** (e.g., ARK Venture Fund, Fidelity, T. Rowe Price — SEC-disclosed fair value marks on Company B shares)

| Round | BS-implied PPS | Forge Sub-class | Tender Offer | Fund NAV Mark | Match? |
|-------|---------------:|-----------------|-------------|--------------|--------|

All four sources should be **consistent** (though not identical — tender offers may use headline price, funds may mark at slight premium/discount).

---

### Phase 7: Analyze Dilution Mechanics

For each funding round, calculate:
- **Valuation growth** = New post-money ÷ Old post-money
- **PPS growth** = New PPS ÷ Old PPS  
- **Dilution factor** = Valuation growth ÷ PPS growth

This matters because: **Company A's holding value grows with PPS, NOT with headline valuation.** If Company B's valuation doubles but half the growth comes from issuing new shares, Company A's holding only increases ~1.5x, not 2x.

---

### Phase 8: Scenario Analysis

Calculate Company A's holding value under various Company B valuation scenarios:

```
Implied total shares = Latest post-money valuation ÷ Latest PPS
Company A value at target valuation = (Target valuation ÷ Implied total shares) × Shares held
```

**Caveat:** Future funding rounds will issue MORE shares, so actual PPS at a given future valuation will be LOWER than the simple ratio above. Apply a dilution haircut based on historical patterns (typically 10-50% depending on capital intensity).

---

## Common Pitfalls to Avoid

1. **Using "headline valuation ÷ total shares" as PPS** — This gives a weighted average, not the actual PPS of any specific sub-class. Company A uses one specific sub-class.

2. **Ignoring fiscal year-end dates** — Many companies don't use calendar year-end. Zoom's FY ends January 31. This is critical for determining which funding tranches are "observable" at the balance sheet date.

3. **Assuming all shares in a round have the same PPS** — They don't. Sub-classes can have 12x PPS differences within the same round (e.g., $20.93 to $259.14 in the same Series G).

4. **Forgetting about non-target investments** — "Strategic Investments" on the balance sheet includes ALL strategic equity holdings, not just Company B. You must estimate and track the non-Company-B portion separately.

5. **Confusing cumulative and quarterly financial data** — Press releases often report cumulative YTD figures for income statement and cash flow items. Derive quarterly standalone figures by subtraction.

6. **Assuming the investment amount is precisely round** — The actual amount may differ slightly from press reports. Use the exact figure from the cash flow statement.

7. **Not verifying with the balance sheet identity** — The equation `BS Change = Gains + Purchases - Proceeds` must hold with zero error. If it doesn't, your data extraction has a mistake.

8. **Confusing pre-money and post-money valuations** — Tender offers often reference pre-money valuations. Funding rounds are typically quoted as post-money. PPS = Post-money valuation ÷ Total shares (including newly issued ones).

9. **Ignoring "strategic discount" sub-classes** — Large tech companies (Amazon, Google) often get special pricing that is dramatically below headline PPS. These sub-classes should NOT be used for Company A's valuation unless Company A is one of these strategic investors.

10. **Not checking for share count changes** — Verify through cash flow data that Company A didn't participate in subsequent rounds. If "Purchases" appear in a quarter that coincides with a new round, investigate whether they bought Company B shares or other investments.

---

## Output Format

Please produce the following deliverables:

### Deliverable 1: Share Count Determination
- Initial investment amount, PPS, and calculated share count
- Evidence that share count has/hasn't changed in subsequent periods

### Deliverable 2: Complete Quarterly Tracking Table

| Quarter | Date | Total Strategic Inv | Company B PPS | Company B Value | Non-Company-B | ASC 321 Event |
|---------|------|-------------------:|-------------:|-----------------:|---------------:|---------------|

### Deliverable 3: Balance Sheet Identity Verification

| Period | BS Change | Calculated (Gains + Purchases - Proceeds) | Error |
|--------|----------:|------------------------------------------:|------:|

### Deliverable 4: PPS Cross-Validation Matrix

| Round | BS-Implied PPS | Forge Data | Tender Offer | Fund Mark | Best Match Sub-class |
|-------|---------------:|-----------:|-------------:|----------:|---------------------|

### Deliverable 5: Dilution Analysis

| Period | Valuation Growth | PPS Growth | Dilution Factor |
|--------|----------------:|----------:|-----------------:|

### Deliverable 6: Scenario Analysis

| Company B Valuation | Implied PPS | Company A Holding Value | % of Company A Market Cap |
|--------------------:|------------:|------------------------:|--------------------------:|

---

## Key Data Sources (Priority Order)

1. **Company A quarterly press releases** (Globenewswire/PR Newswire) — Primary source for all financial data
2. **SEC EDGAR** (10-Q, 10-K filings) — For detailed footnotes and quarterly breakdowns not in press releases
3. **Forge Global / Yahoo Finance** (`XXXX.PVT` pages) — For private company sub-class PPS from Certificate of Incorporation
4. **PitchBook / Crunchbase** — For funding round timelines, amounts, and investor lists
5. **News sources** (The Information, Bloomberg, TechCrunch) — For tender offer prices, round details, and timing
6. **SEC mutual fund filings** — For institutional fair value marks (ARK, Fidelity, T. Rowe Price)
7. **Company B's own announcements** — For round close dates and high-level terms

---

## Example Application

To illustrate: When analyzing Zoom's (ZM) holding of Anthropic shares:

- **Initial investment:** $51M in Series C at $11.2261/share → 4,542,985 shares
- **Key finding:** Anthropic's Series E had 5 sub-classes with PPS ranging from $20.93 to $56.09. Zoom used E-4 ($50.4779), NOT the headline E-1 ($56.09), because E-4 was the sub-class most similar to Zoom's existing preferred shares, and only the November 2024 tranche (not the March 2025 tranche) was available at Zoom's January 31 balance sheet date.
- **Validation:** All 5 balance sheet identity checks produced zero-error matches. All 5 PPS values matched Forge sub-class data within 0.05%.
- **Result:** Zoom's Anthropic holding grew from $51M to $1,177.3M (23.1x) over ~2.7 years, tracked across 12 quarters with full auditability.

---

*This methodology was developed and validated through four iterative analysis sessions on Zoom's Anthropic holdings, achieving zero-error cross-validation across all verifiable periods and sub-0.1% PPS matching against Forge Global Certificate of Incorporation data.*
