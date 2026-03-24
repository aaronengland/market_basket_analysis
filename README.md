# Market Basket Analysis

Association rule mining on grocery transaction data, comparing the Apriori and FP-Growth algorithms.

## Dataset

**Groceries Dataset** from Kaggle (`heeraldedhia/groceries-dataset`) — ~38K grocery store transactions with item-level detail across ~4K members and ~170 unique items.

- **Source**: Kaggle (downloaded via `kagglehub`)
- **Format**: Parquet (stored in S3 bucket `market-basket-analysis-demo`)
- **Fields**: `member_id`, `transaction_date`, `item`

## Project Structure

```
market_basket_analysis/
├── 00_data_collection/     Download from Kaggle, clean, upload to S3
├── 01_eda/                 Item frequencies, basket sizes, temporal patterns
├── 02_preprocessing/       Build transaction lists, one-hot encode baskets
├── 03_apriori/             Apriori association rule mining
├── 04_fpgrowth/            FP-Growth association rule mining
├── 05_comparison/          Side-by-side algorithm comparison
├── requirements.txt
└── README.md
```

## Execution Order

Run notebooks sequentially: `00 → 01 → 02 → 03 → 04 → 05`

Each notebook reads from the previous stage's S3 output and writes results to `./output/`.

## Methodology

### Exploratory Data Analysis
- Item purchase frequency (top items, long-tail distribution)
- Basket size distribution (items per transaction)
- Transaction volume over time
- Member activity patterns
- Day-of-week purchasing patterns

### Preprocessing
- Group items by (member, date) to form transaction baskets
- Remove single-item transactions (no associations possible)
- One-hot encode using `TransactionEncoder` from mlxtend
- Compute item support for threshold selection

### Apriori Algorithm
- Classic level-wise candidate generation approach
- Mines frequent itemsets at configurable minimum support
- Generates association rules filtered by lift

### FP-Growth Algorithm
- Tree-based approach — builds FP-tree, then mines patterns
- Same minimum support and rule thresholds as Apriori for fair comparison
- Typically faster due to avoiding candidate generation

### Comparison
- Rule count and overlap between algorithms
- Metric distributions (support, confidence, lift)
- Top rules by lift — side-by-side
- Runtime comparison

## Key Metrics

- **Support**: Proportion of transactions containing the itemset
- **Confidence**: P(consequent | antecedent)
- **Lift**: Confidence / expected confidence (lift > 1 = positive association)
- **Leverage**: Difference between observed and expected co-occurrence
- **Conviction**: Measures rule dependency (higher = stronger)
