
# Retail Sales Analytics — Power BI

UK giftware retailer, 1M+ transaction rows ([Online Retail II](https://archive.ics.uci.edu/dataset/502/online+retail+ii), UCI).

**Built:** Power Query ETL → star schema (3 dimensions + fact) → 30 DAX measures → 3-page report

**Key finding:** Top 50 products account for only 20% of revenue. This is a long-tail catalogue, not an 80/20 business — range rationalisation would cut revenue rather than waste.

## Dashboard

![Dashboard](screenshots/dashboard.png)

<img width="685" height="386" alt="Screenshot 2026-09-24 170228" src="https://github.com/user-attachments/assets/a8d669ee-c68e-41ac-a3f7-6cda41b91ed8" />


<img width="686" height="386" alt="Screenshot 2026-09-24 170441" src="https://github.com/user-attachments/assets/eaa69f4a-1518-4663-a1cf-874fc59d4044" />


<img width="687" height="412" alt="Screenshot 2026-09-24 170555" src="https://github.com/user-attachments/assets/22ce4bdc-eb7a-4346-b79e-c997bc9f8918" />



## Data model

![Star schema](screenshots/model.png)

<img width="878" height="383" alt="Screenshot 2026-09-24 171059" src="https://github.com/user-attachments/assets/82db3348-e34c-47d8-ad87-5bd8f5e2c135" />


## Power Query pipeline

![Applied Steps](screenshots/power-query.png)


<img width="952" height="436" alt="Screenshot 2026-09-24 171303" src="https://github.com/user-attachments/assets/ceb1a2b5-de76-4ad1-b603-6f59a71798c9" />



## Selected measures

**Net Revenue** — returns are recorded as negative-quantity lines against the original invoice, so gross revenue overstates performance.

**ABC Classification** — buckets 4,600 products into A/B/C by cumulative revenue share. Uses a `>=` comparison rather than a sort, so each row computes its own position independently of table order.


**Tools:** Power Query (M) · DAX · star schema modelling · Power BI Desktop

Data: Chen, D. (2012). Online Retail II. UCI Machine Learning Repository. CC BY 4.0.

