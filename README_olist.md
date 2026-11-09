# Olist Operations Performance | Power BI

**Where and why are deliveries late, and what does it cost in customer satisfaction?**
A one-page Power BI report on 96,203 delivered orders from the Olist Brazilian e-commerce marketplace, January 2017 to August 2018.

**See the report:** [PDF export](Olist_Operations.pdf) | [2-minute video walkthrough](ADD-YOUR-VIDEO-LINK)

## Key findings

- **Late deliveries drive bad reviews.** Late orders average 2.27 stars against 4.29 for on-time orders, and 61% of late orders get a 1 or 2 star review, against 9% of on-time orders.
- **Delivery broke down twice.** On-time delivery fell to 87.6% after the November 2017 Black Friday spike, when orders rose 63% in a month, and to 81.0% in March 2018. It recovered to 95.5% in April.
- **Distance drives delivery time.** São Paulo (SP), with 42% of orders, gets deliveries in 8.7 days at 96% on time. Rio de Janeiro (RJ), the second-largest state, waits 15.2 days at 88%.
- **On-time % flatters performance.** Orders arrive about 12 days before the estimated date on average, so the report tracks actual days to deliver alongside on-time %.

## What I built

- **Power Query:** merged six CSV files into two clean tables, Orders and Items. Fixed review comments with embedded line breaks (3,852 rows), a UTF-8 byte-order mark in the category file, and two categories with no English translation, which keep their Portuguese names instead of being dropped. An inner join keeps only items from delivered orders.
- **Data model:** a small star schema with a marked Date table, Orders related one-to-many to Items with single-direction filtering.
- **DAX:** 3 calculated columns and 9 measures, including on-time %, average days to deliver, freight per order, seller share of orders, and month-over-month change.
- **Report:** KPI cards, a monthly trend of orders and on-time %, top 10 states, categories, and sellers, date and state slicers, and a definitions panel.

## Data model

`Date` (one) filters `Orders` (many), which filters `Items` (many). Order-level measures come from Orders; category and seller measures count distinct orders from Items.

## DAX highlights

```dax
On-time % = AVERAGE(Orders[On time])
```
On time is a 1/0 column, so its average is the share of orders delivered by the estimated date.

```dax
Seller share of orders =
DIVIDE(
    DISTINCTCOUNT(Items[order_id]),
    CALCULATE(DISTINCTCOUNT(Items[order_id]), ALL(Items[seller_id]))
)
```
ALL removes the seller filter from the denominator only, so each seller is divided by all sellers while date and state filters still apply.

```dax
Orders prior month = CALCULATE([Orders], DATEADD('Date'[Date], -1, MONTH))
```
DATEADD shifts the dates in view back one month for month-over-month comparisons.

## Data

[Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) on Kaggle. Prices are in Brazilian reais (R$). Delivered orders only.

## How to open

Download `Olist_Operations.pbix` and open it in Power BI Desktop (free, Windows). A PDF export of the report is also in this repository.

## Files

| File | What it is |
| --- | --- |
| `Olist_Operations.pbix` | The Power BI report |
| `Olist_Operations.pdf` | PDF export of the report |
