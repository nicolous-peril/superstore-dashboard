# Superstore Profitability Analysis Dashboard

### Objectives:
* To identify where the **business is making money**, **where it is losing profit**, and what **actions can improve profitability**.
* To showcase all findings/insights into an **easy-to-understand dashboard**

---

### Install libraries:


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
```

### Load the dataset:


```python
df = pd.read_csv('../data/SampleSuperstore.csv')
```


```python
df.shape
```

The dataset has 9,994 rows and 13 columns.

---

### Initial dataset inspections:


```python
df.head()
```


```python
df.info()
```

The dataset has both categorical and numerical values.


```python
df.describe().round(2)
```

**Insights:**
* Sales and profit distributions are highly skewed. Average sales are around \$230, while the maximum sale exceeds \$22,000. This indicates the presence of large outliers transactions.
* Average profit is \$28.66, but the minimum profit reaches -\$6,599.98. This suggests some products, discounts or regions maybe generating significant losses that require deeper investigation.

---

### Data validation:


```python
# Check missing values
df.isnull().sum()
```


```python
# Check duplicate rows/values
df.duplicated().sum()
```

**Insights:**
* The dataset has no missing values across all key business and sales-related fields.
* There are 17 duplicate records identified out of nearly 10,000 rows.


```python
# Show duplicate rows
df[df.duplicated(keep=False)].sort_values(by=list(df.columns))
```

**Insight:**
17 fully identical rows are found in the dataset, which is only around 0.17% of the total data. Since the dataset doesn’t include a unique transaction or order ID, I decided to **keep them** because they could still represent **legitimate repeated purchases**. Also, the number of duplicates is too small to significantly affect the overall analysis and insights.

---

### Basic Business Metrics:
Calculating high-level business KPIs to understand the company’s overall sales, profitability, and transaction volume.

**Questions Being Answered:**
- How much total sales did the company generate?
- How much total profit was earned?
- What is the overall profit margin?
- How many products were sold?


```python
total_sales = df["Sales"].sum().round(2)
total_profit = df["Profit"].sum().round(2)
profit_margin = total_profit / total_sales
total_quantity = df["Quantity"].sum().round(2)

print(f"Total sales: {total_sales}")
print(f"Total profit: {total_profit}")
print(f"Profit margin: {profit_margin}")
print(f"Total quantity: {total_quantity}")
```

Despite generating over \$2.29M in sales, the company achieved only a 12.47% profit margin, suggesting that strong revenue does not always translate into high profitability.

---

### Profit by Region:
Analyzing profitability across geographic regions to identify the strongest and weakest performing areas.

**Questions Being Answered:**
- Which regions generate the highest profit?
- Which regions underperform financially?
- Are there geographic differences in profitability?


```python
df.groupby("Region")["Profit"].sum().round(2).sort_values(ascending=False)
```

The West region generated the highest profit, while the Central region significantly underperformed, highlighting notable geographic differences in profitability across the business.

---

### Profit by Category:
Evaluating profitability across major product categories to determine which categories contribute most to business success.

**Questions Being Answered:**
- Which product categories are most profitable?
- Which categories generate weaker returns?
- Where should the business focus growth efforts?



```python
df.groupby("Category")["Profit"].sum().round(2).sort_values(ascending=False)
```

Technology and Office Supplies were the strongest profit-driving categories, while Furniture generated significantly lower returns despite likely contributing substantial sales volume.

---

### Profit by Sub-category:
Drilling deeper into product performance to identify specific sub-categories driving profit or causing losses.

**Questions Being Answered:**
- Which sub-categories are the most profitable?
- Which sub-categories are losing money?
- What specific product groups require business attention?



```python
df.groupby("Sub-Category")["Profit"].sum().round(2).sort_values()
```

Tables, Bookcases, and Supplies were the only sub-categories generating losses, while Copiers and Phones emerged as the strongest profit contributors, revealing clear opportunities for product-level optimization.

---

### Discount and Profit Relationship:
Analyzing the relationship between discounts and profitability to determine whether aggressive discounting impacts business performance.

**Questions Being Answered:**
- Do higher discounts reduce profit?
- Is there a relationship between discounting and financial losses?
- Should the business review its discount strategy?



```python
df[["Discount", "Profit"]].corr()
```

There is a noticeable negative relationship between discounts and profit, suggesting that higher discount levels may contribute to reduced profitability and potential financial losses.

---

### Create New Fields:
Creating calculated fields and business-friendly categories that will improve analysis and dashboard interactivity in Tableau.

**Questions Being Answered:**
- How can we simplify the analysis for business users?
- Which calculated metrics can improve reporting?
- How can we better categorize discount and profit behavior?


```python
df["Postal Code"] = df["Postal Code"].astype(str)
df["Profit Margin"] = df["Profit"] / df["Sales"]

df["Profit Status"] = df["Profit"].apply(
    lambda x: "Profit" if x >= 0 else "Loss"
)

def discount_level(discount):
    if discount == 0:
        return "No Discount"
    elif discount <= 0.2:
        return "Low Discount"
    elif discount <= 0.4:
        return "Medium Discount"
    else:
        return "High Discount"

df["Discount Level"] = df["Discount"].apply(discount_level)

df.head()
```

Creating calculated fields such as **Profit Margin**, **Profit Status**, and **Discount Level** improves the clarity of analysis, simplifies business reporting, and enables more interactive and meaningful Tableau visualizations.

---

### Export Clean Dataset for Tableau:
Exporting the cleaned and transformed dataset for use in Tableau dashboard development.


```python
df.to_csv("../data/cleaned_superstore.csv", index=False)
```
