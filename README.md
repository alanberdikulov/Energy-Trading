# Energy Trading Notebook

## What’s inside
- **Market scope**: focuses on ERCOT congestion revenue rights (CRRs) versus nodal futures in order to spot synthetic arbitrage opportunities.
- **Single source of truth**: all logic lives in `Energy Trading.ipynb`, which documents the full pipeline with narrative Markdown plus executable Python cells.
- **Primary artifacts**: a merged trade-level dataset matching CRR auctions to synthetic futures spreads and a path-level summary table with gap statistics, liquidity metrics, and t‑scores used for ranking trades.

## Workflow covered in the notebook
1. **Exploration & triage**
   - Profile ~6M nodal futures rows and ~1.9M CRR auction rows.
   - Drop liquidity columns that are always zero and catalog node/TOU coverage.
2. **Data curation**
   - Filter futures to monthly `_month_on/off_rtp` contracts, parse node and on/off tags, and keep the last settlement per `(node, contract_month, TOU)`.
   - Normalize CRR start dates into delivery months, map TOU tags to ON/OFF, and keep only source/sink nodes that overlap with the futures universe.
3. **Synthetic spread construction**
   - Join CRRs to the latest source/sink futures marks, compute `synthetic = fut_sink – fut_source`, and measure `diff = CRR – synthetic`.
   - Track `gap_max_days` (staleness between futures marks and delivery start) for diagnostics.
4. **Aggregation & filtering**
   - Summaries by `(Source, Sink, TOU)` with counts, mean/std/|diff|, mean CRR vs synthetic prices, timing gaps, CRR MW, bidder counts, and futures open-interest stats.
   - Identify “cheap” and “rich” CRRs using thresholds on trade count (≥50) and |avg diff| (≥$2/MWh), then compute t-stats, capacity proxies, and sign persistence by year.
5. **Reporting helpers**
   - Export merged and summary CSVs in `processed_data/` for downstream dashboards.
   - Provide narrative cells that explain liquidity filters, gap analysis, and trade-direction scoring.

## Reproducing the results
1. Place the raw CSVs referenced in the notebook under `processed_data/` using the same filenames (`nodal_futures_stitched.csv`, `crr_auction_results_stitched.csv`, etc.).
2. Create a Python environment (3.10+) with `pandas`, `numpy`, and Jupyter (`pip install pandas numpy jupyter` or reuse the project’s `.venv`).
3. Launch Jupyter:
   ```powershell
   cd C:\Users\Alan\Desktop\BC
   jupyter notebook Energy Trading.ipynb
   ```
4. Run the cells sequentially. Outputs (CSV exports and console tables) will appear in `processed_data/`.

## Repo structure
- `Energy Trading.ipynb` – the full analysis notebook (only tracked file).
- External data, reports, or scratch artifacts stay untracked to keep the repo light; add them locally under `processed_data/` if you need to rerun the analysis.

Feel free to fork/clone the repo and adapt the notebook for other ISO nodes or alternative liquidity screens. Contributions are welcome through pull requests.

