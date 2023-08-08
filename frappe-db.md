# Frappe Db

#### values statement
```
def results_to_values(iterable):
    return ",".join(str(d).replace(',)',')') for d in iterable)


# usage
from ec_dash.ec_dashboard.report.item_price_update_gc.item_price_update_gc import execute
columns, data = execute({"item_code":"E2012282"})

results_to_values((d['item_code'],d['supplier'].lower()) for d in data)

```

#### generate age
def get_ageing(filters, age_column):
    ageing = ["case", "else '{} +' end".format(filters.get("range3"))]
    low = 0
    for d in ["range1", "range2", "range3"]:
        days = filters.get(d)
        ageing.insert(
            -1,
            "when `{}` > DATE_SUB(%(from_date)s, INTERVAL {} DAY) then '{}-{}'".format(
                age_column, days + 1, low, days
            ),
        )
        low = days + 1
    return " ".join(ageing)

# usage
filters = {"from_date":"2021-02-01","range1" : 15, "range2" : 30, "range3": 60 }
get_ageing(filters)
