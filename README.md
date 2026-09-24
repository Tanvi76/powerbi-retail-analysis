Retail Sales Analytics — Power BI

UK giftware retailer, 1M+ transaction rows (Online Retail II, UCI).

Built: Power Query ETL → star schema (3 dimensions + fact) → 30 DAX measures → 3-page report

Key finding: Top 50 products account for only 20% of revenue. This is a long-tail catalogue, not an 80/20 business — range rationalisation would cut revenue rather than waste.









Selected measures

Net Revenue — returns are recorded as negative-quantity lines against the original invoice, so gross revenue overstates performance.

ABC Classification — buckets 4,600 products into A/B/C by cumulative revenue share. Uses a >= comparison rather than a sort, so each row computes its own position independently of table order.

Tools: Power Query (M) · DAX · star schema modelling · Power BI Desktop

Data: Chen, D. (2012). Online Retail II. UCI Machine Learning Repository. CC BY 4.0.
