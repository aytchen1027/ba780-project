# Health vs. Price: How to Be a Smart Grocery Shopper

**What Open Food Facts and Open Prices reveal about U.S. packaged groceries**

BA780 · Cohort B · Team 2 (B02) · Final deliverable

> **The belief we tested:** "Eating healthy is expensive."
> **What we found:** in the stores we could measure, it mostly isn't. Shoppers overpay for brands, packaging and energy-dense treats, not for nutrition.

---

## Overview

Budget-conscious shoppers are often told that healthy food costs more, so many stop trying. This project tests that claim with open data. We matched **988,445 U.S. products** from [Open Food Facts](https://huggingface.co/datasets/openfoodfacts/product-database) with **39,725 shelf prices** from [Open Prices](https://huggingface.co/datasets/openfoodfacts/open-prices), and compared prices **per 100 g within the same aisle**, which is how a shopper actually chooses between products.

The result is a short set of "smart-shopper" rules and a prototype of a swap-recommendation engine for a gamified app (SmartCart).

**Audience:** everyday grocery shoppers, especially those on a budget.

## Key findings

| Finding | Result |
|---|---|
| Healthy vs. less healthy, same aisle | Nutri-Score A/B products cost **0.83×** D/E products per 100 g (about 17% less). No aisle shows a significant health premium. |
| Cheapest aisles | Legumes, vegetables, milk and yogurt, plain cereals and fruit cost about **$1 per 100 g**. Chocolate, biscuits, nuts and cheese cost $2.30–6. |
| Store brands vs. national brands | **0.58×** the price per 100 g in the same aisle, with more Nutri-Score A/B and fewer ultra-processed products. Part of the gap is bigger pack sizes. |
| Same product, different store | Median gap of **14%** between the cheapest and priciest store for the same barcode. |
| Greener choice | Low-impact (Green-Score A/B) products are about 18 pp more likely to be Nutri-Score A/B and cost **0.93×** per 100 g. Rice/grains and eggs are exceptions. |
| Aisle vs. brand | The aisle explains more of a product's Nutri-Score than the brand does (R² 0.40 vs 0.29). |
| Organic | About **+18%** per package vs. non-organic in the same aisle; the premium ranges from small (sauces) to large (fats, milk and yogurt). |
| Promotions | Ultra-processed products look heavily promoted, but most of that is explained by the aisle. Within the same store and aisle the gap is only about +2 pp. |
| Swap prototype | For **89%** of Nutri-Score D/E products, the prototype finds a healthier, cheaper, similar product in the same aisle. |

## The smart-shopper playbook

1. **Compare within the aisle.**
2. **Read the price per 100 g**, not the package price.
3. **Swap down a grade, not up in price.**
4. **Buy plain staples;** pay for quality only in treats.
5. **Choose the greener option** (in most aisles).
6. **Try the store brand first.**
7. **Know your store.**
8. **Don't follow sale tags into the snack aisle.**

## Hypotheses tested

| Theme | # | Hypothesis |
|---|---|---|
| 1. Know your aisle | H1 | The aisle explains more of Nutri-Score than the brand |
| | H2 | Aisles with more ultra-processed products have fewer Nutri-Score A/B products |
| 2. The greener choice | H3 | Within an aisle, low-impact products are more often Nutri-Score A/B |
| 3. Healthy doesn't cost more | H4 | Within an aisle, Nutri-Score A/B products cost more per 100 g than D/E |
| 4. What shoppers pay for | H5 | Less-processed (NOVA 1–3) products cost more than ultra-processed ones |
| | H6 | Organic products cost more but are not healthier |
| 5. Shop the store | H7 | Store brands cost less per 100 g with no worse Nutri-Score |
| | H8 | Ultra-processed products are on promotion more often, even in the same store and aisle |

## Data

| Dataset | Source | File used |
|---|---|---|
| Open Food Facts product database (U.S.) | [Hugging Face](https://huggingface.co/datasets/openfoodfacts/product-database) | `off_products_us.csv` |
| Open Prices (U.S. stores) | [Hugging Face](https://huggingface.co/datasets/openfoodfacts/open-prices) | `off_prices_us.csv` |
| Open Food Facts food-group taxonomy | [GitHub](https://github.com/openfoodfacts/openfoodfacts-server/tree/main/taxonomies) | `food_groups.json` |

All three are open data under [ODbL 1.0](https://opendatacommons.org/licenses/odbl/1-0/). The two CSVs were rebuilt with the companion data-pull notebook, `B02-Data-Pull-v2.ipynb`.

**Quality metrics and measures used:** Nutri-Score (A–E), NOVA processing group (1–4), Green-Score (A–F), price per 100 g, and food-group-adjusted price indices.

## Methodology

- **Cleaning:** barcodes padded to 13 digits; placeholder values set to missing; impossible nutrient values removed; package sizes limited to 20 g–5 kg; prices limited to USD barcode items, deduplicated, using the **regular (pre-discount) price**; non-grocery items dropped.
- **Joining:** products and prices matched on barcode (86% of priced barcodes match a product), then converted to price per 100 g. About 5,750 foods end up with both a food group and a price per 100 g.
- **Comparisons:** always made **within the same food group**. Because prices are skewed, groups are compared with a **ratio of geometric means** (Welch t-test on log prices, 95% CI). Multiple-comparison p-values are adjusted with Benjamini–Hochberg FDR.
- **Other methods:** R² (aisle vs. brand), Spearman correlation, fixed-effects style regression for promotions (same store and aisle).

## Repository contents

```
.
├── B02-Health-Vs-Price-US-Packaged-Groceries-Final.ipynb   # Main analysis notebook
├── B02-Data-Pull-v2.ipynb                                   # Data download / build (companion)
└── README.md
```

The notebook is organised as: Executive summary → Problem and hypotheses → Data and cleaning → Themes 1–5 → Playbook → Risks → Limitations → Challenges → Next steps (swap app prototype) → References → GenAI disclosure.

## Getting started

### Requirements

- Python 3.10+
- `numpy`, `pandas`, `matplotlib`, `seaborn`, `scipy` (1.11+ for `false_discovery_control`)
- Jupyter or Google Colab

```bash
pip install numpy pandas matplotlib seaborn scipy jupyter
```

### Running the notebook

1. Run `B02-Data-Pull-v2.ipynb` (or obtain the files listed under [Data](#data)) to produce `off_products_us.csv`, `off_prices_us.csv` and `food_groups.json`.
2. Open `B02-Health-Vs-Price-US-Packaged-Groceries-Final.ipynb`.
3. **Update the data path.** The notebook was written for Google Colab and mounts Google Drive:

   ```python
   from google.colab import drive
   drive.mount("/content/drive")
   path = "/content/drive/MyDrive/BA780 Team 2/Codebase/input_data/"
   ```

   To run locally, remove the Drive mount and set `path` to the folder containing your three input files.
4. Run all cells.

> Note: Hugging Face may rate-limit (HTTP 429) if the parquet files are streamed repeatedly. The data-pull notebook downloads them once and reads them locally.

## SmartCart: swap-engine prototype

The final section prototypes the logic of a gamified phone app. For a scanned product it:

1. Shows price per 100 g relative to the aisle median (e.g. "1.2× the aisle average").
2. Finds **similar, healthier and cheaper** products in the same aisle (category-tag similarity ≥ 0.5).
3. Shows the cheapest store where the product has been seen.
4. Awards points (10 per Nutri-Score grade gained, plus 1 per 2% saved).

Example: Cinnamon Toast Crunch ($2.05 per 100 g, 1.2× the cereal median) → Post Shredded Wheat (A, 33% cheaper) or Fiber One (B, 26% cheaper).

## Limitations

- **Local, concentrated prices:** 85% of price observations come from California and 93% from one contributor. Results describe these (mostly Bay Area) stores, **not national U.S. prices**.
- **Small priced sample:** only 0.6% of the catalog has both a food group and a price per 100 g.
- **Coarse matching:** food groups still mix different products, so part of a gap may reflect product mix.
- **Approximate flags:** store brands are detected by brand name; organic by label/name text.
- **Not missing at random:** popular, well-documented products are more likely to have scores and prices.
- **Association, not causation.**

Further risks (large packs vs. food waste, Nutri-Score blind spots, stale prices, bad swaps, gaming and privacy) are discussed in the notebook.

## Next steps

- Add more price regions (the team is scanning Boston prices into Open Prices).
- Improve product matching using finer categories and names; compare price per serving as well as per 100 g.
- Pilot with a small group of shoppers to measure swap acceptance and savings.


## GenAI disclosure

The team used Claude, ChatGPT, Codex and Gemini as coding, debugging and writing assistants. All research questions, hypotheses, analytical choices and conclusions were decided by the team, and outputs were verified by re-running the notebook and reviewing results. The full disclosure is in the notebook.

## References

- Open Food Facts. *Product database*. https://huggingface.co/datasets/openfoodfacts/product-database (ODbL 1.0)
- Open Food Facts. *Open Prices*. https://huggingface.co/datasets/openfoodfacts/open-prices (ODbL 1.0)
- Open Food Facts. *Food-group taxonomy*. https://github.com/openfoodfacts/openfoodfacts-server/tree/main/taxonomies
- Santé publique France. *Nutri-Score*. https://www.santepubliquefrance.fr/en/nutri-score
- Monteiro, C. A., et al. (2019). Ultra-processed foods: what they are and how to identify them. *Public Health Nutrition*, 22(5), 936–941.
- Open Food Facts. *Green-Score*. https://world.openfoodfacts.org/green-score
- Benjamini, Y., & Hochberg, Y. (1995). Controlling the false discovery rate. *JRSS B*, 57(1), 289–300.

## License

Data is © Open Food Facts contributors under ODbL 1.0. Add a license for the code here (e.g. MIT) before publishing.
