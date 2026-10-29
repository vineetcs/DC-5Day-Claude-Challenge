```text

Create a single-file HTML/CSS/JS application. The application must be entirely self-contained and run completely offline in a disconnected environment. Do not use any external CDNs, fonts, or libraries (no Chart.js, Bootstrap, or Tailwind). Use vanilla JavaScript and standard CSS.

**Application Purpose:**
A Quarterly Sales Commission Calculator designed to process uploaded CSV files, calculate performance against targets, and display performance dashboards.

**Data Inputs:**

1. **Sales Data (CSV Upload):** The app should accept a CSV file with the following exact columns: `Invoice No`, `Invoice Date`, `Sales Rep`, `Customer Name`, `Customer Type`, `Product Line`, and `Net Amount (AED)`.
2. **Target Data (CSV Upload or UI Input):** Provide a way to input quarterly targets per `Sales Rep` (e.g., a secondary CSV upload with columns `Sales Rep` and `Target Amount`, or a global target setting).

**Core Logic & Calculations:**
Parse the uploaded CSV(s) using vanilla JavaScript (e.g., `FileReader` API) and group the `Net Amount (AED)` by `Sales Rep`.
Compare the total quarterly sales per rep against their target to determine their commission tier:

* **Tier 1:** 100% to 119% of target achieved.
* **Tier 2:** 120% to 139% of target achieved.
* **Tier 3:** 140% of target achieved.
* **Tier 4:** Greater than 140% achieved.

For each sales rep, calculate:

1. Total Quarterly Sales (Sum of Net Amount).
2. Current achievement percentage against their target.
3. Their current commission tier.
4. The exact financial gap (in AED) required to reach the next tier.

**Dashboard & UI Requirements:**
Build a clean, modern interface using custom CSS. Include the following sections:

1. **Top 5 Achievers:** A visual leaderboard showing the top 5 reps by total sales volume.
2. **Individual Performance Table:** A data table listing all sales reps, their total sales, target, current tier, and the "Gap to Next Tier" (showing exactly how much more they need to sell to level up).
3. **Visual Progress Charts:** Since external charting libraries are forbidden, generate horizontal bar charts using HTML `<div>` elements and CSS widths (e.g., a progress bar filling up past 100% with markers for 120% and 140%).

Ensure the file parsing handles basic formatting issues (like commas in numbers or missing trailing spaces) and that the UI gracefully updates instantly when files are uploaded.

```