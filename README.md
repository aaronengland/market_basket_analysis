# Market Basket Analysis

Association rule mining and network co-occurrence analysis on grocery transaction data, comparing two complementary approaches to discovering item relationships.

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
├── 03_association_rules/   FP-Growth frequent itemsets + association rules
├── 04_network_analysis/    Co-occurrence graph, centrality, community detection
├── 05_comparison/          Association rules vs network analysis comparison
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

### Association Rules (FP-Growth)
- FP-Growth algorithm mines frequent itemsets (min_support=0.005)
- Generates directional association rules filtered by lift > 1.0
- Key metrics: support, confidence, lift, leverage, conviction
- Answers: "If a customer buys A, how likely are they to buy B?"

### Network Analysis
- Builds item co-occurrence graph from basket matrix
- Nodes = items, edges = co-purchase frequency
- Computes centrality metrics (degree, betweenness, weighted degree)
- Louvain community detection identifies item clusters
- Answers: "Which items form natural groups? Which items are hubs?"

### Comparison
- Top items: rule frequency vs network centrality
- Rule strength (lift/confidence) vs edge weight (co-occurrence count)
- Methodology table: directionality, metrics, best use cases
- Association rules excel at product recommendations (directional)
- Network analysis excels at store layout and bundling strategy (structural)

## Key Metrics

**Association Rules:**
- **Support**: Proportion of transactions containing the itemset
- **Confidence**: P(consequent | antecedent)
- **Lift**: Confidence / expected confidence (lift > 1 = positive association)

**Network Analysis:**
- **Weighted Degree**: Total co-occurrence count for an item
- **Betweenness Centrality**: How often an item bridges different item groups
- **Community**: Cluster of items that co-occur more with each other than with outsiders
