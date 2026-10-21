
# Retail Sales Analytics — Power BI

UK giftware retailer, 1M+ transaction rows ([Online Retail II](https://archive.ics.uci.edu/dataset/502/online+retail+ii), UCI).

**Built:** Power Query ETL → star schema (3 dimensions + fact) → 30 DAX measures → 3-page report

**Key finding:** Top 50 products account for only 20% of revenue. This is a long-tail catalogue, not an 80/20 business — range rationalisation would cut revenue rather than waste.

## Dashboard




### Overview
<img src="Retail.png" width="900" alt="Retail page">

### Customer
<img src="Customer.png" width="900" alt="Customer page">











## Data model



<img width="878" height="383" alt="Screenshot 2026-09-24 171059" src="https://github.com/user-attachments/assets/82db3348-e34c-47d8-ad87-5bd8f5e2c135" />


## Power Query pipeline




<img width="952" height="436" alt="Screenshot 2026-09-24 171303" src="https://github.com/user-attachments/assets/ceb1a2b5-de76-4ad1-b603-6f59a71798c9" />



## Selected measures

**Net Revenue** — returns are recorded as negative-quantity lines against the original invoice, so gross revenue overstates performance.

**ABC Classification** — buckets 4,600 products into A/B/C by cumulative revenue share. Uses a `>=` comparison rather than a sort, so each row computes its own position independently of table order.


**Tools:** Power Query (M) · DAX · star schema modelling · Power BI Desktop

Data: Chen, D. (2012). Online Retail II. UCI Machine Learning Repository. CC BY 4.0.

