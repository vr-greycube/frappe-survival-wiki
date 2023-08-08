# Pandas

```
# Below are some quick examples

# Using Dataframe.apply() to apply function add column
def add_3(x):
   return x+3
df2 = df.apply(add_3)

# Using apply function single column
def add_4(x):
   return x+4
df["B"] = df["B"].apply(add_4)

# Apply to multiple columns
df[['A','B']] = df[['A','B']].apply(add_3)

# Apply a lambda function to each column
df2 = df.apply(lambda x : x + 10)

# Using Dataframe.apply() and lambda function
df["A"] = df["A"].apply(lambda x: x-2)

# Using Dataframe.apply() & [] operator
df['A'] = df['A'].apply(np.square)

# Using numpy.square() and [] operator
df['A'] = np.square(df['A'])

# Apply function NumPy.square() to square the values of two rows
#'A'and'B
df2 = df.apply(lambda x: np.square(x) if x.name in ['A','B'] else x)

# Apply function single column using transform()
def add_2(x):
    return x+2
df = df.transform(add_2)

# Using DataFrame.map() to Single Column
df['A'] = df['A'].map(lambda A: A/2.)

# Using DataFrame.assign() and Lambda
df2 = df.assign(B=lambda df: df.B/2)
```

#### script report with pandas

```
import pandas as pd
data = frappe.db.sql("""
select *
from (
    select ROW_NUMBER()
        OVER(PARTITION BY tip.item_code , tip.price_list ORDER BY tip.price_list_rate) rn ,
    tip.item_code , tip.price_list , tip.currency , tip.modified , ts.country ,
        ti.manufacturer_pn , ti.item_name ,
        tip.price_list_rate
        * IF(currency = 'EUR', 1.1126, 1)
        * (case TIMESTAMPDIFF(YEAR, tip.modified, curdate())
            when 2 then 1.04 * 1.04
            when 1 then 1.04
            else 1 end)  price_list_rate
    from `tabItem Price` tip
    inner join tabItem ti on ti.item_code = tip.item_code
    left outer join tabSupplier ts on ts.default_price_list = tip.price_list
    where tip.modified > DATE_SUB(CURDATE(), INTERVAL 3 YEAR)
        and buying = 1
) t
where rn = 1""", as_dict=True)

print(data[:1])
dfa = pd.DataFrame.from_records(data)


def apply_shipping(country, rate):
    if country in ('United States' , 'Mexico', 'Canada'):
        return rate * 1.03
    return rate * 1.07

dfa["price_list_rate"] = dfa.apply(lambda x: apply_shipping(x['country'], x['price_list_rate']), axis=1)

competitor_data = frappe.db.sql("""
    select
        tip.item_code ,
        min(tip.price_list_rate
        * IF(currency = 'EUR', 1.1126, 1)
        * (case TIMESTAMPDIFF(YEAR, tip.modified, curdate())
            when 2 then 1.04 * 1.04
            when 1 then 1.04
            else 1 end)) competitor_rate
    from `tabItem Price` tip
    where tip.modified > DATE_SUB(CURDATE(), INTERVAL 3 YEAR)
        and competitor = 1
    group by item_code
""", as_dict=True)

print(competitor_data[:1])

dfb = pd.DataFrame.from_records(competitor_data)

df = pd.merge(dfa, dfb, on="item_code", how="left")


config = frappe.db.get_value(
"Item Price Configuration", "Item Price Configuration" ,
("sale_price_small_customer",
"sale_price_medium_customer",
"sale_price_large_customer",
"margin_if_no_competitor",), as_dict=True
    )
config = frappe._dict({k: frappe.utils.flt(v)/100 for k,v in config.items()})

df.loc[df["competitor_rate"].isna(),["competitor_rate"]] = df["price_list_rate"]/(1-config.margin_if_no_competitor)

df["range"] = df["competitor_rate"] - df["price_list_rate"]
df["range_percentage"] = df["range"] / df["competitor_rate"]

df.loc[df["range_percentage"] > 0.15, ["bronze"]] = df["price_list_rate"] + (config.sale_price_small_customer * df["range"])
df.loc[df["range_percentage"] > 0.15, ["silver"]] = df["price_list_rate"] + (config.sale_price_medium_customer * df["range"])
df.loc[df["range_percentage"] > 0.15, ["gold"]] = df["price_list_rate"] + (config.sale_price_large_customer * df["range"])


df.loc[df["range_percentage"] < 0.15, ["bronze"]] = df["price_list_rate"]/config.sale_price_small_customer
df.loc[df["range_percentage"] < 0.15, ["silver"]] = df["price_list_rate"]/config.sale_price_medium_customer
df.loc[df["range_percentage"] < 0.15, ["gold"]] = df["price_list_rate"]/config.sale_price_large_customer

df["bronze-margin"] = ( (df["bronze"] - df["price_list_rate"]) / df["bronze"] ) * 100
df["silver-margin"] = ( (df["silver"] - df["price_list_rate"]) / df["silver"] ) * 100
df["gold-margin"] = ( (df["gold"] - df["price_list_rate"]) / df["gold"] ) * 100

df = df.round(2)

df = df[['item_code','item_name','bronze' , 'bronze-margin']]

df = df[:3]

df.to_dict(orient='split')['data']

```

#### add a columns to dataframe

```
# Example 1: Add Column using arithmetic operation based on existing column
df["Final_Fee"] = df["Fee"] - df["Discount"]

# Example 2: Add New Column using assign()
df = pd.DataFrame(technologies)
df1 = df.assign(Discount_Percent=lambda x: x.Fee * x.Discount / 100)

# Example 3: Add column using np.where()
df['Discount_rating'] = np.where(df['Discount'] > 2000, 'Good', 'Bad')

# Example 4: Add column using apply()
df['Final_fee'] = df.apply(lambda x: x['Fee'] - x['Discount'], axis=1)`

# Example 5: Add column to DataFrame using loc[]
df['Without_discount'] = df.loc[:,['Fee', 'Discount']].sum(axis=1)

# apply and lambda
def _apply_tier_margin(tier, row):
    return row[tier] and 100 * (row[tier] - row["price_list_rate"]) / row[tier] or 0

for tier in ("gold", "silver", "bronze"):
    df[f"{tier}_margin"] = df.apply(lambda x: _apply_tier_margin(tier, x), axis=1)

df = (df
        .assign(
                aov = lambda x: x['revenue'] / x['transactions'],
                conversion_rate = lambda x: x['transactions'] / x['sessions']
        )
)

# Define the function
def multiply(row):
    return row['column_1'] * row['column_2']
df['result'] = df.apply(multiply, axis=1)


```

#### filter dataframe

```
# Filter Rows Based on condition
df[df["Courses"] == 'Spark']
df.loc[df['Courses'] == value]
df.query("Courses == 'Spark'")
df.loc[df['Courses'] != 'Spark']
df.loc[df['Courses'].isin(values)]
df.loc[~df['Courses'].isin(values)]

# Filter Multiple Conditions using Multiple Columns
df.loc[(df['Discount'] >= 1000) & (df['Discount'] <= 2000)]
df.loc[(df['Discount'] >= 1200) & (df['Fee'] >= 23000 )]

# Using lambda function
df.apply(lambda row: row[df['Courses'].isin(['Spark','PySpark'])])

# Filter columns that have no None & nana values
df.dropna()

# Other examples
df[df['Courses'].str.contains("Spark")]
df[df['Courses'].str.lower().str.contains("spark")]
df[df['Courses'].str.startswith("P")]
```

[https://sparkbyexamples.com/pandas/pandas-filter-rows-by-conditions/?expand_article=1](https://sparkbyexamples.com/pandas/pandas-filter-rows-by-conditions/?expand_article=1)


